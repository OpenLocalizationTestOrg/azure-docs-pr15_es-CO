<properties
    pageTitle="Consultas de lista eficaz en lote de Azure | Microsoft Azure"
    description="Aumentar el rendimiento mediante el filtrado de las consultas cuando se solicita información sobre los recursos de proceso por lotes como grupos, trabajos, tareas y nodos de cálculo."
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
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Consultar el servicio de proceso de Azure eficientemente

Aquí aprenderá cómo aumentar el rendimiento de la aplicación de Azure lote al reducir la cantidad de datos devueltos por el servicio al realizar consultas de trabajos, tareas y calcula los nodos con el [Lote .NET] [ api_net] library.

Casi todas las aplicaciones de lote necesitan realizar algún tipo de supervisión u otra operación que consulta el servicio de proceso por lotes, a menudo a intervalos regulares. Por ejemplo, para determinar si hay cualquier tarea en cola restante en un trabajo, debe obtener los datos de cada tarea en el trabajo. Para determinar el estado de los nodos en el grupo de servidores, debe obtener los datos en cada nodo de la piscina. Este artículo explica cómo ejecutar dichas consultas de la forma más eficaz.

## <a name="meet-the-detaillevel"></a>Cumplir con la DetailLevel

En una aplicación por lotes de producción, las entidades como trabajos, tareas y nodos de cálculo pueden varios miles. Cuando se solicita información acerca de estos recursos, una gran cantidad de datos debe "cruzar el cable" desde el servicio de proceso por lotes para su aplicación en cada consulta. Al limitar el número de elementos y el tipo de información que es devuelto por una consulta, puede aumentar la velocidad de las consultas y por tanto el rendimiento de la aplicación.

Este [Lote .NET] [ api_net] fragmento de código de API enumera *todas* las tareas que está asociada con un trabajo, junto con *todas* las propiedades de cada tarea:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Puede realizar una consulta de lista mucho más eficaz, sin embargo, mediante la aplicación de "nivel de detalle" a la consulta. Para ello, proporcionando un [ODATADetailLevel] [ odata] objeto de la [JobOperations.ListTasks] [ net_list_tasks] método. Este fragmento de código devuelve el Id., línea de comandos y propiedades de información de nodo de cálculo de las tareas completadas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

