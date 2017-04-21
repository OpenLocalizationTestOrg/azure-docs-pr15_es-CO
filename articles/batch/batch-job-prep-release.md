<properties
    pageTitle="Trabajo de preparación y limpieza de lote | Microsoft Azure"
    description="Utilice tareas de preparación de nivel de trabajo para minimizar la transferencia de datos a los nodos de cálculo por lotes de Azure y suelte las tareas de limpieza de nodo a la finalización del trabajo."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Ejecutar tareas de preparación y realización del trabajo en los nodos de cálculo por lotes de Azure

 Un proceso de Azure, a menudo requiere algún tipo de instalación antes de que sus tareas se ejecutan y posterior mantenimiento de trabajo cuando se hayan completado sus tareas. Debe descargar los datos de entrada de tareas comunes para los nodos de cálculo, o cargar datos de salida de la tarea en el almacenamiento de Azure después de completarse el trabajo. Puede utilizar las tareas de **preparación del trabajo** y **liberar el trabajo** para realizar estas operaciones.

## <a name="what-are-job-preparation-and-release-tasks"></a>¿Qué son trabajo preparación y suelte las tareas?

Antes de ejecutarán las tareas de un trabajo, la tarea de preparación de trabajo se ejecuta en todos los nodos de cálculo programados para ejecutarse al menos una tarea. Una vez que haya terminado la tarea, la tarea de lanzamiento de trabajo se ejecuta en cada nodo del grupo de que se ejecuta al menos una tarea. Al igual que con las tareas normales de lote, puede especificar una línea de comandos que deben invocarse cuando se ejecuta una tarea de trabajo de preparación o release.

Tareas de preparación y el lanzamiento de trabajo ofrecen características familiares de tareas por lotes, como la descarga de archivos ([archivos de recursos][net_job_prep_resourcefiles]), elevados de ejecución, variables de entorno personalizadas, duración de ejecución máximo, número de reintentos y tiempo de retención de archivo.

En las siguientes secciones, aprenderá cómo utilizar el [JobPreparationTask] de[ net_job_prep] y [JobReleaseTask] [ net_job_release] clases se encuentran en el [Lote .NET] [ api_net] library.

> [AZURE.TIP] Tareas de preparación y el lanzamiento de trabajo son especialmente útiles en entornos "compartido pool", en el que un grupo de nodos de cálculo persiste entre ejecuciones de los trabajos y es utilizado por muchos trabajos.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Cuándo utilizar preparación del trabajo y las tareas de lanzamiento

Preparación del trabajo y las tareas de lanzamiento son los más idóneos para las siguientes situaciones:

**Descargar datos de tareas comunes**

Trabajos por lotes a menudo requieren un conjunto común de datos como entrada para las tareas del proyecto. Por ejemplo, en los cálculos de análisis de riesgo diaria, datos de mercado son específico del proyecto, aún comunes a todas las tareas en el trabajo. Estos datos de mercado, a menudo, varios gigabytes de tamaño, deben descargar sólo una vez para cada nodo de cálculo para que puede utilizar cualquier tarea que se ejecuta en el nodo. Utilice una **tarea de preparación de trabajos** para descargar estos datos en cada nodo antes de la ejecución del trabajo del otras tareas.

**Eliminar resultados de trabajo y tareas**

En un entorno "comparten la piscina", cuando los nodos de cálculo de la agrupación no se da de baja entre los trabajos, debe eliminar datos entre ejecuciones del trabajo. Debe conservar espacio en disco en los nodos o cumplir las directivas de seguridad de su organización. Utilizar una **versión de la tarea** eliminar datos que se ha descargado por una tarea de la tarea de preparación o generado durante la ejecución de la tarea.

**Retención de registros**

Puede que desee conservar una copia de los archivos de registro que generan las tareas, o tal vez bloquear archivos de volcado que pueden generar aplicaciones con errores. Utilizar una **versión de la tarea** en estos casos para comprimir y cargar estos datos en un [Almacenamiento de Azure] [ azure_storage] cuenta.

>[AZURE.TIP] Otra manera de conservar registros de trabajo y otras tareas de salida datos están utilizar la biblioteca de [Convenciones de archivo por lotes de Azure](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tareas de preparación del trabajo

Antes de la ejecución de tareas de un trabajo, Batch: ejecuta la tarea de preparación del trabajo en cada nodo de cálculo que está programado para ejecutar una tarea. De forma predeterminada, el servicio de lote se espera para que la tarea de preparación del trabajo para completarse antes de ejecutar las tareas programadas para ejecutar en el nodo. Sin embargo, puede configurar el servicio no debe esperar. Si se reinicia el nodo, la tarea de preparación de trabajo se ejecuta de nuevo, pero también se puede deshabilitar este comportamiento.

La tarea de preparación de trabajo se ejecuta sólo en los nodos que están programados para ejecutar una tarea. Esto impide la ejecución innecesaria de una tarea de preparación en caso de que un nodo no está asignado a una tarea. Esto puede ocurrir cuando el número de tareas para una tarea es menor que el número de nodos en un grupo. También se aplica cuando está habilitada [la ejecución de tareas simultáneas](batch-parallel-node-tasks.md) , lo que deja algunos nodos inactivo si el recuento de tarea es menor que el total posibles tareas simultáneas. Si no ejecuta la tarea de preparación del trabajo en los nodos inactivos, puede ahorrar dinero en gastos de transferencia de datos.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] diferencia de [CloudPool.StartTask] [ pool_starttask] en que JobPreparationTask se ejecuta al principio de cada trabajo, mientras que StartTask sólo se ejecuta si un nodo de cálculo primero se une a un grupo o se reinicia.

