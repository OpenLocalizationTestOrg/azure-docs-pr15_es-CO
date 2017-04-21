<properties
    pageTitle="Trabajo y tarea de salida de persistencia en el lote de Azure | Microsoft Azure"
    description="Aprenda a usar el almacenamiento de Azure un almacén permanente para la tarea por lotes y el trabajo de salida y permiten ver el resultado almacenado en el portal de Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Conservar la salida de trabajo y tareas por lotes de Azure

Las tareas que se ejecuta en lote suele dar como resultado debe almacenarse y posteriormente recuperó después por otras tareas en el trabajo, la aplicación cliente que se ejecuta el trabajo, o ambos. Esta salida podría ser archivos creados mediante el procesamiento de entrada de datos o archivo de registro asociado con la ejecución de la tarea. Este artículo presenta una biblioteca de clases de .NET que usa una técnica basada en convenciones para conservar este resultado de la tarea al almacenamiento Azure Blob, haciéndolo disponible incluso después de eliminar los grupos de puestos de trabajo y nodos de cálculo.

Mediante la técnica de este artículo, también puede ver el resultado de la tarea de **registros guardados** en el [portal de Azure]y **guardado los archivos de salida** [portal].

![Archivos de salida de guardado y selectores de registros guardados en portal][1]

>[AZURE.NOTE] Las [Convenciones de archivo por lotes de Azure] [ nuget_package] biblioteca de clases .NET que se describe en este artículo está en vista previa. Algunas de las características que se describen aquí pueden cambiar antes de su disponibilidad general.

## <a name="task-output-considerations"></a>Consideraciones de la salida de tarea

Cuando diseñe la solución de lote, debe considerar varios factores relacionados con salidas de trabajo y tareas.

* **Calcular la duración del nodo**: calcular los nodos suelen ser transitorios, especialmente en grupos de escala automática. Los resultados de las tareas que se ejecutan en un nodo sólo están disponibles mientras existe el nodo, y sólo en el tiempo de retención de archivo que has establecido para la tarea. Para garantizar que se conserva el resultado de la tarea, las tareas, por tanto, deben cargar los archivos de salida a un almacén permanente, por ejemplo, el almacenamiento de Azure.

* **Almacenamiento de información de salida**: para conservar los datos de salida de la tarea al almacenamiento duradero, puede utilizar el [SDK de almacenamiento de Azure](../storage/storage-dotnet-how-to-use-blobs.md) en el código de tarea para cargar el resultado de la tarea a un contenedor de almacenamiento Blob. Si implementa un contenedor y la convención de nomenclatura de archivos, la aplicación de cliente o de otras tareas en el trabajo pueden encontrar y descargar este resultado basándose en la convención.

* **Recuperación de salida**: puede recuperar resultados de tarea directamente desde los nodos de cálculo en el grupo de servidores o de almacenamiento de Azure si las tareas continúan su salida. Para recuperar los resultados de una tarea directamente desde un nodo de cálculo, es necesario el nombre del archivo y su ubicación en el nodo de salida. Si persisten los resultados para el almacenamiento de Azure, tareas de nivel inferiores o la aplicación de cliente debe tener la ruta de acceso completa al archivo en el almacenamiento de Azure para descargarlo mediante el SDK del almacenamiento de Azure.

* **Ver resultado**: cuando se desplaza a una tarea por lotes en el portal de Azure y selecciona **los archivos en el nodo**, se presentan con todos los archivos asociados con la tarea, no sólo los archivos de salida que le interesa. De nuevo, archivos en los nodos de cálculo sólo están disponibles mientras existe el nodo y sólo dentro del tiempo de retención de archivo haya establecido para la tarea. Para ver el resultado de la tarea que ha persistido en el almacenamiento de Azure en el portal o en una aplicación como el [Explorador de almacenamiento de Azure][storage_explorer], debe conocer su ubicación y desplácese hasta el archivo directamente.

## <a name="help-for-persisted-output"></a>Ayuda para la salida persistente

Para ayudar a que más fácilmente persisten trabajos y tareas de salida, el equipo de lote ha definido e implementa un conjunto de convenciones de nomenclatura, así como una biblioteca de clases. NET, las [Convenciones de archivo por lotes de Azure] [ nuget_package] library, que se puede utilizar en las aplicaciones de proceso por lotes. Además, el portal de Azure es consciente de estas convenciones de nomenclatura para que usted puede encontrar fácilmente los archivos que haya guardado utilizando la biblioteca.