En este escenario de ejemplo, si hay miles de tareas en el trabajo, los resultados de la segunda consulta normalmente se devolverá mucho más rápido que el primero. Para obtener más información acerca de cómo utilizar ODATADetailLevel Cuando pones artículos con la API de .NET por lotes es incluido [a continuación](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Recomendamos encarecidamente que usted *siempre* proporcionar un objeto de ODATADetailLevel a las llamadas de la lista de las API de .NET para garantizar la máxima eficacia y rendimiento de la aplicación. Al especificar un nivel de detalle, puede ayudar a reducir los tiempos de respuesta del servicio de proceso por lotes, mejorar la utilización de la red y minimizar el uso de memoria por aplicaciones cliente.

## <a name="filter-select-and-expand"></a>Filtrar, seleccione y expanda

El [Lote .NET] [ api_net] y el [Resto del lote] [ api_rest] API proporcionan la capacidad de reducir el número de elementos que se devuelven en una lista, así como la cantidad de información que se devuelve para cada uno. Lo hace mediante la especificación de **filtro**, **Seleccione**y **expanda las cadenas** al realizar consultas de la lista.

### <a name="filter"></a>Filtro
La cadena de filtro es una expresión que reduce el número de elementos que se devuelven. Por ejemplo, mostrar sólo las tareas en ejecución de un trabajo o nodos de cálculo que están listos para ejecutar las tareas de la lista.

- La cadena de filtro consta de una o más expresiones, con una expresión que consta de un nombre de propiedad, un operador y un valor. Las propiedades que pueden especificarse son específicas para cada tipo de entidad que consulta, como son los operadores que se admiten para cada propiedad.
- Varias expresiones pueden combinarse mediante los operadores lógicos `and` y `or`.
- En este ejemplo filtrar listas de cadenas sólo la ejecución "representar" tareas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Seleccione
La cadena de selección limita los valores de propiedad que se devuelven para cada elemento. Especifica una lista de nombres de propiedad y se devuelven únicamente los valores de propiedad para los elementos de resultados de la consulta.

- La cadena de selección consiste en una lista separada por comas de nombres de propiedad. Puede especificar cualquiera de las propiedades para el tipo de entidad que se está consultando.
- Este ejemplo de select cadena especifica que se deben devolver sólo tres valores de propiedad para cada tarea: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
La cadena de expansión reduce el número de llamadas a la API que se requieren para obtener cierta información. Cuando se utiliza una cadena de expansión, puede obtenerse más información sobre cada elemento con una sola llamada API. En lugar de obtener primero la lista de entidades, a continuación, solicita información para cada elemento de la lista, se utilice una cadena de expansión para obtener la misma información en una sola llamada API. Menos llamadas a la API supone un mejor rendimiento.

- Similar a la cadena de selección, la cadena de expansión controla si ciertos datos se incluyen en resultados de consulta de la lista.
- La cadena de expandir sólo se admite cuando se utiliza en la lista de trabajos, programaciones de trabajo, tareas y grupos. Actualmente, sólo admite información de estadísticas.
- Cuando todas las propiedades son necesarios y no se especifica ninguna cadena select, la cadena de expansión *debe* utilizarse para obtener información estadística. Si se utiliza una cadena de selección para obtener un subconjunto de propiedades, a continuación, `stats` pueden especificarse en la cadena de selección, y no es necesario especificar la cadena de expansión.
- En este ejemplo se expanda cadena especifica que se debe devolver información de estadísticas para cada elemento de la lista: `stats`.

> [AZURE.NOTE] Al construir cualquiera de los tipos de cadena de tres consulta (filtrar, seleccione y expanda), debe asegurarse de que coinciden los nombres de propiedad y el caso de sus homólogos de elemento de la API de REST. Por ejemplo, cuando se trabaja con la clase de .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , debe especificar el **estado** en lugar del **estado**, incluso aunque la propiedad .NET es [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte las tablas siguientes para las asignaciones de propiedad entre las API de resto y. NET.

### <a name="rules-for-filter-select-and-expand-strings"></a>Las reglas de filtro, seleccione y expanda las cadenas

- Nombres de propiedades de filtro, seleccione y expanda las cadenas deben aparecer como lo hacían en el [Resto del lote] [ api_rest] API--incluso cuando se utiliza [.NET por lotes] [ api_net] o uno de los otros SDK de lote.
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad distinguen mayúsculas de minúsculas.
- Fecha y hora puede ser uno de los dos formatos de cadenas y deben ir precedidas por `DateTime`.

  - Ejemplo de formato DTF de W3C:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Ejemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Cadenas booleanas son `true` o `false`.
- Si se especifica una propiedad no válida o un operador, un `400 (Bad Request)` producirá un error.

## <a name="efficient-querying-in-batch-net"></a>Eficiente de consulta en lote.

Dentro del [Lote .NET] [ api_net] API, el [ODATADetailLevel] [ odata] clase se utiliza para el suministro de filtro, seleccione y expanda las cadenas a la lista de operaciones. La clase ODataDetailLevel tiene tres propiedades de cadena pública que pueden especificados en el constructor o establecer directamente en el objeto. A continuación, pase el objeto de ODataDetailLevel como un parámetro a las distintas operaciones de lista como [ListPools][net_list_pools], [ListJobs][net_list_jobs]y [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limitar el número de elementos que se devuelven.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Especificar los valores de propiedades que se devuelven con cada elemento.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Llamar recuperar datos de todos los elementos en una sola API en lugar de llamadas independientes para cada elemento.

El siguiente fragmento de código utiliza la API de .NET de lote para consultar eficazmente el servicio por lotes para las estadísticas de un conjunto específico de grupos. En este escenario, el usuario de proceso por lotes tiene grupos de prueba y producción. El grupo de prueba identificadores van precedidas de "prueba" y el grupo de producción identificadores van precedidas de "producción". En el fragmento de código, *myBatchClient* es una instancia de la clase [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) ha inicializado correctamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Una instancia de [ODATADetailLevel] [ odata] que está configurado con Select y cláusulas de expansión también pueden pasarse para el caso de métodos Get, como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), para limitar la cantidad de datos que se devuelven.

## <a name="batch-rest-to-net-api-mappings"></a>Lote resto a las asignaciones de las API de .NET

Nombres de propiedad de filtro, seleccione y expanda las cadenas *deben* reflejar sus equivalentes API de REST, tanto en nombre como caso. Las tablas siguientes indican las asignaciones entre los equivalentes de .NET y la API de REST.

### <a name="mappings-for-filter-strings"></a>Asignaciones de las cadenas de filtro

- **Lista de métodos. NET**: cada uno de los métodos de la API de .NET en esta columna acepta una [ODATADetailLevel] [ odata] objeto como parámetro.
- **Solicitudes de lista REST**: página de cada REST API vinculado en esta columna contiene una tabla que especifica las propiedades y operaciones que se permiten en las cadenas de *filtro* . Utilizará estos nombres de propiedades y operaciones cuando se construye un [ODATADetailLevel.FilterClause] [ odata_filter] cadena.

| Lista de métodos de .NET | Solicitudes de lista REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Enumerar los certificados en una cuenta][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Lista de los archivos asociados a una tarea][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Enumera el estado de la preparación de trabajos y tareas de lanzamiento para un trabajo][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Enumerar los trabajos de una cuenta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Lista de los archivos en un nodo][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Lista de las tareas asociadas a una tarea][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Lista de los planes de trabajo en una cuenta][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Enumerar los trabajos asociados a una tarea programada][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Lista de los nodos de cálculo en un grupo][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Lista de los grupos de una cuenta][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Asignaciones para seleccionadas cadenas

- **Tipos .NET por lotes**: tipos de la API de .NET por lotes.
- **Entidades de REST API**: cada página de esta columna contiene una o más tablas que enumeran los nombres de propiedad de REST API para el tipo. Estos nombres de propiedades se utilizan al construir cadenas *Seleccione* . Utilizará los mismos nombres de propiedad cuando se construye un [ODATADetailLevel.SelectClause] [ odata_select] cadena.

| Tipos de lotes .NET | Entidades de la API de REST |
|---|---|
| [Certificado][net_cert] | [Obtener información sobre un certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obtener información acerca de un trabajo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtener información acerca de una tarea programada][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtener información sobre un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Obtener información acerca de un grupo][rest_get_pool] |
| [CloudTask][net_task] | [Obtener información acerca de una tarea][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Ejemplo: crear una cadena de filtro

Cuando se construye una cadena de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte la tabla anteriormente en "Asignaciones para las cadenas de filtro" a la página de documentación de buscar REST API que corresponde a la operación de la lista que desea realizar. Encontrará las propiedades filtrables y sus operadores admitidos en la primera tabla VariasFilas (MultiRow) en esa página. Si desea recuperar todas las tareas cuyo código de salida es distinto de cero, por ejemplo, esta fila en la [lista de las tareas asociadas a una tarea] [ rest_list_tasks] especifica la cadena de propiedad aplicable y operadores permitidos:

| Propiedad | Operaciones permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Por lo tanto, sería la cadena de filtro para mostrar todas las tareas con un código de salida distinto de cero:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Ejemplo: construir una cadena de selección

Construir [ODATADetailLevel.SelectClause][odata_select], consulte la tabla anterior en "Asignaciones para seleccionadas cadenas" y vaya a la página de REST API que corresponde al tipo de entidad que estás anunciando. Encontrará las propiedades seleccionables y sus operadores admitidos en la primera tabla VariasFilas (MultiRow) en esa página. Si desea recuperar sólo el ID y la línea de comandos para cada tarea en una lista, por ejemplo, encontrará estas filas en la tabla aplicable en [obtener información acerca de una tarea][rest_get_task]:

| Propiedad | Tipo | Notas |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La cadena de selección para incluir sólo el ID y la línea de comandos con cada tarea de la lista sería:

`id, commandLine`

## <a name="code-samples"></a>Ejemplos de código

### <a name="efficient-list-queries-code-sample"></a>Ejemplo de código de consultas eficaz lista

Retirar el [EfficientListQueries] [ efficient_query_sample] proyecto de ejemplo en GitHub para ver la consulta de lista cómo eficaz puede afectar al rendimiento de una aplicación. Esta aplicación de consola C# se crea y agrega un gran número de tareas a una tarea. A continuación, realiza varias llamadas a la [JobOperations.ListTasks] [ net_list_tasks] método y pasadas [ODATADetailLevel] [ odata] objetos que estén configurados con distintos valores de propiedad para variar la cantidad de datos que se va a devolver. Produce un resultado similar al siguiente:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Como se muestra en los tiempos transcurridos, puede reducir considerablemente los tiempos de respuesta de consulta limitando las propiedades y el número de elementos que se devuelven. Puede encontrar este y otros proyectos de muestra en los [ejemplos de azure lote] [ github_samples] repositorio en GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Ejemplo de biblioteca y el código de BatchMetrics

Además del ejemplo de código EfficientListQueries anterior, puede encontrar el [BatchMetrics] [ batch_metrics] proyecto en las [muestras del lote de azure] [ github_samples] repositorio de GitHub. El proyecto de ejemplo BatchMetrics muestra cómo supervisar el progreso de trabajo por lotes de Azure usando la API de lote eficazmente.

La [BatchMetrics] [ batch_metrics] ejemplo incluye un proyecto de biblioteca de clase .NET que puede incorporar a sus propios proyectos y un programa de línea de comandos simple ejercicio y demostrar el uso de la biblioteca.

Dentro del proyecto de la aplicación de ejemplo muestra las siguientes operaciones:

1. Seleccionar atributos específicos con el fin de descargar sólo las propiedades que necesita
2. Filtrado en tiempos de transición de estado para descargar sólo los cambios desde la última consulta

Por ejemplo, el siguiente método aparece en la biblioteca de BatchMetrics. Devuelve un ODATADetailLevel que especifica que sólo los `id` y `state` propiedades deben obtenerse para las entidades que se consultan. También especifica que sólo las entidades cuyo estado ha cambiado desde que se ha especificado `DateTime` parámetro debe devolverse.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Próximos pasos

### <a name="parallel-node-tasks"></a>Tareas del nodo paralelo

[Lote de Azure maximizar calcular uso de recursos con tareas simultáneas de nodo](batch-parallel-node-tasks.md) es otro artículo relacionados con el rendimiento de la aplicación de lote. Algunos tipos de cargas de trabajo pueden beneficiarse de la ejecución de tareas paralelas en mayor--pero menos--nodos de cálculo. Consulte el [escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo para obtener más información sobre este tipo de escenario.

### <a name="batch-forum"></a>Foro de lote

El [Foro de Azure lote] [ forum] en MSDN es un lugar excelente para analizar el lote y formular preguntas acerca del servicio. Cabeza de sobre para publicaciones "adhesivas" útiles y registrar las preguntas que puedan surgir mientras crea sus soluciones de proceso por lotes.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
