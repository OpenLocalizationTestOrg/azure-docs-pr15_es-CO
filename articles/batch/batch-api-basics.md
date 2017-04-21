<properties
    pageTitle="Introducción a las características lote Azure para desarrolladores | Microsoft Azure"
    description="Conocer las características de su API desde una perspectiva de desarrollo y el servicio de proceso por lotes."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Introducción a las características para desarrolladores de lote

En esta información general de los componentes principales del servicio por lotes de Azure, analizaremos las características principales del servicio y soluciones de cálculo de recursos que pueden utilizar los desarrolladores lote para construir paralelo a gran escala.

Si está desarrollando una aplicación computacional distribuida o servicio que emite directo [REST API] [ batch_rest_api] llamadas o si está usando uno de los [SDK de lote](batch-technical-overview.md#batch-development-apis), utilizará muchos de los recursos y características descritas en este artículo.

> [AZURE.TIP] Para obtener una introducción de alto nivel al servicio por lotes, consulte [Aspectos básicos de Azure por lotes](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Flujo de trabajo de servicio por lotes

El siguiente flujo de trabajo de alto nivel es típico de casi todas las aplicaciones y servicios que utilizan el servicio por lotes para procesar las cargas de trabajo paralelas:

1. Cargar los **archivos de datos** que se van a procesar a un [Almacenamiento de Azure] [ azure_storage] cuenta. Por lotes incluyen compatibilidad integrada para tener acceso a almacenamiento Azure Blob y las tareas pueden descargar estos archivos a [nodos de cálculo](#compute-node) cuando se ejecutan las tareas.

2. Cargue los **archivos de la aplicación** que se ejecutarán las tareas. Estos archivos pueden ser archivos binarios o secuencias de comandos y sus dependencias y ejecutan las tareas en sus trabajos. Las tareas pueden descargar estos archivos desde su cuenta de almacenamiento, o puede utilizar la característica de [paquetes de aplicaciones](#application-packages) de proceso por lotes para la implementación y administración de aplicaciones.

3. Crear un [grupo](#pool) de nodos de cálculo. Cuando se crea un grupo, especifique el número de nodos de cálculo para el grupo, su tamaño y el sistema operativo. Cuando se ejecuta cada tarea en su trabajo, ha asignado para ejecutar en uno de los nodos en la agrupación.

4. Crear un [trabajo](#job). Un trabajo administra una colección de tareas. Asociar cada trabajo a un grupo específico donde se ejecutarán las tareas de la tarea.

5. Agregar [tareas](#task) a la tarea. Cada tarea ejecuta la aplicación o el script que has enviado para procesar los archivos de datos que se descarga desde su cuenta de almacenamiento. Como cada tarea finaliza, puede cargar su salida en el almacenamiento de Azure.

6. Supervisar el progreso de trabajo y recuperar el resultado de la tarea desde el almacenamiento de Azure.

Las siguientes secciones tratan estos y otros recursos de proceso por lotes que permiten su escenario computacional distribuido.

> [AZURE.NOTE] Se necesita una [cuenta de proceso por lotes](batch-account-create-portal.md) para utilizar el servicio de proceso por lotes. Además, casi todas las soluciones de usar un [Almacenamiento de Azure] [ azure_storage] para el almacenamiento de archivos y la recuperación de cuenta. Lote actualmente admite sólo el **Propósito General** cuenta tipo de almacenamiento, tal como se describe en el paso 5 de [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Recursos del servicio de proceso por lotes

Algunos de los siguientes recursos: cuentas, calculan nodos, grupos, trabajos y tareas--requeridas por todas las soluciones que utilizan el servicio por lotes. Otros, como programaciones de trabajo y paquetes de aplicaciones, son características útiles, pero opcionales.

- [Cuenta](#account)
- [Nodo de cálculo](#compute-node)
- [Piscina](#pool)
- [Trabajo](#job)

  - [Programaciones de trabajos](#scheduled-jobs)

- [Tarea](#task)

  - [Iniciar tarea](#start-task)
  - [El administrador tarea](#job-manager-task)
  - [Tareas de preparación y lanzamiento](#job-preparation-and-release-tasks)
  - [Tareas varias instancias (MPI)](#multi-instance-tasks)
  - [Dependencias entre tareas](#task-dependencies)

- [Paquetes de aplicaciones](#application-packages)

## <a name="account"></a>Cuenta

Una cuenta de proceso por lotes es una entidad identificada de forma única dentro del servicio de proceso por lotes. Todo el procesamiento está asociado con una cuenta de proceso por lotes. Cuando se realizan operaciones con el servicio de proceso por lotes, es necesario el nombre de cuenta y una de sus claves de cuenta. Puede [crear una cuenta de Azure lote mediante el portal de Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Nodo de cálculo

Un nodo de cálculo es una Azure máquina virtual (VM) que se dedica a procesar una parte de su carga de trabajo. El tamaño de un nodo determina el número de núcleos de CPU, capacidad de memoria y tamaño del sistema de archivos local que se asigna al nodo. Puede crear grupos de nodos de Windows o Linux mediante imágenes de servicios de nube de Azure o mercado de máquinas virtuales. Consulte la siguiente sección del [grupo](#pool) para obtener más información sobre estas opciones.

Nodos pueden ejecutar cualquier archivo ejecutable o script que es compatible con el entorno de sistema operativo del nodo. Esto incluye \*.exe, \*.cmd, \*.bat y secuencias de comandos de PowerShell para Windows--y binarios, shell y secuencias de comandos de Python para Linux.

Todos calculan también incluyen nodos de proceso por lotes:

- Un estándar de [estructura de carpetas](#files-and-directories) y asociadas [las variables de entorno](#environment-settings-for-tasks) que están disponibles para referencia por tareas.
- Configuración de **Firewall** está configurado para controlar el acceso.
- [Acceso remoto](#connecting-to-compute-nodes) a Windows (Remote Desktop Protocol (RDP)) y los nodos de Linux (Secure Shell (SSH)).

## <a name="pool"></a>Piscina

Un grupo es una colección de nodos que la aplicación se ejecuta en. El grupo puede crearse manualmente por el usuario o automáticamente por el servicio de proceso por lotes cuando se especifica que se realice el trabajo. Puede crear y administrar un grupo que cumpla los requisitos de recursos de la aplicación. Un grupo puede utilizarse sólo por la cuenta de proceso por lotes en que se creó. Una cuenta de proceso por lotes puede tener más de un grupo.

Grupos de lotes Azure generación sobre la plataforma principal de Azure compute. Proporcionan asignación a gran escala, instalación de aplicaciones, distribución de datos, la supervisión del estado y ajuste flexible del número de nodos de cálculo dentro de un grupo ([escala](#scaling-compute-resources)).

Todos los nodos que se agregan a un grupo se asigna una dirección IP y un nombre único. Cuando se quita un nodo de un grupo, se pierden los cambios que se realizan en el sistema operativo o los archivos, y se liberan su nombre y dirección IP para un uso futuro. Cuando un nodo abandona un grupo, su duración es sobre.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- COMPUTE nodo **sistema operativo** y **versión**

    Tiene dos opciones al seleccionar un sistema operativo para los nodos en el grupo de servidores: **Configuración de la máquina Virtual** y **Configuración de servicios de nube**.

    **Configuración de máquina virtual** proporciona imágenes de Linux y Windows para calculan los nodos de las [Máquinas virtuales de Azure Marketplace][vm_marketplace].
    Cuando se crea un grupo que contiene los nodos de configuración de máquina Virtual, debe especificar no sólo el tamaño de los nodos, pero también la **referencia de la imagen de máquina virtual** y el **agente de nodo SKU** de lote esté instalado en los nodos. Para obtener más información acerca de cómo especificar estas propiedades de grupo, consulte [disposición Linux nodos de cálculo en grupos de lotes de Azure](batch-linux-nodes.md).

    **Configuración de servicios de nube** proporciona que Windows compute nodos *solamente*. Sistemas operativos disponibles para los grupos de configuración de servicios de nube se enumeran en la [matriz de compatibilidad SDK y versiones del sistema operativo de invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Cuando se crea un grupo que contiene los nodos de servicios de nube, debe especificar sólo el tamaño del nodo y su *Familia del sistema operativo*. Al crear nodos de cálculo de grupos de Windows, se suele utilizar servicios en la nube.

    - La *Familia del sistema operativo* también determina qué versiones de .NET se instalan con el sistema operativo.
    - Como con las funciones de trabajo dentro de los servicios de nube, puede especificar una *Versión del sistema operativo* (para obtener más información sobre los roles de trabajo, consulte la sección [acerca de los servicios de nube](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) en la [Visión general de los servicios de nube](../cloud-services/cloud-services-choose-me.md)).
    - Como con las funciones de trabajo, le recomendamos que especifique `*` para la *Versión del sistema operativo* para que los nodos se actualizan automáticamente y no hay ningún trabajo necesario para atender al recién lanzado versiones. Es el caso de uso principal para seleccionar una versión específica del sistema operativo garantizar la compatibilidad de las aplicaciones, lo que permite las pruebas que se realizarán antes de permitir que la versión de actualización de compatibilidad con versiones anteriores. Después de la validación, puede actualizarse la *Versión del sistema operativo* para el grupo y la nueva imagen de sistema operativo puede ser instalado--las tareas en ejecución se interrumpen y devuelto a la cola.

- **Tamaño de los nodos**

    Tamaños de nodo de **Configuración de servicios de nube** compute aparecen en [tamaños para servicios de nube](../cloud-services/cloud-services-sizes-specs.md). Lote admite todos los tamaños de los servicios de nube excepto `ExtraSmall`.

    **Configuración de máquina virtual** compute nodo tamaños aparecen en [tamaños de máquinas virtuales en Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) y [tamaños de máquinas virtuales en Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lote admite todos los tamaños de Azure VM excepto `STANDARD_A0` y aquellos con almacenamiento premium (`STANDARD_GS`, `STANDARD_DS`, y `STANDARD_DSV2` series).

    Al seleccionar un tamaño de nodo de cálculo, tenga en cuenta las características y los requisitos de las aplicaciones que encontrará en los nodos. Aspectos como si la aplicación es multiproceso y la cantidad de memoria que consume puede ayudar a determinan el tamaño de nodo más conveniente y rentable. Es típico para seleccionar un tamaño de nodo suponiendo que una tarea se ejecutará en un nodo cada vez. Sin embargo, es posible tener varias tareas (y, por tanto, varias instancias de la aplicación) [se ejecuta en paralelo](batch-parallel-node-tasks.md) en nodos de cálculo durante la ejecución del trabajo. En este caso, es frecuente elegir un nodo más grande para acomodar el aumento de la demanda de la ejecución de tareas paralelas. Para obtener más información, vea [Directiva de programación de tareas](#task-scheduling-policy) .

    Todos los nodos de un grupo tienen el mismo tamaño. Si pretende ejecutar aplicaciones con distintos requisitos de sistema y cargar los niveles, recomendamos que utilice grupos distintos.

- **Número de nodos**

    Esto es el número de nodos de cálculo que desea implementar en el grupo. Esto se conoce como *destino* porque, en algunas situaciones, la agrupación no podría alcanzar el número deseado de nodos. Un grupo no podría alcanzar el número deseado de nodos si alcanza la [cuota de núcleo](batch-quota-limit.md#batch-account-quotas) para la cuenta de proceso por lotes--o si hay una fórmula de escala automática que ha aplicado al grupo que limita el número máximo de nodos (consulte la siguiente sección "Directiva de escalado").

- **Directiva de escala**

    Además de especificar un número estático de nodos, puede escribir en su lugar y aplicar una [fórmula de escalado automático](#scaling-compute-resources) a un grupo. El servicio de proceso por lotes periódicamente evalúa la fórmula y ajusta el número de nodos dentro del grupo basado en grupo, trabajo y parámetros de la tarea se pueden especificar distintos.

- **Directiva de programación de tareas**

    La opción de configuración [max tareas por nodo](batch-parallel-node-tasks.md) determina el número máximo de tareas que se pueden ejecutar en paralelo en cada nodo de cálculo en la agrupación.

    La configuración predeterminada es que una tarea cada vez que se ejecuta en un nodo, pero hay situaciones donde es beneficioso tener más de una tarea que se ejecuta en un nodo simultáneamente. Consulte el [escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo de [tareas simultáneas de nodo](batch-parallel-node-tasks.md) para ver cómo puede beneficiarse de varias tareas por nodo.

    También puede especificar un *tipo de relleno* que determina si reparte las tareas uniformemente entre todos los nodos en un grupo de lotes o paquetes de cada nodo con el número máximo de tareas antes de asignar tareas a otro nodo.

- **Estado de las comunicaciones** de nodos computacionales.

    En la mayoría de los escenarios, tareas funcionan de forma independiente y no necesitan comunicarse entre sí. Sin embargo, existen algunas aplicaciones en el que deben comunicar tareas, al igual que [los escenarios MPI](batch-mpi.md).

    Puede configurar un grupo para permitir la comunicación entre los nodos dentro de TI:**la comunicación entre nodos**. Cuando se habilita la comunicación entre nodos, los nodos de grupos de configuración de servicios de nube pueden comunicarse entre sí en puertos mayores que 1100 y grupos de configuración de máquina Virtual no restringen el tráfico en cualquier puerto.

    Observe que lo que permite la comunicación entre nodos también afecta a la colocación de los nodos de clústeres y podría limitar el número máximo de nodos en un grupo debido a las restricciones de implementación. Si la aplicación no requiere la comunicación entre nodos, el servicio de lote puede asignar un número potencialmente grande de nodos a la agrupación de varios clústeres diferentes y centros de datos para habilitar la mayor potencia de procesamiento en paralelo.

- **Iniciar tarea** de nodos computacionales.

    Opcional de *iniciar la tarea* se ejecuta en cada nodo como ese nodo une a la agrupación y cada vez que un nodo se reinicia o renovado. La tarea de inicio es especialmente útil para preparar los nodos de cálculo para la ejecución de las tareas, como la instalación de las aplicaciones que se ejecutan las tareas en los nodos de cálculo.

- **Paquetes de aplicaciones**

    Puede especificar los [paquetes de aplicaciones](#application-packages) para implementar en los nodos de cálculo en el grupo. Paquetes de aplicaciones proporcionan una implementación simplificada y control de versiones de las aplicaciones que se ejecutan las tareas. Paquetes de aplicaciones que se especifican para un grupo de servidores se instalan en cada nodo que se une a ese grupo, y cada vez que un nodo se reinicia o se rehace su imagen. Paquetes de aplicaciones actualmente no son compatibles en nodos computacionales Linux.

- **Configuración de red**

    Puede especificar el identificador de un Azure [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) en el que el grupo los nodos de cálculo debe crearse. Requisitos para especificar un VNet para el grupo de servidores se puede encontrar en [Agregar un grupo a una cuenta de] [ vnet] en la referencia de la API de REST de lote.

> [AZURE.IMPORTANT] Todas las cuentas de proceso por lotes tienen una predeterminada **cuota** que limita el número de **núcleos** (y por lo tanto, nodos de cómputo) en una cuenta de proceso por lotes. Puede encontrar las cuotas predeterminadas e instrucciones sobre cómo [aumentar la cuota](batch-quota-limit.md#increase-a-quota) (por ejemplo, el número máximo de núcleos en la cuenta de proceso por lotes) en [las cuotas y límites para el servicio de proceso de Azure](batch-quota-limit.md). Si usted se encuentra preguntando "¿por qué no mi grupo llegue a más de X nodos?" Esta cuota de núcleo puede ser la causa.

## <a name="job"></a>Trabajo

Un trabajo es una colección de tareas. Administra cómo realiza sus tareas en los nodos de cálculo en un grupo de cálculo.

- El trabajo especifica el **grupo** en el que el trabajo debe ejecutarse. Puede crear un nuevo grupo para cada trabajo, o utilizar un grupo para muchos trabajos. Puede crear un grupo para cada trabajo que está asociado con una programación de trabajo, o para todos los trabajos que están asociados con un trabajo programado.

- Puede especificar una **prioridad del trabajo**de opcional. Cuando se envía un trabajo con una prioridad mayor que los trabajos que están actualmente en curso, se insertan las tareas para el trabajo de mayor prioridad en la cola por delante de las tareas para las tareas de menor prioridad. Tareas en trabajos de menor prioridad que ya están ejecutando no son adelantadas.

- Puede utilizar **restricciones** de trabajo para especificar ciertos límites para los trabajos:

    Puede establecer un **tiempo máximo de wallclock**, por lo que si un trabajo se ejecuta durante más tiempo que el wallclock máximo especificado, se terminan el trabajo y todas sus tareas.

    Lote puede detectar y, a continuación, vuelva a intentar tareas fallidas. Puede especificar el **número máximo de reintentos de la tarea** como una restricción, incluso si una tarea es *siempre* o *nunca* vuelve a intentar. Volver a intentar una tarea significa que la tarea es devuelto a la cola para ejecutarse de nuevo.

- La aplicación cliente puede agregar tareas a una tarea, o puede especificar que una [tarea del administrador](#job-manager-task). Una tarea de administrador de trabajo contiene la información que es necesaria crear las tareas necesarias para un trabajo, con la tarea del Administrador de trabajo se ejecuta en uno de los nodos de cálculo en el grupo. La tarea de la tarea de administrador se controla específicamente por lotes--está en la cola en cuanto se crea el trabajo y se reinicia si se produce un error. Una tarea del administrador es *necesaria* para los trabajos que se crean mediante una [programación de trabajo](#scheduled-jobs) porque es la única manera de definir las tareas antes de que se crea una instancia de la tarea.

- De forma predeterminada, trabajos permanecen en estado activo cuando se hayan completado todas las tareas dentro del trabajo. Puede cambiar este comportamiento para que el trabajo finalice automáticamente cuando se hayan completado todas las tareas en el trabajo. Establecer propiedad de **onAllTasksComplete** de trabajo ([OnAllTasksComplete] [ net_onalltaskscomplete] en .NET por lotes) a *terminatejob* para terminar automáticamente el trabajo cuando todas sus tareas se encuentran en el estado completado.

    Tenga en cuenta que el servicio de lote considera un trabajo *sin* tareas que todos sus tareas completadas. Por lo tanto, esta opción se utiliza normalmente con una [tarea del administrador](#job-manager-task). Si desea utilizar la terminación automática del trabajo sin un administrador de trabajos, debe inicialmente establece la propiedad **onAllTasksComplete** de un nuevo trabajo en *noaction*y establézcalo en *terminatejob* sólo después de que haya terminado de agregar tareas a la tarea.

### <a name="job-priority"></a>Prioridad del trabajo

Puede asignar una prioridad a los trabajos que se crean en el lote. El servicio de proceso por lotes utiliza el valor de prioridad del trabajo para determinar el orden de la programación de trabajos dentro de una cuenta (Esto no es debe ser confundido con un [trabajo programado](#scheduled-jobs)). La prioridad de valores comprendidos entre -1000 y 1000, con-1000 la prioridad más baja y 1000 la más alta. La prioridad de un trabajo puede actualizar mediante la [actualización de las propiedades de un trabajo de] [ rest_update_job] operación (resto de lote) o mediante la modificación de la [CloudJob.Priority] [ net_cloudjob_priority] propiedad (lote. NET).

Dentro de la misma cuenta, trabajos de mayor prioridad tienen prioridad de programación a través de trabajos de menor prioridad. Un trabajo con un valor de prioridad más alta en una cuenta no tiene prioridad de programación sobre otro trabajo con un valor de menor prioridad en una cuenta diferente.

Programación a través de grupos de trabajo es independiente. Entre diferentes grupos, no se garantiza que se programa un trabajo de mayor prioridad en primer lugar si su grupo asociado está quedando sin nodos inactivos. En el mismo grupo, los trabajos con el mismo nivel de prioridad tienen la misma oportunidad de estar programado.

### <a name="scheduled-jobs"></a>Trabajos programados

[Programaciones de trabajo] [ rest_job_schedules] permiten crear trabajos periódicos en el servicio de proceso por lotes. Una programación de trabajo especifica cuándo ejecutar trabajos e incluye las especificaciones para los trabajos que se ejecutará. Puede especificar la duración de la programación, cuánto y cuando la programación está en vigor y la frecuencia durante ese período de tiempo que se deben crear trabajos.

## <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociado con un trabajo. Se ejecuta en un nodo. Las tareas se asignan a un nodo para su ejecución o se ponen en cola hasta que quede libre un nodo. En pocas palabras, una tarea ejecuta uno o más programas o secuencias de comandos en un nodo de cálculo para realizar el trabajo que necesita.

Cuando se crea una tarea, puede especificar:

- La **línea de comandos** de la tarea. Se trata de la línea de comandos que se ejecuta la aplicación o la secuencia de comandos en el nodo de cálculo.

    Es importante destacar que la línea de comandos no se ejecuta realmente en un shell. Por lo tanto, no puede tomar las características de shell como expansión de la [variable de entorno](#environment-settings-for-tasks) forma nativa (Esto incluye el `PATH`). Para sacar partido de estas características, se debe invocar el shell en la línea de comandos: por ejemplo, con el lanzamiento de `cmd.exe` en nodos de Windows o `/bin/sh` en Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Si las tareas que necesite ejecutar una aplicación o un script que no está en el nodo `PATH` o hacer referencia a variables de entorno, invocar el shell explícitamente en la línea de comandos de la tarea.

- **Archivos de recursos** que contienen los datos que se procesan. Estos archivos se copian automáticamente en el nodo de almacenamiento Blob en una cuenta de almacenamiento de Azure de **Propósito General** antes de que se ejecuta la línea de comandos de la tarea. Para obtener más información, consulte las secciones de [tareas de inicio](#start-task) y [los archivos y directorios](#files-and-directories).

- Las **variables de entorno** que son requeridos por su aplicación. Para obtener más información, consulte la sección [Configuración del entorno para las tareas](#environment-settings-for-tasks) .

- Las **restricciones** que debe ejecutar la tarea. Por ejemplo, el tiempo máximo que la tarea se puede ejecutar, el número máximo de veces que debe reintentarse una tarea fallida, y el máximo de tiempo que los archivos en el directorio de trabajo de la tarea se conservan.

- **Paquetes de aplicaciones** para implementar en el nodo de cálculo en la que la tarea está programada para ejecutarse. [Paquetes de aplicaciones](#application-packages) proporcionan una implementación simplificada y control de versiones de las aplicaciones que se ejecutan las tareas. Paquetes de aplicaciones de nivel de tarea son especialmente útiles en entornos de pool compartido, donde los distintos trabajos se ejecutan en un grupo y el grupo no se elimina cuando se completa un trabajo. Si el trabajo tiene menos tareas de nodos en el grupo, paquetes de aplicaciones de tarea pueden minimizar la transferencia de datos ya que la aplicación se implementa sólo en los nodos que ejecutan tareas.

Además de las tareas que definen para realizar cálculos en un nodo, también se proporcionan las siguientes tareas especiales por el servicio de proceso por lotes:

- [Iniciar tarea](#start-task)
- [El administrador tarea](#job-manager-task)
- [Tareas de preparación y lanzamiento](#job-preparation-and-release-tasks)
- [Tareas varias instancias (MPI)](#multi-instance-tasks)
- [Dependencias entre tareas](#task-dependencies)

### <a name="start-task"></a>Iniciar tarea

Mediante la asociación de una **tarea de inicio** con un grupo, puede preparar el entorno operativo de sus nodos. Por ejemplo, puede realizar acciones como instalar las aplicaciones que se ejecutan las tareas e iniciar procesos en segundo plano. La tarea de inicio se ejecuta cada vez que se inicia un nodo para mientras permanece en el grupo, incluso cuando el nodo es el primero agrega a la piscina y cuando se reinicia o renovado.

Una de las ventajas de la tarea de inicio es que puede contener toda la información necesaria configurar un nodo de cálculo e instalar las aplicaciones necesarias para la ejecución de la tarea. Por lo tanto, aumenta el número de nodos en un grupo es tan simple como especificar el nuevo número de nodos de destino--lote ya tiene la información necesaria para configurar los nuevos nodos y prepararlos para aceptar tareas.

Como con cualquier tarea por lotes de Azure, puede especificar una lista de **archivos** de recursos de [Almacenamiento de Azure][azure_storage], además de una **línea de comandos** que se ejecutará. Lote copia primero los archivos de recursos al nodo de almacenamiento de Azure y, a continuación, ejecuta la línea de comandos. Para una tarea de inicio del grupo, la lista de archivos normalmente contiene la aplicación de tareas y sus dependencias.

Sin embargo, también puede incluir datos de referencia para ser utilizado por todas las tareas que se ejecutan en el nodo de cálculo. Por ejemplo, podría realizar la línea de comandos de inicio de la tarea un `robocopy` operación para copiar los archivos de aplicación (que se han especificado como archivos de recursos y descargado en el nodo) de [directorio de trabajo de la tarea de inicio](#files-and-directories) a la [carpeta compartida](#files-and-directories)y, a continuación, ejecute un archivo MSI o `setup.exe`.

> [AZURE.IMPORTANT] Lote actualmente *sólo* es compatible con el tipo de cuenta de almacenamiento de información de **Propósito General** , como se describe en el paso 5 de [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Las tareas por lotes (incluyendo tareas estándar, tareas de inicio, las tareas de preparación de trabajos y tareas de lanzamiento) deben especificar los archivos de recursos que residen *sólo* en cuentas de almacenamiento de **Propósito General** .

Es generalmente deseable para el servicio de lote debe esperar para que la tarea de inicio se complete antes de considerar el nodo preparado para ser asignado a las tareas, pero puede configurarlo.

Si se produce un error en una tarea de inicio en un nodo de cálculo, a continuación, se actualiza el estado del nodo para reflejar el error y el nodo no está asignado a ninguna tarea. Una tarea de inicio puede fallar si hay un problema al copiar sus archivos de recursos de almacenamiento de información, o si el proceso ejecutado por su línea de comandos devuelve un código de salida distinto de cero.

Si Agregar o actualizar la tarea de inicio para un grupo *existente* , deberá reiniciar sus nodos de cálculo para la tarea de inicio que se aplicará a los nodos.

### <a name="job-manager-task"></a>El administrador tarea

Se suele utilizar una **tarea de administrador** al control o supervisar la ejecución del trabajo, por ejemplo, para crear y enviar las tareas de un trabajo, determinan tareas adicionales para ejecutar y determinar cuándo el trabajo está completo. Sin embargo, una tarea de la tarea de administrador no está restringido a estas actividades. Es una tarea de toda regla que puede realizar todas las acciones que se requieren para el trabajo. Por ejemplo, una tarea de la tarea de administrador podría descargar un archivo que se especifica como un parámetro, analizar el contenido de ese archivo y enviar tareas adicionales según dicho contenido.

Se inicia una tarea de la tarea de administrador antes de todas las demás tareas. Proporciona las siguientes características:

- Automáticamente se envía como una tarea por el servicio de proceso por lotes cuando se crea el trabajo.

- Se programa para ejecutar antes de las demás tareas en un trabajo.

- Su nodo asociado es el último que se quita de un grupo cuando el grupo es que se producen reducciones.

- Su terminación puede estar vinculada a la finalización de todas las tareas en el trabajo.

- Una tarea de trabajo administrador recibe la prioridad más alta cuando necesita que se reinicie. Si el nodo inactivo no está disponible, el servicio de lote podría terminar una de las otras tareas en ejecución en el grupo para hacer espacio para ejecutar la tarea de administrador de trabajos.

- Una tarea de la tarea de administrador en un trabajo no tienen prioridad sobre las tareas de otros trabajos. A través de trabajos, se observan sólo las prioridades de nivel de trabajo.

### <a name="job-preparation-and-release-tasks"></a>Tareas de preparación y lanzamiento

Lote proporciona tareas de preparación para la instalación de la ejecución del trabajo previo. Lanzamiento de tareas son de limpieza o de mantenimiento posterior a la tarea.

- **Tareas de preparación del trabajo**: una tarea de la tarea de preparación se ejecuta en todos los nodos de cálculo que están programados para ejecutar tareas, antes de que cualquiera de las tareas se ejecutan. Puede utilizar una tarea de la tarea de preparación para copiar los datos que es compartidos por todas las tareas, pero que es único para el trabajo, por ejemplo.
- **Versión de tarea**: cuando haya finalizado un trabajo, una tarea de la versión de trabajo se ejecuta en cada nodo del grupo de que se ejecuta al menos una tarea. Puede utilizar una tarea de la tarea de lanzamiento para eliminar datos que se copian mediante la tarea de preparación del trabajo, o para comprimir y cargar datos en el registro de diagnóstico, por ejemplo.

Ambos trabajos de preparación y lanzamiento tareas le permiten especificar una línea de comandos para ejecutar cuando se invoca la tarea. Ofrecen características como la descarga de archivos, ejecución con privilegios elevados, variables de entorno personalizadas, duración de ejecución máximo, número de reintentos y tiempo de retención de archivo.

Para obtener más información sobre las tareas de preparación y el lanzamiento de trabajo, consulte [tareas de preparación y finalización del trabajo de ejecución en lotes de Azure nodos de cálculo](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarea de instancias múltiples

Una [tarea de varias instancias](batch-mpi.md) es una tarea que está configurada para ejecutar simultáneamente en más de un nodo de cálculo. Con tareas de múltiples instancias, puede habilitar escenarios de computación de alto rendimiento que requieren un grupo de nodos de cálculo se asignan juntos para procesar una carga de trabajo individual (como el paso de mensajes interfaz (MPI)).

Para obtener información detallada sobre cómo ejecutar trabajos MPI en lotes mediante la biblioteca de .NET por lotes, consulte [tareas de varias instancias de uso para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) en lote de Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependencias entre tareas

[Las dependencias entre tareas](batch-task-dependencies.md), como el nombre implica, permiten especificar que una tarea depende de la finalización de otras tareas antes de su ejecución. Esta función proporciona compatibilidad para las situaciones en que una tarea "descendente" consume el resultado de una tarea "ascendente", o cuando una tarea ascendente realiza alguna inicialización requerida por una tarea de nivel inferior. Para utilizar esta característica, debe habilitar primero las dependencias entre tareas en su trabajo por lotes. Después, para cada tarea que depende de otro (o muchos otros), especifique las tareas que depende de esa tarea.

Con las dependencias entre tareas, puede configurar escenarios como los siguientes:

* *taskB* depende de *taskA* (*taskB* no iniciará su ejecución hasta que haya finalizado la *taskA* ).
* *taskC* depende de *taskA* y *taskB*.
* *taskD* depende de una variedad de tareas, como las tareas *1* a *10*, antes de que ejecute.

Comprobar las [dependencias entre tareas en el lote de Azure](batch-task-dependencies.md) y la [TaskDependencies] [ github_sample_taskdeps] código de ejemplo en las [muestras del lote de azure] [ github_samples] repositorio de GitHub para más información detallada acerca de esta característica.

## <a name="environment-settings-for-tasks"></a>Configuración del entorno de tareas

Cada tarea ejecutada por el servicio de lote tiene acceso a las variables de entorno que se establece en los nodos de cálculo. Esto incluye las variables de entorno definidas por el servicio de proceso por lotes ([definidos por el servicio de][msdn_env_vars]) y las variables de entorno personalizado que puede definir para las tareas. Las aplicaciones y secuencias de comandos que se ejecutan las tareas tienen acceso a estas variables de entorno durante la ejecución.

Puede establecer variables de entorno personalizadas en el nivel de tarea o trabajo rellenando la propiedad de *Configuración del entorno* para estas entidades. Por ejemplo, vea [Agregar una tarea a un trabajo] [ rest_add_task] operación (lote REST API), o la [CloudTask.EnvironmentSettings] de[ net_cloudtask_env] y [CloudJob.CommonEnvironmentSettings] [ net_job_env] propiedades en .NET por lotes.

La aplicación de cliente o servicio puede obtener variables de entorno de una tarea, definido por el servicio y personalizadas, utilizando [obtener información acerca de una tarea] [ rest_get_task_info] operación (resto de lote) o mediante el acceso a la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propiedad (lote. NET). Procesos que se ejecutan en un nodo de cálculo pueden tener acceso a estas y otras variables de entorno en el nodo, por ejemplo, utilizando el familiar `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` sintaxis (Linux).

Encontrará una lista completa de todas las variables de entorno definidas por el servicio en [calcular las variables de entorno del nodo][msdn_env_vars].

## <a name="files-and-directories"></a>Archivos y directorios

Cada tarea tiene un *directorio de trabajo* en el que crea archivos y directorios de cero o más. Este directorio de trabajo puede utilizarse para almacenar el programa que se ejecuta la tarea, los datos que procesa y el resultado del procesamiento que se realiza. Todos los archivos y directorios de una tarea son propiedad del usuario de la tarea.

El servicio de lote expone una parte del sistema de archivos en un nodo como el *directorio raíz*. Las tareas pueden acceder al directorio de raíz mediante una referencia a la `AZ_BATCH_NODE_ROOT_DIR` la variable de entorno. Para obtener más información acerca de cómo utilizar variables de entorno, consulte [Configuración del entorno para las tareas](#environment-settings-for-tasks).

El directorio raíz contiene la siguiente estructura de directorios:

![Calcular la estructura de directorios de nodo][1]

- **compartido**: este directorio proporciona acceso de lectura y escritura a *todas las* tareas que se ejecutan en un nodo. Cualquier tarea que se ejecuta en el nodo puede crear, leer, actualizar y eliminar archivos en este directorio. Las tareas pueden acceder a este directorio haciendo referencia a la `AZ_BATCH_NODE_SHARED_DIR` la variable de entorno.

- **Inicio**: una tarea de inicio utiliza este directorio como directorio de trabajo. Aquí se almacenan todos los archivos que se descargan en el nodo de la tarea de inicio. La tarea de inicio puede crear, leer, actualizar y eliminar archivos en este directorio. Las tareas pueden acceder a este directorio haciendo referencia a la `AZ_BATCH_NODE_STARTUP_DIR` la variable de entorno.

- **Tareas**: se crea un directorio para cada tarea que se ejecuta en el nodo. Se accede haciendo referencia a la `AZ_BATCH_TASK_DIR` la variable de entorno.

    Dentro de cada directorio de la tarea, el servicio de proceso por lotes crea un directorio de trabajo (`wd`) cuya ruta de acceso única es especificado por el `AZ_BATCH_TASK_WORKING_DIR` la variable de entorno. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio. Este directorio se mantiene basado en la restricción de *RetentionTime* especificado para la tarea.

    `stdout.txt`y `stderr.txt`: estos archivos se escriben en la carpeta de tareas durante la ejecución de la tarea.

>[AZURE.IMPORTANT] Cuando se quita un nodo del grupo, se quitan *todos* los archivos que se almacenan en el nodo.

## <a name="application-packages"></a>Paquetes de aplicaciones

La característica de [paquetes de aplicaciones](batch-application-packages.md) proporciona fácil administración e implementación de aplicaciones a los nodos de cálculo en los grupos. Puede cargar y administrar varias versiones de las aplicaciones que ejecuta las tareas, incluidos sus archivos binarios y archivos de soporte técnico. A continuación, puede desplegar manualmente uno o más de estas aplicaciones en los nodos de cálculo en la agrupación.

Puede especificar los paquetes de aplicaciones en el nivel de grupo y tarea. Al especificar los paquetes de aplicaciones de grupo, la aplicación se implementa en todos los nodos en el grupo. Especificar paquetes de solicitud de tarea, la aplicación se implementa sólo a los nodos que están programados para ejecutarse al menos una de las tareas del proyecto, justo antes de que se ejecute la línea de comandos de la tarea.

Lote controla los detalles del trabajo con el almacenamiento de Azure para almacenar los paquetes de aplicaciones e implementarlas para nodos de cálculo, por lo que puede simplificar el código y la administración de overhead.

Para obtener más información acerca de la característica de paquete de la aplicación, consulte [implementación de aplicaciones con paquetes de aplicaciones de proceso de Azure](batch-application-packages.md).

>[AZURE.NOTE] Si agrega paquetes de aplicaciones de grupo a un grupo *existente* , debe reiniciar sus nodos de cómputo para los paquetes de aplicaciones que se distribuirán a los nodos.

## <a name="pool-and-compute-node-lifetime"></a>Duración de nodo de grupo y compute

Cuando diseñe la solución de lotes de Azure, tiene que tomar una decisión de diseño sobre cómo y cuando se crean grupos y calculan cuánto nodos dentro de esos grupos se mantienen a disposición.

En un extremo del espectro, puede crear un grupo para cada trabajo que se envía y elimine el grupo tan pronto como finalizado su ejecución. Esto maximiza la utilización porque sólo se asignan los nodos cuando sea necesario y apagar tan pronto como están inactivos. Aunque esto significa que el trabajo debe esperar a que los nodos se asignen, es importante tener en cuenta que las tareas se programan para su ejecución en cuanto los nodos están disponibles individualmente, asignados, y ha completado la tarea de inicio. Por lotes hace *no* espera hasta que todos los nodos dentro de un grupo están disponibles antes de asignar tareas a los nodos. Esto garantiza la utilización máxima de todos los nodos disponibles.

En el otro extremo del espectro, si tener trabajos iniciar inmediatamente es la prioridad más alta, puede crear un grupo con anterioridad y disponer de sus nodos antes de envían los trabajos. En este escenario, las tareas se pueden iniciar inmediatamente, pero nodos podrían permanecen inactivos mientras se espera que se asignará.

Normalmente se utiliza un enfoque combinado para el tratamiento de una variable, pero constante, de carga. Puede tener un grupo que se envían a varios trabajos, pero se pueden escalar el número de nodos hacia arriba o hacia abajo según el trabajo de carga (consulte [recursos computacionales se escala](#scaling-compute-resources) en la sección siguiente). Puede hacerlo de manera reactiva, basado en la carga actual, o de forma proactiva, si se puede predecir la carga.

## <a name="scaling-compute-resources"></a>Escalar recursos de cálculo

Con [la escala automática](batch-automatic-scaling.md), puede tener el servicio por lotes ajustar dinámicamente el número de nodos de cálculo en un grupo según el uso actual de carga de trabajo y recursos de su escenario de compute. Esto le permite reducir el coste total de la ejecución de la aplicación utilizando sólo los recursos que necesita, y los que no es necesario liberar a.

Habilita la escala automática escribiendo una [fórmula de ajuste de escala automático](batch-automatic-scaling.md#automatic-scaling-formulas) y asociar esa fórmula a un grupo. El servicio de proceso por lotes utiliza la fórmula para determinar el número de destino de los nodos en el grupo para el siguiente intervalo de escala (un intervalo en el que puede configurar). Puede especificar la configuración de escala automática de un grupo cuando se crea o activar más tarde la escala en un grupo. También puede actualizar la configuración de escala de un grupo habilitado para escalar.

Por ejemplo, quizás un trabajo requiere que envíe a un gran número de tareas que deben ejecutarse. Puede asignar una fórmula escala al grupo que ajusta el número de nodos del grupo basado en el número actual de tareas en cola y la tasa de finalización de las tareas en el trabajo. El servicio de proceso por lotes periódicamente evalúa la fórmula y cambia el tamaño del grupo, basado en la carga de trabajo (agregar nodos para muchas tareas en cola y quitar nodos de ninguna tarea en cola o se está ejecutando) y la configuración de fórmulas.

Una fórmula de escala se puede basar en las siguientes métricas:

- **Métricas de tiempo** se basan en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos** se basan en el uso de CPU, uso de ancho de banda, uso de memoria y número de nodos.

- **Métricas de tarea** se basan en el estado de la tarea, como *activo* (en cola), *ejecución*o *completado*.

Cuando la escala automática, reduce el número de nodos de cálculo en un grupo, debe considerar cómo controlar las tareas que se ejecutan en el momento de la operación de disminución. Para ello, lote proporciona una *opción de desasignación de nodo* que puede incluir en las fórmulas. Por ejemplo, puede especificar que las tareas en ejecución se detiene inmediatamente, detenerse inmediatamente y devuelto a la cola para su ejecución en otro nodo o puede finalizar antes de que el nodo se quita de la agrupación.

Para obtener más información sobre la escala automáticamente una aplicación, vea [automáticamente escala nodos de cálculo en un grupo de lotes de Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Para maximizar la utilización de los recursos computacionales, establezca el número de destino de nodos a cero al final de un trabajo, pero permiten ejecutar tareas finalicen.

## <a name="security-with-certificates"></a>Seguridad con certificados

Normalmente debe utilizar certificados al cifrar o descifrar información confidencial para tareas, como la clave para una [cuenta de almacenamiento de Azure][azure_storage]. Para ello, puede instalar certificados en los nodos. Secretos cifrados se pasan a las tareas a través de parámetros de línea de comandos o incrustados en uno de los recursos de la tarea, y los certificados instalados pueden utilizarse para descifrar los archivos.

Utilice el [complemento certificados] [ rest_add_cert] operación (resto de lote) o [CertificateOperations.CreateCertificate] [ net_create_cert] método (lote. NET) para agregar un certificado a una cuenta de proceso por lotes. Se puede asociar el certificado a un grupo nuevo o existente. Cuando un certificado está asociado a un grupo, el servicio de lote instala el certificado en cada nodo del grupo. El servicio de lote instala los certificados apropiados cuando se inicia el nodo, antes de iniciar las tareas (incluidas las tareas de inicio y trabajo manager).

Si agrega certificados a un grupo *existente* , debe reiniciar sus nodos de cómputo para los certificados que se aplicará a los nodos.

## <a name="error-handling"></a>Control de errores

Puede resultarle necesarios para controlar los errores de la tarea y aplicación dentro de la solución de lotes.

### <a name="task-failure-handling"></a>Control de errores de la tarea
Fallos de tareas se dividen en las siguientes categorías:

- **Errores de programación**

    Si se produce un error en la transferencia de archivos que se especifican para una tarea por cualquier motivo, se establece un "error de programación" para la tarea.

    Errores de programación se puede producir si ha movido los archivos de recursos de la tarea, la cuenta de almacenamiento ya no está disponible o se encontró otro problema que impide la correcta copia de archivos en el nodo.

- **Errores de aplicación**

    El proceso que se especifica mediante la línea de comandos de la tarea también puede fallar. Se considera que el proceso que ha fallado cuando se devuelve un código de salida distinto de cero por el proceso que se ejecuta la tarea (consulte *los códigos de salida de tareas* en la sección siguiente).

    Para errores de aplicación, puede configurar por lotes para reintentar automáticamente la tarea hasta un número especificado de veces.

- **Errores de restricción**

    Puede establecer una restricción que especifica la duración de ejecución máximo para un trabajo o tarea, el *maxWallClockTime*. Esto puede ser útil para terminar tareas "colgado".

    Cuando se excedió la cantidad máxima de tiempo, se marca la tarea como *completada*, pero el código de salida se establece en `0xC000013A` y el campo *schedulingError* se marca como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Depurar errores de aplicación

- `stderr`y`stdout`

    Durante la ejecución, una aplicación podría producir resultados de diagnóstico que puede utilizar para solucionar problemas. Como se mencionó en la sección anterior, [los archivos y directorios](#files-and-directories), el servicio de proceso por lotes escribe salida estándar y salida de error estándar para `stdout.txt` y `stderr.txt` archivos en el directorio de la tarea en el nodo de cálculo. Puede utilizar el portal de Azure o uno de los SDK de proceso por lotes para descargar estos archivos. Por ejemplo, puede recuperar estos y otros archivos para solucionar problemas mediante el uso de [ComputeNode.GetNodeFile] [ net_getfile_node] y [CloudTask.GetNodeFile] [ net_getfile_task] en la biblioteca de .NET por lotes.

- **Códigos de salida de la tarea**

    Como se mencionó anteriormente, una tarea está marcada como errónea por el servicio de proceso por lotes si el proceso que se ejecuta la tarea devuelve un código de salida distinto de cero. Cuando una tarea ejecuta un proceso, por lotes rellena la propiedad de código de salida de la tarea con el *código del proceso de devolución*. Es importante destacar que el código de salida de la tarea es **no** determinado por el servicio de lote--está determinada por el propio proceso o el sistema operativo en el que se ejecuta el proceso.

### <a name="accounting-for-task-failures-or-interruptions"></a>Contabilización de interrupciones o de errores de la tarea

Tareas de vez en cuando pueden fallar o se interrumpa. Podría fallar la propia aplicación de tareas, podría reiniciarse el nodo en el que se ejecuta la tarea o el nodo podría quitarse de la piscina durante una operación de cambio de tamaño si se establece la directiva de desasignación de la piscina para quitar los nodos inmediatamente sin esperar tareas finalicen. En todos los casos, la tarea puede ser automáticamente devuelto a la cola por lotes para su ejecución en otro nodo.

También es posible que un problema intermitente hacer que una tarea se colgara o tardan demasiado en ejecutarse. Puede establecer el tiempo máximo de ejecución de una tarea. Si se supera, por lotes interrumpe la aplicación de la tarea.

### <a name="connecting-to-compute-nodes"></a>Conectando con nodos de cálculo

Puede realizar la depuración y solución de problemas de inicio de sesión en un nodo de cálculo remotamente adicionales. Puede utilizar el portal de Azure para descargar un archivo de protocolo de escritorio remoto (RDP) para los nodos de Windows y obtener información de conexión de Secure Shell (SSH) para los nodos de Linux. También puede hacerlo mediante las API de lote--por ejemplo, con [.NET de lote] [ net_rdpfile] o [Lote Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Para conectarse a un nodo a través de RDP o SSH, primero debe crear un usuario en el nodo. Para ello, puede utilizar Azure portal, [Agregar una cuenta de usuario a un nodo] [ rest_create_user] utilizando la API de REST de lote, llame a la [ComputeNode.CreateComputeNodeUser] [ net_create_user] método en .NET por lotes o llamada la [add_user] [ py_add_user] método en el módulo de Python por lotes.

### <a name="troubleshooting-bad-compute-nodes"></a>Solución de problemas "mala" nodos de cálculo

En situaciones donde algunas de las tareas no se superan, su aplicación cliente de proceso por lotes o servicio puede examinar los metadatos de las tareas con errores para identificar un nodo que. Cada nodo de un grupo recibe un identificador único y el nodo en el que se ejecuta una tarea se incluye en los metadatos de la tarea. Cuando haya identificado un nodo del problema, puede realizar varias acciones con él:

- **Reinicie el nodo** ([REST][rest_reboot] | [.NET][net_reboot])

    Reinicie el nodo a veces puede aclarar problemas latentes como procesos atascados o se ha bloqueado. Tenga en cuenta que si el grupo utiliza una tarea de inicio o el trabajo utiliza una tarea de la tarea de preparación, se ejecutan cuando se reinicie el nodo.

- **Crear una nueva imagen del nodo** ([REST][rest_reimage] | [.NET][net_reimage])

    Esto vuelve a instalar el sistema operativo en el nodo. Al igual que con reiniciar un nodo, iniciar las tareas y se vuelven a ejecutar las tareas de preparación del trabajo después de que el nodo se ha renovado.

- **Quitar el nodo del grupo** ([REST][rest_remove] | [.NET][net_remove])

    A veces es necesario quitar completamente el nodo del grupo.

- **Desactivar la tarea de programación en el nodo** ([REST][rest_offline] | [.NET][net_offline])

    Esto efectivamente tiene el nodo "desconectado" por lo que no hay más tareas se asignan a él, pero permite que el nodo permanezca ejecución y en el grupo. Esto le permite ejecutar una vez investigado la causa de los errores sin perder datos de tareas fallidas--y sin el nodo, lo que provoca errores de tareas adicionales. Por ejemplo, puede deshabilitar la tarea de programación en el nodo, a continuación, [iniciar sesión en forma remota](#connecting-to-compute-nodes) para examinar los registros de sucesos de un nodo o solucionar otros problemas. Cuando termine la investigación, a continuación, puede traer en línea el nodo habilitando la programación de tareas ([resto][rest_online] | [.NET][net_online]), o realizar una de las otras acciones que se explicó anteriormente.

> [AZURE.IMPORTANT] Con cada acción que se describe en esta sección, reinicio, crear una nueva imagen, quitar y deshabilitar la programación de tareas, es posible especificar cómo se controlan las tareas en ejecución actualmente en el nodo al realizar la acción. Por ejemplo, cuando se deshabilita la programación de tareas en un nodo utilizando la biblioteca de cliente .NET de lote, puede especificar un [DisableComputeNodeSchedulingOption] de[ net_offline_option] valor de enumeración para especificar si al **Terminar** las tareas en ejecución, **poner** ellos para la programación en otros nodos, o permitir la ejecución de tareas para completar antes de realizar la acción (**TaskCompletion**).

## <a name="next-steps"></a>Próximos pasos

- Recorra una aplicación por lotes de ejemplo paso a paso de [Introducción a la biblioteca por lotes de Azure para .NET](batch-dotnet-get-started.md). También hay una [versión de Python](batch-python-tutorial.md) del tutorial que se ejecuta una carga de trabajo en nodos computacionales Linux.

- Descargar y generar los [Lotes Explorer] [ github_batchexplorer] proyecto de ejemplo para su uso mientras desarrolla sus soluciones de proceso por lotes. Mediante el Explorador de proceso por lotes, puede realizar lo siguiente y mucho más:
  - Supervisar y manipular grupos, trabajos y tareas dentro de la cuenta de proceso por lotes
  - Descargar `stdout.txt`, `stderr.txt`y otros archivos de nodos
  - Crear usuarios en nodos y descargar archivos RDP para conexiones remotas

- Aprenda cómo [crear grupos de nodos computacionales Linux](batch-linux-nodes.md).

- Visite el [foro de Azure lote] [ batch_forum] en MSDN. El foro es un buen lugar para hacer preguntas, si sólo están aprendiendo o es un experto en el uso de lotes.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
