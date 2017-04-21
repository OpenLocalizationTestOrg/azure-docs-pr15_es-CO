<properties
    pageTitle="Aplicación fácil instalación y administración en el lote de Azure | Microsoft Azure"
    description="Use la característica de paquetes de aplicación de Azure lote para administrar fácilmente varias versiones para su instalación en lotes y aplicaciones de los nodos de cálculo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Implementación de aplicaciones con paquetes de aplicaciones de proceso de Azure

La característica de paquetes de aplicaciones de Azure lote proporciona administración fácil de aplicaciones de la tarea y su implementación en los nodos de cálculo en el grupo. Con paquetes de aplicaciones, puede cargar y administrar varias versiones de las aplicaciones que se ejecuten las tareas, incluidos sus archivos auxiliares. Puede entonces automáticamente implementar uno o más de estas aplicaciones en los nodos de cálculo en el grupo.

En este artículo, aprenderá cómo cargar y administrar paquetes de aplicaciones en el portal de Azure. A continuación, aprenderá cómo instalarlos en los nodos de cálculo de la agrupación con el [Lote .NET] [ api_net] library.

> [AZURE.NOTE] La función de paquetes de aplicación descrita aquí reemplaza a la característica "Lote Apps" disponible en las versiones anteriores del servicio.

## <a name="application-package-requirements"></a>Requisitos del paquete de aplicación

