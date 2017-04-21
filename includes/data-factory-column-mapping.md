## <a name="column-mapping-with-translator-rules"></a>Asignación de columnas con reglas de traductor
Asignación de columna puede utilizarse para especificar cómo se especifican columnas en la "estructura" de asignación de tabla de origen a las columnas especificada en la "estructura" de la tabla del receptor. La propiedad **columnMapping** está disponible en la sección **typeProperties** de la actividad de copia.

Asignación de columna admite los siguientes escenarios:

- Todas las columnas de la tabla de origen "estructura" se asignan a todas las columnas de la tabla de receptor "estructura".
- Un subconjunto de las columnas de la tabla de origen "estructura" se asignan a todas las columnas de la tabla de receptor "estructura".

Los siguientes son las condiciones de error y se producirán una excepción:

- Menos columnas o columnas más en la "estructura" de la tabla de receptor que especifica en la asignación.
- Asignación duplicada.
- Resultado de la consulta SQL no tiene un nombre de columna que se especifica en la asignación.

## <a name="column-mapping-samples"></a>Ejemplos de asignación de columna
> [AZURE.NOTE] Los ejemplos siguientes son para SQL Azure y Azure Blob pero son aplicables a cualquier almacén de datos que es compatible con conjuntos de datos rectangular. Tendrá que ajustar el conjunto de datos y las definiciones de servicios vinculados en los ejemplos siguientes para señalar a los datos del origen de datos correspondiente. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ejemplo 1: asignación de Azure SQL a Azure blob de columnas
En este ejemplo, la tabla de entrada tiene una estructura y señala a una tabla SQL en una base de datos de SQL Azure.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

En este ejemplo, la tabla de resultados tiene una estructura y señala a un objeto binario en un almacenamiento Azure blob.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

A continuación se muestra el JSON para la actividad. Las columnas de origen que se asignan a columnas de receptor (**columnMappings**) mediante la propiedad **traductor** .

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Flujo de asignación de columna:**

![Flujo de asignación de columna](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ejemplo 2: consulta SQL Azure SQL Azure blob en la asignación de columnas
En este ejemplo, se utiliza una consulta SQL para extraer datos de SQL Azure en lugar de simplemente especificando el nombre de la tabla y los nombres de columna en la sección "estructura". 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

En este caso, los resultados de la consulta en primer lugar se asignan a las columnas especificadas en "estructura" de origen. A continuación, las columnas de origen "estructura" se asignan a columnas de receptor "estructura" con las reglas especificadas en columnMappings.  Supongamos que la consulta devuelve 5 columnas, dos columnas adicionales y las especificadas en la "estructura" de origen.

**Flujo de asignación de columna**

![Asignación de columna flujo-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







