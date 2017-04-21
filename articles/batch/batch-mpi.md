<properties
    pageTitle="Ejecutar aplicaciones de MPI en Azure lote con tareas múltiples instancias | Microsoft Azure"
    description="Obtenga información sobre cómo ejecutar aplicaciones de interfaz de paso de mensajes (MPI) utilizando el tipo de tarea de varias instancias en el lote de Azure."
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

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Utilice tareas de varias instancias para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) en lote de Azure

Tareas varias instancias permiten ejecutar una tarea por lotes de Azure en varios nodos de cálculo simultáneamente. Estas tareas permiten escenarios como aplicaciones de interfaz de paso de mensajes (MPI) en lotes de cómputo de alto rendimiento. En este artículo, aprenderá a ejecutar tareas múltiples instancias utilizando el [Lote .NET] [ api_net] library.

>[AZURE.NOTE] Aunque los ejemplos en este artículo se centran en lote. NET, MS-MPI y nodos de cálculo de Windows, los conceptos de tarea multinstancia tratado aquí son aplicables a otras plataformas y tecnologías (Python y MPI Intel en nodos de Linux, por ejemplo).

## <a name="multi-instance-task-overview"></a>Resumen de la tarea de múltiples instancias

De lote, cada tarea suele ser ejecutado en un nodo de cálculo único--enviar varias tareas para un trabajo y el servicio de lote programa cada tarea para su ejecución en un nodo. Sin embargo, al configurar de una tarea **configuración de varias instancias**, saber por lotes para crear en su lugar una tarea principal y varias subtareas que se ejecutan en varios nodos.

![Resumen de la tarea de múltiples instancias][1]

Cuando envíe una tarea con la configuración de varias instancias a un trabajo, lote realiza varios pasos específicos para las tareas de varias instancias:

1. El servicio por lotes crea una **principal** y varias **subtareas** se basan en la configuración de varias instancias. El número total de tareas (principales más todas las subtareas) coincide con el número de **instancias** (nodos de cálculo) que especifique en la configuración de varias instancias.
1. Lote designa uno de los nodos de cálculo como **principal**y programa la tarea principal para ejecutar en el patrón. Programar las subtareas para ejecutar en el resto de los nodos de cálculo asignado a la tarea de varias instancias, una subtarea por nodo.
1. El principal y todas las subtareas descargar los **archivos de recursos comunes** que especifique en la configuración de varias instancias.
1. Después el recurso común archivos se han descargado, el primario y subtareas ejecutan el **comando de coordinación** que se especifica en la configuración de varias instancias. Normalmente se utiliza el comando de coordinación para preparar los nodos para la ejecución de la tarea. Esto puede incluir a partir de servicios en segundo plano (como [Microsoft MPI][msmpi_msdn]de `smpd.exe`) y comprobar que los nodos están listos para procesar mensajes entre nodos.
1. La tarea principal, ejecuta el **comando de la aplicación** en el nodo maestro *después de que* el comando de coordinación se ha realizado correctamente, el principal y todas las subtareas. El comando de la aplicación es la línea de comandos de la propia tarea de varias instancias y es ejecutado por la tarea principal. En un [MS-MPI][msmpi_msdn]-solución, basada en esto es donde se ejecuta la aplicación MPI habilitado mediante `mpiexec.exe`.

> [AZURE.NOTE] Aunque es funcionalmente distinto, la tarea de varias instancias de"" no es un tipo de tarea único como el [StartTask] [ net_starttask] o [JobPreparationTask][net_jobprep]. La tarea de varias instancias es simplemente una tarea por lotes estándar ([CloudTask] [ net_task] en .NET de lote) cuyas instancias múltiples se ha configurado. En este artículo, nos referimos a esto como la **tarea de varias instancias**.

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para las tareas de varias instancias

Instancias múltiples tareas que requieren un pool con **habilitada la comunicación entre nodo**y con la **ejecución de tareas simultáneas deshabilitada**. Si intenta ejecutar una tarea de varias instancias en un grupo con la comunicación entre nodos deshabilitada o con un valor *maxTasksPerNode* mayor que 1, la tarea no se programará nunca--permanecerá indefinidamente en el estado "activo". Este fragmento de código muestra la creación de un grupo de este tipo utilizando la biblioteca de .NET por lotes.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Además, tareas múltiples instancias pueden ejecutar *sólo* en los nodos en **grupos que se crearon después de 14 de diciembre de 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Utilice un StartTask para instalar MPI