## <a name="job-release-task"></a>Versión de tarea

Una vez que una tarea está marcada como completada, la tarea de lanzamiento de trabajo se ejecuta en cada nodo del grupo de que se ejecuta al menos una tarea. Marca una tarea como completada mediante la emisión de una solicitud de terminación. El servicio de lote establece el estado del trabajo para *Terminar*, finaliza las tareas activas o ejecución asociadas con el trabajo y ejecuta la tarea de la tarea de liberación. A continuación, mueve el trabajo al estado *completado* .

> [AZURE.NOTE] Eliminación de trabajo también ejecuta la tarea de la tarea de liberación. Sin embargo, si ya ha terminado un trabajo, la tarea lanzamiento no se ejecuta una segunda vez si el trabajo se elimina posteriormente.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Trabajo de preparación y suelte tareas con .NET por lotes

Para utilizar una tarea de la tarea de preparación, asigne un [JobPreparationTask] [ net_job_prep] objeto [CloudJob.JobPreparationTask de su trabajo] [ net_job_prep_cloudjob] propiedad. Inicializar de forma similar, un [JobReleaseTask] de[ net_job_release] y asignarlo a [CloudJob.JobReleaseTask de su trabajo] [ net_job_prep_cloudjob] propiedad para establecer la versión de la tarea.

En este fragmento de código, `myBatchClient` es una instancia de [BatchClient][net_batch_client], y `myPool` es un grupo existente dentro de la cuenta de proceso por lotes.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Como se mencionó anteriormente, la tarea lanzamiento se ejecuta cuando se termina un trabajo o se elimina. Terminar un trabajo con [JobOperations.TerminateJobAsync][net_job_terminate]. Eliminar un trabajo de [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente terminar o eliminar un trabajo cuando se hayan completado sus tareas, o cuando se ha alcanzado el tiempo de espera que haya definido.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Ejemplo de código en GitHub

Para ver la preparación del trabajo y liberar las tareas de acción, consulte la [JobPrepRelease] [ job_prep_release_sample] proyecto de ejemplo en GitHub. Esta aplicación de consola hace lo siguiente:

1. Crea un grupo con dos nodos de "pequeños".
2. Crea un trabajo con tareas estándares, versión y preparación del trabajo.
3. Ejecuta la tarea de preparación del trabajo, que escribe primero el identificador de nodo en un archivo de texto en el directorio "compartido" de un nodo.
4. Ejecuta una tarea en cada nodo que escribe su identificador de tarea en el mismo archivo de texto.
5. Una vez que se completan todas las tareas (o se alcance el tiempo de espera), imprime el contenido del archivo de texto de cada nodo en la consola.
6. Cuando se complete el trabajo, ejecuta la versión de tarea para eliminar el archivo desde el nodo.
6. Imprime los códigos de salida de las tareas de preparación y lanzamiento de trabajo para cada nodo en el que ejecuta.
7. Ejecución de pausas para permitir la confirmación de la eliminación de trabajo o grupo de servidores.

Resultado de la aplicación de ejemplo es similar al siguiente:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Debido al tiempo variable de creación y el inicio de los nodos en un nuevo grupo (algunos nodos no están listos para tareas antes que otras), puede que vea resultados diferentes. Específicamente, porque las tareas se completan rápidamente, uno de los nodos del grupo puede ejecutar todas las tareas del proyecto. Si esto ocurre, observará que el trabajo de preparar y no existen tareas de liberación para el nodo que no ejecuta ninguna tarea.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspeccionar la preparación del trabajo y las tareas de liberación en el portal de Azure

Al ejecutar la aplicación de ejemplo, puede usar el [portal de Azure] [ portal] para ver las propiedades de la tarea y sus tareas, o incluso descargar el archivo de texto compartido que se modifica por las tareas de trabajo.

La captura de pantalla siguiente muestra la **hoja de tareas de preparación** en el portal de Azure después de una ejecución de la aplicación de ejemplo. Vaya a las propiedades de *JobPrepReleaseSampleJob* cuando hayan finalizado las tareas (pero antes de eliminar su trabajo y grupo) y haga clic en **tareas de preparación** o **tareas de lanzamiento** para ver sus propiedades.

![Propiedades del trabajo de preparación en el portal de Azure][1]

## <a name="next-steps"></a>Próximos pasos

### <a name="application-packages"></a>Paquetes de aplicaciones

Además de la tarea de preparación del trabajo, también puede utilizar la característica de [paquetes de aplicaciones](batch-application-packages.md) de proceso por lotes para preparar los nodos de cálculo para la ejecución de la tarea. Esta característica es especialmente útil para implementar aplicaciones que no requieren que se ejecuta un instalador, aplicaciones que contienen muchos archivos (100 +) o aplicaciones que requieren control de versiones estricto.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo

Esta publicación de foro MSDN proporciona una visión general de los diversos métodos de preparación de los nodos para la ejecución de tareas:

[Instalación de aplicaciones y datos en lotes de ensayo nodos de cálculo][forum_post]

Escrito por uno de los miembros del equipo por lotes de Azure, describe varias técnicas que puede utilizar para implementar aplicaciones y datos para nodos de cálculo.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