Debe [vincular una cuenta de almacenamiento de Azure](#link-a-storage-account) a su cuenta de lote para utilizar los paquetes de aplicación.

La función de paquetes de aplicación descrita en este artículo es compatible *sólo* con los grupos de lotes que se crearon después de 10 de marzo de 2016. Paquetes de aplicaciones no se implementará para nodos de cálculo en grupos creados antes de esta fecha.

Esta característica se introdujo en [Lote REST API] [ api_rest] versión 2015-12-01.2.2 y correspondiente [Lote .NET] [ api_net] versión 3.1.0 de la biblioteca. Recomendamos que utilice siempre la versión más reciente de la API cuando se trabaja con lotes.

> [AZURE.IMPORTANT] Actualmente, sólo los grupos de *CloudServiceConfiguration* compatible con paquetes de aplicaciones. No puede utilizar paquetes de aplicaciones en grupos creados con imágenes de VirtualMachineConfiguration. Consulte la sección de [configuración de máquina Virtual](batch-linux-nodes.md#virtual-machine-configuration) de [Linux provisión nodos de cálculo en grupos de Azure lote](batch-linux-nodes.md) para obtener más información acerca de las dos configuraciones diferentes.

## <a name="about-applications-and-application-packages"></a>Acerca de aplicaciones y paquetes de aplicaciones

Dentro del lote de Azure, una *aplicación* hace referencia a un conjunto de archivos binarios con versiones que pueden descargarse automáticamente a los nodos de cálculo en el grupo. Un *paquete de aplicación* hace referencia a un *conjunto específico* de los datos binarios y representa una determinada *versión* de la aplicación.

![Diagrama de alto nivel de las aplicaciones y paquetes de aplicaciones][1]

### <a name="applications"></a>Aplicaciones

Una aplicación en lote contiene uno o más aplicaciones empaqueta y especifica las opciones de configuración de la aplicación. Por ejemplo, una aplicación puede especificar la versión del paquete de aplicación predeterminado para instalar en nodos de cómputo y si se pueden actualizar o eliminar sus paquetes.

### <a name="application-packages"></a>Paquetes de aplicaciones

Un paquete de aplicación es un archivo .zip que contiene los archivos binarios y archivos auxiliares que son necesarios para la ejecución por sus tareas. Cada paquete de aplicación representa una versión específica de la aplicación.

Puede especificar los paquetes de aplicaciones en el nivel de grupo y tarea. Puede especificar uno o varios de estos paquetes y (opcionalmente) una versión cuando se crea un grupo o una tarea.

* **Paquetes de grupo de aplicaciones** se implementan en *todos* los nodos en el grupo. Las aplicaciones se implementan cuando un nodo une a un grupo, y cuando se reinicia o se rehace su imagen.

    Paquetes de aplicaciones de grupo son apropiados cuando todos los nodos en un grupo de ejecutan las tareas de un trabajo. Puede especificar uno o más paquetes de aplicación cuando se crea un grupo, y puede agregar o actualizar paquetes de un grupo existente. Si actualiza los paquetes de aplicaciones de un grupo existente, debe reiniciar sus nodos para instalar el nuevo paquete.

* **Paquetes de aplicaciones de tareas** sólo se implementan en un nodo de cálculo programado para ejecutar una tarea, justo antes de ejecutar la línea de comandos de la tarea. Si el paquete de la aplicación especificada y la versión ya está en el nodo, se vuelve a implementar y se utiliza el paquete existente.

    Paquetes de aplicaciones de tareas son útiles en entornos de pool compartido, donde los distintos trabajos se ejecutan en un grupo y el grupo no se elimina cuando se completa un trabajo. Si el trabajo tiene menos tareas de nodos en el grupo, paquetes de aplicaciones de tarea pueden minimizar la transferencia de datos ya que la aplicación se implementa sólo en los nodos que ejecutan tareas.

    Otras situaciones que pueden beneficiarse de los paquetes de aplicación de tareas son tareas que usan una aplicación de gran tamaño, pero sólo un pequeño número de tareas. Por ejemplo, una etapa de procesamiento previo o una tarea de fusión, donde la aplicación de un tratamiento previo o mezcla es pesado.

> [AZURE.IMPORTANT] Hay restricciones en el número de aplicaciones y paquetes de aplicaciones dentro de una cuenta de proceso por lotes, así como el tamaño de paquete máximo de aplicación. Para obtener más información acerca de estos límites, consulte [cuotas y límites para el servicio de proceso de Azure](batch-quota-limit.md) .

### <a name="benefits-of-application-packages"></a>Beneficios de los paquetes de aplicación

Paquetes de aplicaciones pueden simplificar el código de la solución de lotes y reducir la sobrecarga necesaria para administrar las aplicaciones que se ejecutan las tareas.

Tarea de inicio de la agrupación no tiene que especificar una larga lista de archivos de recursos individuales para instalar en los nodos. No tienes que administrar manualmente varias versiones de los archivos de aplicación en el almacenamiento de Azure, o en los nodos. Y no es necesario preocuparse de generar [Direcciones URL SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para proporcionar acceso a los archivos en su cuenta de almacenamiento. Lote funciona en segundo plano con el almacenamiento de Azure para almacenar paquetes de aplicación e implementarlas para nodos de cálculo.

## <a name="upload-and-manage-applications"></a>Cargar y administrar aplicaciones

Puede usar el [portal de Azure] [ portal] o en la biblioteca de [.NET de administración por lotes](batch-management-dotnet.md) para administrar los paquetes de aplicaciones en la cuenta de proceso por lotes. En las secciones siguientes, se primero vincula una cuenta de almacenamiento, luego explican cómo agregar aplicaciones y paquetes y administrarlos con el portal.

### <a name="link-a-storage-account"></a>Vincular una cuenta de almacenamiento

Para utilizar paquetes de aplicaciones, primero debe vincular una cuenta de almacenamiento de Azure a su cuenta de proceso por lotes. Si todavía no ha configurado una cuenta de almacenamiento de información para la cuenta de proceso por lotes, el portal de Azure mostrará un aviso la primera vez que haga clic en el mosaico de **aplicaciones** en la hoja de la **cuenta de proceso por lotes** .

> [AZURE.IMPORTANT] Lote actualmente admite *sólo* el tipo de cuenta de almacenamiento de información de **Propósito General** como se describe en el paso 5, [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account), [cuentas de almacenamiento acerca de Azure](../storage/storage-create-storage-account.md). Al vincular una cuenta de almacenamiento de Azure a tu cuenta de proceso por lotes, vínculo *sólo* una cuenta de almacenamiento de **Propósito General** .

![Ninguna advertencia de cuenta configurada de almacenamiento de información en el portal de Azure][9]

El servicio de proceso por lotes utiliza la cuenta de almacenamiento asociada para el almacenamiento y la recuperación de paquetes de aplicaciones. Una vez haya vinculado las dos cuentas, por lotes pueden implementar automáticamente los paquetes almacenados en la cuenta de almacenamiento vinculada a los nodos de cálculo. Haga clic en **configuración de la cuenta de almacenamiento de información** en la hoja de la **Advertencia** y haga clic en **Cuenta de almacenamiento** en el blade de **Almacenamiento cuenta** para vincular una cuenta de almacenamiento a la cuenta de proceso por lotes.

![Elija hoja de almacenamiento cuenta en el portal de Azure][10]

Se recomienda que cree un almacenamiento cuenta *específicamente* para su uso con la cuenta de proceso por lotes y seleccione aquí. Para obtener más información acerca de cómo crear una cuenta de almacenamiento, consulte "Crear una cuenta de almacenamiento" en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Una vez haya creado una cuenta de almacenamiento, que puede, a continuación, vincularlo a tu cuenta por lotes utilizando la hoja de la **Cuenta de almacenamiento** .

> [AZURE.WARNING] Porque por lotes utiliza el almacenamiento de Azure para almacenar los paquetes de aplicaciones, está [cargada como normal] [ storage_pricing] para los datos blob del bloque. Asegúrese de tener en cuenta el tamaño y el número de los paquetes de aplicaciones y quitar periódicamente paquetes obsoletos para minimizar el costo.

### <a name="view-current-applications"></a>Ver aplicaciones actuales

Para ver las aplicaciones en la cuenta de proceso por lotes, haga clic en el elemento de menú de **aplicaciones** en el menú de la izquierda mientras está viendo la hoja de la **cuenta de proceso por lotes** .

![Mosaico de aplicaciones][2]

Se abrirá la hoja de **aplicaciones** :

![Enumerar las aplicaciones][3]

El módulo de **aplicaciones** muestra el ID de cada aplicación en su cuenta y las siguientes propiedades:

* **Paquetes**: el número de versiones asociadas a esta aplicación.
* **Versión predeterminada**: la versión que se instalará si no especifica una versión, cuando se establece la aplicación de un grupo. Esta configuración es opcional.
* **Permitir actualizaciones**, el valor que especifica las adiciones, eliminaciones y actualizaciones del paquete si se permiten. Si se establece en **No**, paquete actualizaciones y eliminaciones están deshabilitadas para la aplicación. Pueden agregarse sólo versiones nuevas de paquetes de aplicación. El valor predeterminado es **Sí**.

### <a name="view-application-details"></a>Ver detalles de la aplicación

Haga clic en una aplicación en el módulo de **aplicaciones** para abrir la hoja que incluye los detalles de esa aplicación.

![Detalles de la aplicación][4]

En la hoja detalles de aplicación, puede configurar los siguientes valores para la aplicación.

* **Permitir actualizaciones**--especificar si se pueden actualizar o eliminar sus paquetes de aplicaciones. Consulte "Actualizar o eliminar un paquete de aplicaciones" posteriormente en este artículo.
* **Versión predeterminada**: especificar un paquete de aplicación predeterminado para implementar nodos de cálculo.
* **Nombre para mostrar**: especifica el nombre de un "ambiente" que su solución puede utilizar cuando se muestra información acerca de la aplicación, como en la interfaz de usuario de un servicio que proporciona a los clientes a través de lotes de lote.

### <a name="add-a-new-application"></a>Agregar una nueva aplicación

Cree una nueva aplicación, agregar un paquete de aplicación y especifique un identificador de aplicación nuevo y único. El primer paquete de aplicación que se agrega con el nuevo ID de aplicación también crea la nueva aplicación.

Haga clic en **Agregar** en el módulo de **aplicaciones** para abrir la hoja de la **nueva aplicación** .

![Nuevo módulo de aplicación de portal de Azure][5]

La hoja **nueva aplicación** proporciona los campos siguientes para especificar la configuración de su nueva aplicación y el paquete de la aplicación.

**Id. de aplicación**

Este campo especifica el identificador de la aplicación nueva, que está sujeta a las reglas de validación de ID de lote de Azure estándares:

* Puede contener cualquier combinación de caracteres alfanuméricos, incluidos guiones y caracteres de subrayado.
* No puede contener más de 64 caracteres.
* Debe ser único dentro de la cuenta de proceso por lotes.
* Es conservar mayúsculas y case insensitive.

**Versión**

Especifica la versión del paquete de aplicación que está cargando. Las cadenas de versión están sujetas a las siguientes reglas de validación:

* Puede contener cualquier combinación de caracteres alfanuméricos, incluidos guiones, caracteres de subrayado y puntos.
* No puede contener más de 64 caracteres.
* Debe ser único dentro de la aplicación.
* Case preserving y distingue mayúsculas de minúsculas.

**Paquete de aplicación**

Este campo especifica el archivo .zip que contiene los archivos binarios y los archivos auxiliares necesarios para ejecutar la aplicación. Haga clic en el cuadro **Seleccione un archivo** o en el icono de carpeta para buscar y seleccionar un archivo .zip que contiene los archivos de la aplicación.

Después de seleccionar un archivo, haga clic en **Aceptar** para comenzar la carga para el almacenamiento de Azure. Una vez completada la operación de carga, se le notificará y se cerrará la hoja. Dependiendo del tamaño del archivo que se va a cargar y la velocidad de su conexión de red, esta operación puede tardar algún tiempo.

> [AZURE.WARNING] No cierre la hoja **nueva aplicación** antes de que finalice la operación de carga. Si lo hace, se detendrá el proceso de carga.

### <a name="add-a-new-application-package"></a>Agregar un nuevo paquete de aplicación

Para agregar una nueva versión del paquete de aplicación para una aplicación existente, seleccione una aplicación en el módulo de **aplicaciones** , haga clic en **paquetes**y haga clic en **Agregar** para abrir la hoja de **Agregar paquete** .

![Agregar hoja de paquete de aplicación de portal de Azure][8]

Como puede ver, los campos coinciden con los de la hoja de la **nueva aplicación** , pero se deshabilita el cuadro de **id de aplicación** . Como hizo con la nueva aplicación, especificar la **versión** del paquete de nuevo, busque el archivo .zip de **paquete de la aplicación** , haga clic en **Aceptar** para cargar el paquete.

### <a name="update-or-delete-an-application-package"></a>Actualizar o eliminar un paquete de aplicación

Para actualizar o eliminar un paquete de aplicación existente, abra la hoja de datos para la aplicación, haga clic en **paquetes** para abrir la hoja de **paquetes** , haga clic en el **botón de puntos suspensivos** en la fila del paquete de aplicación que desea modificar y seleccione la acción que desea realizar.

![Actualizar o eliminar el paquete en el portal de Azure][7]

**Actualización**

Cuando hace clic en **Actualizar**, se muestra la hoja del *paquete de actualización* . Esta hoja es similar a la hoja del *nuevo paquete de aplicación* , sin embargo se habilita sólo el campo de selección de paquete, que le permite especificar un nuevo archivo ZIP para cargar.

![Hoja de paquete de actualización en el portal de Azure][11]

**Eliminar**

Al hacer clic en **Eliminar**, se le pedirá que confirme la eliminación de la versión del paquete y por lotes elimina el paquete de almacenamiento de Azure. Si elimina la versión predeterminada de una aplicación, se quita la configuración de la **versión predeterminada** de la aplicación.

![Eliminar aplicación][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicaciones en nodos computacionales.

Ahora que ha visto cómo administrar paquetes de aplicación con el portal de Azure, podemos discutir cómo implementarlas para los nodos de cálculo y ejecutarlas con las tareas por lotes.

### <a name="install-pool-application-packages"></a>Instalar paquetes de aplicación de grupo

Para instalar un paquete de aplicación en todos los nodos de cálculo en un grupo, especifique una o más aplicaciones paquete *referencias* para el grupo. Los paquetes de aplicaciones que se especifican para un grupo de servidores se instalan en cada nodo de cálculo cuando ese nodo une a la agrupación, y cuando el nodo se reinicia o se rehace su imagen.

En .NET por lotes, especifique uno o más [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] al crear un nuevo grupo o para un grupo existente. La [ApplicationPackageReference] [ net_pkgref] clase especifica un identificador de aplicación y la versión para instalar en un grupo de nodos de cálculo.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Si se produce un error en la implementación del paquete de aplicación por cualquier motivo, el servicio de lote marca el nodo de [inutilizable][net_nodestate], y no hay ninguna tarea se programará para su ejecución en ese nodo. En este caso, debe **reiniciar** el nodo para reiniciar la implementación del paquete. Reiniciar el nodo también le permitirá la programación de tareas de nuevo en el nodo.

### <a name="install-task-application-packages"></a>Instalar paquetes de solicitud de tarea

Similar a un grupo, especifique aplicación paquete *referencias* para una tarea. Cuando una tarea está programada para ejecutarse en un nodo, el paquete está descargado y extraído antes de que se ejecuta la línea de comandos de la tarea. Si una versión y el paquete especificado ya está instalado en el nodo, el paquete no se descarga y se utiliza el paquete existente.

Para instalar un paquete de solicitud de tarea, configure de la tarea [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propiedad:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Ejecutar las aplicaciones instaladas

Los paquetes que ha especificado para un grupo o tarea se descargan y se extrae a un directorio con nombre dentro de la `AZ_BATCH_ROOT_DIR` del nodo. Por lotes también crea una variable de entorno que contiene la ruta de acceso al directorio con nombre. Las líneas de comando de tareas utilizar esta variable de entorno al hacer referencia a la aplicación en el nodo. La variable tiene el formato siguiente:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`y `version` son valores que corresponden a la versión de la aplicación y el paquete que ha especificado para la implementación. Por ejemplo, si especificó esa versión 2.7 de aplicación *blender* debe estar instalado, las líneas de comando tarea utilizaría esta variable de entorno para tener acceso a sus archivos:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si especifica una versión predeterminada para una aplicación, se puede omitir el sufijo de versión. Por ejemplo, si establece "2.7" como la versión predeterminada para la aplicación de *Mezclador*, las tareas pueden hacer referencia a la variable de entorno siguiente y ejecutarán la versión 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

El siguiente fragmento de código muestra una línea de comandos de ejemplo tareas que inicia la versión predeterminada de la aplicación de *Mezclador* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consulte [Configuración del entorno para las tareas](batch-api-basics.md#environment-settings-for-tasks) de la [Introducción de la característica de lote](batch-api-basics.md) para obtener más información acerca de la configuración del entorno de nodo de cálculo.

## <a name="update-a-pools-application-packages"></a>Actualizar los paquetes de aplicación de la agrupación

Si ya ha configurado un grupo existente con un paquete de aplicación, puede especificar un nuevo paquete para el grupo. Si se especifica una nueva referencia de paquete para un grupo, de las condiciones siguientes:

* Todos los nodos nuevos que se unen a la piscina y cualquier nodo existente que se reinicia o se rehace su imagen instalarán el paquete recién especificado.
* Calcular los nodos que ya están en el grupo cuando se actualizan las referencias del paquete no instalación automáticamente el nuevo paquete de aplicación. Para calcular estos nodos deben ser reiniciados o renovados para recibir el nuevo paquete.
* Cuando se implementa un nuevo paquete, las variables de entorno creado reflejan las nuevas referencias de paquete de la aplicación.

En este ejemplo, el grupo existente tiene la versión 2.7 de la aplicación de *Mezclador* configurada como uno de sus [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Para actualizar los nodos del grupo con versión 2.76b, especificar una nueva [ApplicationPackageReference] [ net_pkgref] con la nueva versión y confirmar el cambio.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ahora que se ha configurado la nueva versión, cualquier nodo *nuevo* que se une el grupo tendrá versión 2.76b implementado. Para instalar 2.76b en los nodos que están *ya* en el fondo, reiniciar o crear una nueva imagen de ellos. Tenga en cuenta que reinició nodos conservará los archivos desde las implementaciones anteriores de paquetes.

## <a name="list-the-applications-in-a-batch-account"></a>Enumerar las aplicaciones en una cuenta de proceso por lotes

Puede enumerar las aplicaciones y sus paquetes en una cuenta de proceso por lotes utilizando la [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] método.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Envolver

Con paquetes de aplicaciones, puede ayudar a los clientes seleccionar las aplicaciones para sus trabajos y especificar la versión exacta que se utilizará al procesar los trabajos con el servicio habilitado por lotes. También podría proporcionar la capacidad de cargar y realizar un seguimiento de sus propias aplicaciones en el servicio de sus clientes.

## <a name="next-steps"></a>Próximos pasos

* La [API de REST de lote] [ api_rest] también proporciona compatibilidad para trabajar con paquetes de aplicaciones. Por ejemplo, ver el [applicationPackageReferences] [ rest_add_pool_with_packages] elemento en [Agregar un grupo a una cuenta de] [ rest_add_pool] para obtener información sobre cómo especificar los paquetes para instalar mediante la API de REST. Vea [aplicaciones] [ rest_applications] para obtener más información acerca de cómo obtener información de la aplicación utilizando la API de REST de lote.

* Aprenda cómo [Administrar cuotas con .NET de administración por lotes y las cuentas por lotes de Azure](batch-management-dotnet.md), mediante programación. El [Lote administración .NET] [ api_net_mgmt] biblioteca puede habilitar características de creación y eliminación de cuenta para la aplicación del proceso o servicio.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nivel de paquetes de aplicaciones"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaico de aplicaciones de portal de Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Módulo de aplicaciones de portal de Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Hoja de detalles de aplicación de portal de Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nuevo módulo de aplicación de portal de Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Actualizar o eliminar paquetes de lista desplegable en el portal de Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nueva hoja de paquete de aplicación de portal de Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ninguna alerta de cuenta vinculada de almacenamiento"
[10]: ./media/batch-application-packages/app_pkg_10.png "Elija hoja de almacenamiento cuenta en el portal de Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Hoja de paquete de actualización en el portal de Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar el cuadro de diálogo de confirmación de paquete en portal de Azure"