Para ejecutar aplicaciones de MPI con una tarea de varias instancias, primero necesitará instalar una implementación de MPI (MS-MPI o MPI Intel, por ejemplo) en los nodos de cálculo en el grupo. Esto es un buen momento para utilizar un [StartTask][net_starttask], que se ejecuta siempre que un nodo se une a un grupo o se reinicie. Este fragmento de código crea un StartTask que especifica el paquete de instalación de MS-MPI como un [archivo de recursos][net_resourcefile]. Línea de comandos de inicio de la tarea se ejecuta una vez descargado el archivo de recursos al nodo. En este caso, la línea de comandos realiza una instalación desatendida de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remoto acceso directo a memoria (RDMA)

Cuando elige un [tamaño capaz de RDMA](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) como A9 para los nodos de cálculo en el grupo de lotes, la aplicación de MPI puede beneficiarse de red de access (RDMA) de alto rendimiento, baja latencia memoria directo remoto de Azure.

Busque los tamaños especificados como "RDMA capaz" en los siguientes artículos:

* Grupos de **CloudServiceConfiguration**

  * [Tamaños de los servicios de nube](../cloud-services/cloud-services-sizes-specs.md) (Sólo Windows)

* Grupos de **VirtualMachineConfiguration**

  * [Tamaños de máquinas virtuales en Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Tamaños de máquinas virtuales en Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Para aprovechar las ventajas de RDMA en [Linux nodos de cálculo](batch-linux-nodes.md), debe utilizar **MPI Intel** en los nodos. Para obtener más información acerca de los grupos CloudServiceConfiguration y VirtualMachineConfiguration, consulte la sección [Pool](batch-api-basics.md#Pool) de introducción a las características del lote.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Crear una tarea de varias instancias con .NET por lotes

Ahora que hemos analizado los requisitos de grupo y la instalación del paquete MPI, vamos a crear la tarea de varias instancias. En este fragmento de código, creamos un estándar [CloudTask][net_task], a continuación, configure su [MultiInstanceSettings] [ net_multiinstance_prop] propiedad. Como se mencionó anteriormente, la tarea de múltiples instancias no es un tipo de tareas distintas, pero una tarea por lotes estándar configurado con la configuración de varias instancias.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Las subtareas y tareas principales

Cuando se crea la configuración de varias instancias de una tarea, especifique el número de nodos de cálculo que se ejecuta la tarea. Al enviar la tarea a un trabajo, el servicio de proceso por lotes crea una tarea **principal** y suficiente **subtareas** que juntos coincide con el número de nodos especificada.

Estas tareas se asignan un identificador entero en el intervalo de 0 a *numberOfInstances* - 1. La tarea con el identificador 0 es la tarea principal y todos los demás identificadores son subtareas. Por ejemplo, si crea la siguiente configuración de varias instancias de una tarea, la tarea principal tendría un identificador de 0 y las subtareas tendría ID 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nodo maestro

Cuando se envía una tarea de varias instancias, el servicio de lote designa uno de los nodos de cálculo como el nodo "maestro" y programa la tarea principal para ejecutar en el nodo maestro. Las subtareas se programan para ejecutarse en el resto de los nodos asignado a la tarea de varias instancias.

## <a name="coordination-command"></a>Comando de coordinación

Se ejecuta el **comando de coordinación** por ambos el primario y subtareas.

Está bloqueando la invocación del comando coordinación--por lotes no ejecutan el comando de la aplicación hasta que el comando de coordinación ha devuelto correctamente para todas las subtareas. El comando de coordinación, por tanto, debe iniciar los servicios de fondo requerido, compruebe que estén listas para su uso y salga. Por ejemplo, este comando de coordinación para una solución con la versión de MS-MPI 7 inicia el servicio SMPD en el nodo, a continuación, sale:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe el uso de `start` en este comando de coordinación. Esto es necesario porque la `smpd.exe` aplicación no devuelve inmediatamente después de la ejecución. Sin necesidad de utilizar el [Inicio] de[ cmd_start] de comandos, este comando de coordinación no devolvía y, por tanto, bloquearía la ejecución del comando aplicación.

## <a name="application-command"></a>Comando de aplicación

Una vez que han terminado la tarea primaria y todas las subtareas de ejecutar el comando de coordinación, línea de comandos de la tarea de varias instancias es ejecutado por la tarea principal *sólo*. Llamamos a esto el **comando de la aplicación** para distinguirlo del comando de coordinación.

Para las aplicaciones de MS-MPI, utilice el comando de la aplicación para ejecutar la aplicación MPI habilitado con `mpiexec.exe`. Por ejemplo, aquí es un comando de aplicación para una solución mediante MS-MPI versión 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Como MS-MPI `mpiexec.exe` utiliza la `CCP_NODES` variable, de forma predeterminada (Véase [variables de entorno](#environment-variables)) en el ejemplo se excluye la línea de comandos de la aplicación anterior.

## <a name="environment-variables"></a>Variables de entorno

Por lotes crean varias [variables de entorno] [ msdn_env_var] específicos de las tareas de varias instancias en los nodos de cálculo asignados a una tarea de varias instancias. Las líneas de comando de coordinación y aplicación puede hacer referencia a estas variables de entorno, como las secuencias de comandos y programas que se ejecuten.

Las siguientes variables de entorno son creadas por el servicio de lotes para su uso por tareas de varias instancias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obtener más información acerca de estas y el lote compute nodo las variables de entorno, incluido su contenido y visibilidad, vea [calcular variables de entorno del nodo][msdn_env_var].

>[AZURE.TIP] El ejemplo de código de lote Linux MPI contiene un ejemplo de cómo se pueden utilizar varias de estas variables de entorno. La [coordinación cmd] [ coord_cmd_example] secuencia de comandos de Bash descargas de aplicaciones comunes y entrada de archivos de almacenamiento de Azure, permite a un recurso compartido de Network File System (NFS) en el nodo maestro y configura los demás nodos asignados a la tarea de varias instancias como clientes NFS.

## <a name="resource-files"></a>Archivos de recursos

Hay dos conjuntos de archivos de recursos a tener en cuenta para las tareas de varias instancias: **archivos de recursos comunes** que descargue *todas las* tareas (ambos principal y subtareas), y **archivos de recursos** especificados para la instancia de múltiples tareas propio, descargas de tarea que *sólo el principal* .

Puede especificar uno o más **archivos de recursos comunes** en la configuración de varias instancias de una tarea. Estos archivos de recursos comunes se descargan desde [El almacenamiento de Azure](./../storage/storage-introduction.md) en **directorio compartido de tareas** por el principal del cada nodo y todas las subtareas. Tener acceso al directorio compartido tareas a de líneas de comandos de aplicación y coordinación mediante el `AZ_BATCH_TASK_SHARED_DIR` la variable de entorno. El `AZ_BATCH_TASK_SHARED_DIR` ruta de acceso es idéntico en todos los nodos asignado a la tarea de varias instancias, lo que puede compartir un comando único de coordinación entre el principal y todas las subtareas. Lote no "compartir" el directorio en un sentido de acceso remoto, pero puede utilizarlo como un montaje o compartir punto como se ha mencionado anteriormente en la información sobre las variables del entorno.

Archivos de recursos que especifique para la propia tarea varias instancias se descargan en el directorio de trabajo de la tarea, `AZ_BATCH_TASK_WORKING_DIR`, de forma predeterminada. Como se mencionó, a diferencia de los archivos de recursos comunes, sólo la tarea principal descarga archivos de recurso especificados para la propia tarea de varias instancias.

> [AZURE.IMPORTANT] Utilice siempre las variables de entorno `AZ_BATCH_TASK_SHARED_DIR` y `AZ_BATCH_TASK_WORKING_DIR` para hacer referencia a estos directorios en las líneas de comando. No intente crear manualmente las rutas de acceso.

## <a name="task-lifetime"></a>Duración de la tarea

La duración de la tarea principal controla la duración de la tarea de toda instancia múltiple. Cuando sale de la primaria, se terminan todas las subtareas. El código de salida del equipo principal es el código de salida de la tarea y, por tanto, se utiliza para determinar el éxito o el fracaso de la tarea para fines de reintentos.

Si falla alguna de las subtareas, saliendo con un código de retorno distinto de cero, por ejemplo, la tarea de toda instancia múltiple se produce un error. La tarea de varias instancias se termina y vuelve a intentar, hasta su límite de reintentos.

Cuando se elimina una tarea de varias instancias, también se eliminan por el servicio de lote principal y todas las subtareas. Todos subtarea directorios y sus archivos se eliminarán de los nodos de cálculo, igual que para una tarea estándar.

[TaskConstraints] [ net_taskconstraints] para una tarea de varias instancias, como el [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]y [RetentionTime] [ net_taskconstraint_retention] , se admiten las propiedades ya que son para una tarea estándar y aplicarán a la principal y todas las subtareas. Sin embargo, si cambia la [RetentionTime] [ net_taskconstraint_retention] propiedad después de agregar la tarea de varias instancias para el trabajo, este cambio sólo se aplica a la tarea primaria. Todas las subtareas seguirán utilizando el original [RetentionTime][net_taskconstraint_retention].

Lista de tareas recientes de un nodo de cálculo refleja el identificador de una subtarea si la tarea reciente formaba parte de una tarea de varias instancias.

## <a name="obtain-information-about-subtasks"></a>Obtener información acerca de las subtareas

Para obtener información sobre las subtareas utilizando la biblioteca de .NET por lotes, llame a la [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Este método devuelve información sobre todas las subtareas e información sobre el nodo de cálculo que ejecutan las tareas. Con esta información, puede determinar el directorio de raíz de cada subtarea, el identificador de grupo, su estado actual, código de salida y. Esta información se puede utilizar en combinación con el [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obtener los archivos de la subtarea. Tenga en cuenta que este método no devuelve información de la tarea principal (id 0).

> [AZURE.NOTE] A menos que se indique lo contrario, los métodos .NET por lotes que funcionan en las instancias múltiples [CloudTask] [ net_task] sí se aplican *sólo* a la tarea primaria. Por ejemplo, al llamar a la [CloudTask.ListNodeFiles] [ net_task_listnodefiles] método en una tarea de varias instancias, se devuelven sólo los archivos de la tarea principal.

El fragmento de código siguiente muestra cómo obtener información de las subtareas, así como para solicitar el contenido del archivo de los nodos en el que ejecuta.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Ejemplo de código

La [MultiInstanceTasks] [ github_mpi] en GitHub el ejemplo de código se muestra cómo utilizar una tarea de varias instancias para que se ejecute un [MS-MPI] [ msmpi_msdn] nodos de cálculo de aplicación en el lote. Siga los pasos de [preparación](#preparation) y [ejecución](#execution) para ejecutar el ejemplo.

### <a name="preparation"></a>Preparación

1. Siga los dos primeros pasos en [cómo compilar y ejecutar un programa de MS-MPI simple][msmpi_howto]. Esto satisface la prerequesites para el siguiente paso.
1. Crear una versión de *lanzamiento* de la [MPIHelloWorld] [ helloworld_proj] ejemplo de programa MPI. Éste es el programa que ejecutará la tarea de instancias múltiples nodos de cálculo.
1. Crear un archivo zip que contiene `MPIHelloWorld.exe` (que genera el paso 2) y `MSMpiSetup.exe` (que había descargado el paso 1). Podrá cargar este archivo zip como un paquete de aplicación en el siguiente paso.
1. Utilice el [portal de Azure] [ portal] para crear un lote de [aplicación](batch-application-packages.md) llamado "MPIHelloWorld" y especifique el archivo zip que creó en el paso anterior como versión "1.0" del paquete de aplicación. Para obtener más información, vea [cargar y administrar aplicaciones](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Crear una versión de *lanzamiento* de `MPIHelloWorld.exe` por lo que no tiene que incluir las dependencias adicionales (por ejemplo, `msvcp140d.dll` o `vcruntime140d.dll`) en el paquete de aplicación.

### <a name="execution"></a>Ejecución

1. Descargar las [muestras del lote de azure] [ github_samples_zip] de GitHub.
1. Abra la **solución** de MultiInstanceTasks en Visual Studio 2015. El `MultiInstanceTasks.sln` archivo de solución se encuentra en:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Escriba sus credenciales de cuenta por lotes y de almacenamiento de información en `AccountSettings.settings` en el proyecto **Microsoft.Azure.Batch.Samples.Common** .
1. **Generar y ejecutar** la solución MultiInstanceTasks para ejecutar el MPI la aplicación de ejemplo en nodos de cálculo en un grupo de lotes.
1. *Opcional*: usar el [portal de Azure] [ portal] o el [Explorador de proceso por lotes] [ batch_explorer] para examinar el grupo de ejemplo, trabajo y tareas ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de eliminan los recursos.

>[AZURE.TIP] [Comunidad de Visual Studio] puede descargar[ visual_studio] gratis si no tiene Visual Studio.

Resultado de `MultiInstanceTasks.exe` es similar al siguiente:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximos pasos

- El blog del equipo de lote de Azure y Microsoft HPC explica [MPI soporte para Linux en Azure lote][blog_mpi_linux]e incluye información sobre el uso de [OpenFOAM] [ openfoam] con el lote. Puede encontrar ejemplos de código Python para el [ejemplo OpenFOAM en GitHub][github_mpi].

- Aprenda cómo [crear grupos de nodos computacionales Linux](batch-linux-nodes.md) para su uso en las soluciones de Azure lote MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Resumen de varias instancias"
