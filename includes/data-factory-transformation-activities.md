Generador de datos Azure es compatible con las actividades de transformación siguientes que se pueden agregar a las tuberías individualmente o encadenadas con otra actividad.

Actividad de transformación de datos |  Calcular el entorno 
:----------------------- | :--------------------
[Subárbol](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Cerdo](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop de transmisión por secuencias](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina de actividades de aprendizaje: recursos de actualización y ejecución por lotes](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procedimiento almacenado](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse o de SQL Server |
[Datos Analytics lago U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Análisis de datos Azure lago 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] o lote de Azure
   
> [AZURE.NOTE] 
> MapReduce actividad sirve para ejecutar programas de chispa en el clúster HDInsight Spark. Vea [programas de chispa invocar de la factoría de datos de Azure](../articles/data-factory/data-factory-spark.md) para más detalles.
> Puede crear una actividad personalizada para ejecutar secuencias de comandos de R en el clúster HDInsight con R instalado. Consulte [Ejecutar Script de R mediante el generador de datos de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).