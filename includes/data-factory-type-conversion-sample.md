### <a name="type-conversion-sample"></a>Ejemplo de conversión de tipo
En el siguiente ejemplo es para copiar datos de un Blob a SQL Azure con las conversiones de tipo.

Supongamos que el conjunto de datos Blob en formato CSV y contiene 3 columnas. Uno de ellos es una columna de fecha y hora con un formato de fecha y hora personalizado mediante francés abreviaturas para el día de la semana.

Se definen el conjunto de datos de origen de Blob como sigue junto con las definiciones de tipo para las columnas.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Dado el SQL tipo de tabla de asignación de tipo .NET anteriormente se definiría en la tabla SQL Azure con el siguiente esquema.

| Nombre de la columna | Tipo SQL |
| ----------- | -------- |
| ID de usuario | bigint |
| nombre | texto |
| LastLoginDate | fecha y hora |

A continuación se definirá el conjunto de datos de SQL Azure como sigue. Nota: No es necesario especificar la sección de "estructura" con información de tipos desde el tipo de información ya está especificada en el almacén de datos subyacente.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

En este caso factoría de datos realizará automáticamente el tipo de conversiones, incluido el campo de fecha y hora con la fecha y hora personalizado formato utilizando la referencia cultural fr-FR. al mover los datos de Blob a SQL Azure.


