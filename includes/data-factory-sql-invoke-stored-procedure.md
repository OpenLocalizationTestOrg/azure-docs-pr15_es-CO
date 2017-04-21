## <a name="invoking-stored-procedure-for-sql-sink"></a>Invocando el procedimiento almacenado para el receptor de SQL

Cuando se copian datos en SQL Server o base de datos de Azure SQL o SQL Server, un usuario especificado procedimiento almacenado podría ser configurado y se invoca con parámetros adicionales. 

Un procedimiento almacenado puede aprovecharse cuando los mecanismos de copia integrada no sirven para el propósito. Normalmente, esto se aprovecha cuando un proceso adicional (combinar columnas, buscar valores adicionales, inserción en varias tablas...) debe realizarse antes de la inserción de datos de origen en la tabla de destino final. 

Puede invocar un procedimiento almacenado de la opción. En el siguiente ejemplo se muestra cómo utilizar un procedimiento almacenado para realizar una inserción en una tabla de la base de datos simple. 

**Conjunto de datos de salida**

En este ejemplo, el tipo se establece en: SqlServerTable. Establézcalo en AzureSqlTable utilizar con una base de datos de SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Definir la sección SqlSink en la actividad de copia JSON de la siguiente manera. Para llamar a un procedimiento almacenado al insertar datos, son necesarias las propiedades SqlWriterStoredProcedureName y SqlWriterTableType.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

En la base de datos, definir el procedimiento almacenado con el mismo nombre como SqlWriterStoredProcedureName. Controla los datos de entrada desde el origen especificado y la inserción en la tabla de resultados. Observe que el nombre del parámetro del procedimiento almacenado debe ser igual a tableName definido en el archivo de tabla JSON.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

En la base de datos, defina el tipo de tabla con el mismo nombre como SqlWriterTableType. Observe que el esquema del tipo tabla debe ser igual que el esquema devuelto por los datos de entrada.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La característica de procedimiento almacenado aprovecha [Los parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).