## <a name="using-the-file-conventions-library"></a>Uso de la biblioteca de convenciones de archivo

[Azure convenciones de archivo por lotes] [ nuget_package] es una biblioteca de clase .NET que las aplicaciones .NET de lote se pueden utilizar para almacenar y recuperar los resultados de la tarea desde el almacenamiento de Azure y fácilmente. Se pretende para su uso en código de cliente y tareas--en el código de tarea para almacenar archivos y en el código de cliente para mostrar y recuperarlos. Las tareas también pueden utilizar la biblioteca para recuperar los resultados de las tareas de nivel superior, como en un escenario de [dependencias entre tareas](batch-task-dependencies.md) .

La biblioteca de convenciones se encarga de garantizar que las tareas y contenedores de salida archivos se nombran según la convención y se cargan en el lugar correcto cuando se conserva en el almacenamiento de Azure. Cuando se recuperan los resultados, puede encontrar fácilmente los resultados para un determinado trabajo o tarea al enumerar o recuperar los resultados por ID y finalidad, en lugar de tener que conocer los nombres de archivo o su ubicación en el almacenamiento de información.

Por ejemplo, puede utilizar la biblioteca para "Mostrar todos los archivos intermedios para tarea 7" o "get me la vista previa en miniatura de trabajo *mymovie*," sin necesidad de conocer los nombres de archivo o ubicación dentro de su cuenta de almacenamiento.

### <a name="get-the-library"></a>Obtener la biblioteca

Puede obtener la biblioteca, que contiene clases nuevas y amplía la [CloudJob] [ net_cloudjob] y [CloudTask] [ net_cloudtask] clases con nuevos métodos, de [NuGet][nuget_package]. Puede agregar a su proyecto de Visual Studio mediante el [Administrador de paquetes de NuGet biblioteca][nuget_manager].

>[AZURE.TIP] Puede encontrar el [código fuente] de[ github_file_conventions] de la biblioteca de convenciones de archivo por lotes de Azure en GitHub en el SDK de Microsoft Azure para el repositorio. NET.

## <a name="requirement-linked-storage-account"></a>Requisito: cuenta de almacenamiento vinculado

Para almacenar los resultados mediante la biblioteca de convenciones de archivo de almacenamiento duradero y verlos en el portal de Azure, primero debe [vincular una cuenta de almacenamiento de Azure](batch-application-packages.md#link-a-storage-account) a su cuenta de proceso por lotes. Si no lo ha hecho ya, vincular una cuenta de almacenamiento en su cuenta por lotes mediante el portal de Azure:

**Cuenta de lote** blade > **configuración** > **Cuenta de almacenamiento** > **Cuenta de almacenamiento** (ninguno) > Seleccionar una cuenta de almacenamiento en su suscripción

Para un análisis más detallado sobre la vinculación de una cuenta de almacenamiento, vea [implementación de aplicaciones con paquetes de aplicaciones de proceso de Azure](batch-application-packages.md).

## <a name="persist-output"></a>Conservar los resultados

Hay dos acciones principales para realizar al guardar el trabajo y tarea de salida con la biblioteca de convenciones de archivo: crear el contenedor de almacenamiento y guardar la salida en el contenedor.

>[AZURE.WARNING] Dado que todas las salidas de trabajo y tareas se almacenan en el mismo contenedor, [límites de procesos de almacenamiento de información](../storage/storage-performance-checklist.md#blobs) pueden aplicarse si un gran número de tareas intenta conservar archivos al mismo tiempo.

### <a name="create-storage-container"></a>Crear el contenedor de almacenamiento de información

Antes de que las tareas comienzan salida persistente al almacenamiento de información, debe crear un contenedor de almacenamiento blob al que van cargar sus resultados. Esto se realiza llamando a [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Este método de extensión tiene un [CloudStorageAccount] de[ net_cloudstorageaccount] de objeto como un parámetro y crea un contenedor llamado de tal manera que su contenido sea reconocible por el portal de Azure y los métodos de recuperación que se describe más adelante en el artículo.

Suelen colocar este código en la aplicación de cliente, la aplicación que crea los grupos, trabajos y tareas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Almacenar resultados de tarea

Ahora que ha preparado un contenedor en el almacenamiento de blobs, tareas pueden guardar resultados al contenedor mediante el [TaskOutputStorage] [ net_taskoutputstorage] clase se encuentra en la biblioteca de convenciones de archivo.

En el código de la tarea, primero cree una [TaskOutputStorage] [ net_taskoutputstorage] objeto, a continuación, cuando la tarea ha finalizado su trabajo, llame a la [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] método para guardar su resultado en el almacenamiento de Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

El parámetro "tipo de salida" clasifica los archivos persistentes. Hay cuatro predefinidos [TaskOutputKind] [ net_taskoutputkind] tipos: "TaskOutput", "TaskPreview", "TaskLog" y "TaskIntermediate". También puede definir las clases personalizadas si serían útiles en su flujo de trabajo.

Estos tipos de salida le permiten especificar qué tipo de salidas para enumerar al consultar posteriormente por lotes de las salidas persistentes de una determinada tarea. En otras palabras, cuando se muestran los resultados de una tarea, puede filtrar la lista en uno de los tipos de salida. Por ejemplo, "Dame la *vista previa* del resultado de la tarea *109*." Más en el listado y recuperar resultados aparece en [recuperar resultados](#retrieve-output) más adelante en el artículo.

>[AZURE.TIP] El tipo de salida también designa donde en el portal de Azure aparece un archivo en particular: *TaskOutput*-archivos clasificados aparecen en "Archivos de salida de la tarea" y *TaskLog* archivos aparecen en los registros de tareas"."

### <a name="store-job-outputs"></a>Almacenar los resultados del trabajo

Además de almacenar resultados de tarea, puede almacenar los resultados asociados a un trabajo completo. Por ejemplo, en la tarea de combinación de un trabajo de procesamiento de la película, podría conservar la película procesada completamente como resultado de un trabajo. Cuando haya completado su trabajo, la aplicación cliente puede simplemente enumerar y recuperar los resultados para el trabajo y no es necesario consultar las tareas individuales.

Almacenar la salida de trabajo llamando a la [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] método, y especifique la [JobOutputKind] [ net_joboutputkind] y nombre de archivo:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Como con TaskOutputKind para los resultados de tarea, utilice la [JobOutputKind] [ net_joboutputkind] parámetro para clasificar un trabajo de conservar archivos. Este parámetro le permite consultar más adelante para un tipo específico de salida (lista). El JobOutputKind incluye tipos de salida y vista previa y admite la creación de tipos personalizados.

### <a name="store-task-logs"></a>Almacenar registros de tareas

Además, de guardar un archivo de almacenamiento duradero cuando se completa una tarea o trabajo, puede ser necesario conservar los archivos que se actualizan durante la ejecución de una tarea--archivos de registro o `stdout.txt` y `stderr.txt`, por ejemplo. Para ello, la biblioteca convenciones de archivo por lotes de Azure proporciona el [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] método. Con [SaveTrackedAsync][net_savetrackedasync], puede realizar un seguimiento de las actualizaciones en un archivo en el nodo (en un intervalo especificado) y conservar las actualizaciones para el almacenamiento de Azure.

En el siguiente fragmento de código, utilizamos [SaveTrackedAsync] [ net_savetrackedasync] actualizar `stdout.txt` en el almacenamiento de Azure cada 15 segundos durante la ejecución de la tarea:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`es simplemente un marcador de posición para el código que normalmente realizaría la tarea. Por ejemplo, podría tener código que descarga los datos desde el almacenamiento de Azure y realiza el cálculo o la transformación en él. La parte importante de este fragmento de código demuestra cómo se puede incluir este código en un `using` bloque para actualizar periódicamente un archivo con [SaveTrackedAsync][net_savetrackedasync].

El `Task.Delay` es necesaria al final de este `using` bloque para asegurarse de que el agente de nodo tiene tiempo para vaciar el contenido de la salida estándar al archivo stdout.txt en el nodo (el agente de nodo es un programa que se ejecuta en cada nodo en la agrupación y proporciona la interfaz de comando y control entre el nodo y el servicio de proceso por lotes). Sin este retraso, es posible que se pierdan los últimos segundos de salida. Este retraso puede no ser necesario para todos los archivos.

>[AZURE.NOTE] Cuando se habilita el seguimiento con SaveTrackedAsync de archivos, sólo *anexa* al archivo de seguimiento se conservan para el almacenamiento de Azure. Utilice este método sólo para el seguimiento de archivos de registro sin rotación u otros archivos que se adjuntan, es decir, los datos sólo se agrega al final del archivo cuando se actualiza.

## <a name="retrieve-output"></a>Recuperar la salida

Cuando se recupera el resultado almacenado mediante la biblioteca de convenciones de archivo por lotes de Azure, lo hace en forma centrado en tareas y trabajo. Puede solicitar la salida para tarea o trabajo sin necesidad de conocer su ruta de acceso en el blob de almacenamiento o incluso el nombre de su archivo. Puede simplemente decir "Dame de tarea *109*archivos de salida".

El fragmento de código siguiente recorre en iteración todas las tareas de un trabajo, imprime cierta información acerca de los archivos de salida de la tarea y, a continuación, descarga los archivos desde el almacenamiento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Resultados de tarea y el portal de Azure

El portal de Azure muestra resultados de tarea y los registros que se conservan a una cuenta de almacenamiento Azure vinculada utilizando las convenciones de nomenclatura que se encuentra en el [Archivo convenciones Léame de Azure por lotes][github_file_conventions_readme]. Puede implementar estas convenciones en un idioma de su elección, o puede utilizar la biblioteca de convenciones de archivo en las aplicaciones. NET.

### <a name="enable-portal-display"></a>Activar la visualización del portal

Para habilitar la presentación de los resultados en el portal, debe cumplir los siguientes requisitos:

 1. [Vincular una cuenta de almacenamiento de Azure](#requirement-linked-storage-account) a su cuenta de proceso por lotes.
 2. Cumplir las convenciones de nomenclatura predefinidas para los contenedores de almacenamiento de información y archivos al conservar los resultados. Puede encontrar la definición de estas convenciones en la biblioteca de convenciones de archivo [Léame][github_file_conventions_readme]. Si utiliza las [Convenciones de archivo por lotes de Azure] [ nuget_package] biblioteca para conservar su salida, este requisito se cumple.

### <a name="view-outputs-in-the-portal"></a>Ver resultados en el portal

Para ver los registros y resultados de tarea en el portal de Azure, desplácese a la tarea cuya salida interesa, haga clic en **archivos de salida guardado** o **registros guardados**. Esta imagen muestra los **archivos de salida guardado** para la tarea con el identificador "007":

![Hoja de resultados de tareas en el portal de Azure][2]

## <a name="code-sample"></a>Ejemplo de código

La [PersistOutputs] [ github_persistoutputs] proyecto de ejemplo es uno de los [ejemplos de código de lote de Azure] [ github_samples] en GitHub. Esta solución de Visual Studio 2015 muestra cómo utilizar la biblioteca de convenciones de archivo por lotes de Azure para conservar los resultados de tarea en el almacenamiento duradero. Para ejecutar el ejemplo, siga estos pasos:

1. Abra el proyecto en **Visual Studio 2015**.
2. Agregue su lote y almacenamiento de **credenciales de la cuenta** a **AccountSettings.settings** en el proyecto Microsoft.Azure.Batch.Samples.Common.
3. **Generar** (pero no se ejecutan) la solución. Si se le pide, restaurar los paquetes de NuGet.
4. Utilice el portal de Azure para cargar un [paquete de aplicación](batch-application-packages.md) para **PersistOutputsTask**. Incluir el `PersistOutputsTask.exe` y sus ensamblados dependientes en el paquete zip, establecer el identificador de aplicación a "PersistOutputsTask" y la versión del paquete de aplicación a "1.0".
5. **Inicio** proyecto de la **PersistOutputs** (ejecutar).

## <a name="next-steps"></a>Próximos pasos

### <a name="application-deployment"></a>Implementación de aplicaciones

La función de [paquetes de aplicaciones](batch-application-packages.md) de lote proporciona una forma sencilla para ambos implementar y versión de las aplicaciones que se ejecutan las tareas en nodos de cálculo.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo

Desproteger los [nodos de cálculo instalar aplicaciones y datos de ensayo en Batch] [ forum_post] registrar en el foro de Azure lote para tener una visión general de los distintos métodos de preparación de los nodos para ejecutar tareas. Escrito por uno de los miembros del equipo por lotes de Azure, esta publicación es un buen manual sobre las distintas formas de obtener archivos (incluidas las aplicaciones y los datos de entrada de tarea) en los nodos de cómputo y algunas consideraciones especiales para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Archivos de salida de guardado y selectores de registros guardados en portal"
[2]: ./media/batch-task-output/task-output-02.png "Hoja de resultados de tareas en el portal de Azure"