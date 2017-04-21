## <a name="fileshare-dataset-type-properties"></a>Propiedades del tipo de conjunto de datos de recurso compartido de archivos

Para obtener una lista completa de las secciones y las propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](../articles/data-factory/data-factory-create-datasets.md) . Las secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos. 

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. Proporciona información que es específica del tipo de conjunto de datos. La sección de typeProperties para un conjunto de datos de tipo dataset **FileShare** tiene las siguientes propiedades:

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
folderPath | Ruta de Sub a la carpeta. Utilice el carácter de escape ' \ ' para los caracteres especiales en la cadena. Para obtener ejemplos, vea [ejemplo vinculado a las definiciones de servicio y el conjunto de datos](#sample-linked-service-and-dataset-definitions) .<br/><br/>Puede combinar esta propiedad con **partitionBy** tener rutas de carpeta basadas en sector fechas y horas de inicio y fin. | Sí
nombre de archivo | Especifique el nombre del archivo en el **folderPath** si desea que la tabla para hacer referencia a un archivo específico en la carpeta. Si no se especifica ningún valor para esta propiedad, la tabla se señala a todos los archivos de la carpeta.<br/><br/>Cuando no se especifica el nombre de archivo para un conjunto de datos de salida, el nombre del archivo generado sería el siguiente este formato: <br/><br/>Datos. <Guid>.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No
partitionedBy | partitionedBy puede utilizarse para especificar una dinámica folderPath, nombre de archivo de datos de la serie de tiempo. Por ejemplo, folderPath parametrizada, para cada hora de los datos. | No
Formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**y **OrcFormat**. Establezca la propiedad **type** en formato en uno de estos valores. Consulte [Especificar FormatoDeTexto (TextFormat)](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificar JsonFormat](#specifying-jsonformat)y [OrcFormat especificar](#specifying-orcformat) secciones para obtener más información. Si desea copiar los archivos como-es entre almacenes basados en archivos (copia binaria), puede omitir la sección formato de ambas definiciones de conjunto de datos de entrada y salida. | No
fileFilter | Especificar un filtro que se utilizará para seleccionar un subconjunto de archivos en el folderPath en lugar de todos los archivos.<br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (carácter individual).<br/><br/>Ejemplos 1:`"fileFilter": "*.log"`<br/>Ejemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter es aplicable a un conjunto de datos entrada de recurso compartido de archivos. Esta propiedad no es compatible con HDFS.  | No
| compresión | Especificar el tipo y el nivel de compresión de los datos. Tipos admitidos son: **GZip**, **Deflate**y **BZip2** y niveles admitidos son: **óptima** y **más rápida**. Actualmente, no se admite configuración de compresión de datos en **AvroFormat** o **OrcFormat**. Para obtener más información, consulte la sección [compatibilidad con la compresión](#compression-support) .  | No |
| useBinaryTransfer | Especifica si utilizar el modo de transferencia binaria. True para que el modo binario y ASCII false. Valor predeterminado: True. Esta propiedad sólo puede utilizarse cuando el tipo de servicio vinculado asociado es del tipo: FtpServer. | No | 
 

> [AZURE.NOTE] nombre de archivo y fileFilter no pueden utilizarse simultáneamente.

### <a name="using-partionedby-property"></a>Utilizando la propiedad partionedBy

Como se mencionó en la sección anterior, se puede especificar un folderPath dinámico, nombre de archivo de datos de la serie de tiempo con partitionedBy. Puede hacerlo con las macros de la factoría de datos y la variable de sistema SliceStart, SliceEnd que indican el período de tiempo lógico para un segmento de datos especificado. 

Para obtener información sobre los conjuntos de datos de la serie de tiempo, la programación y sectores, vea [Crear conjuntos de datos](../articles/data-factory/data-factory-create-datasets.md), [programación y ejecución](../articles/data-factory/data-factory-scheduling-and-execution.md)y [Creación de tuberías](../articles/data-factory/data-factory-create-pipelines.md) artículos. 

#### <a name="sample-1"></a>Ejemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

En este ejemplo {sector} se reemplaza con el valor de la variable de sistema del generador de datos SliceStart en el formato (YYYYMMDDHH) especificado. La SliceStart se refiere a la hora del sector de inicio. La folderPath es diferente para cada sector. Ejemplo: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

En este ejemplo, el año, mes, día y hora de SliceStart se extraen a variables independientes que se utilizan en propiedades folderPath y nombre de archivo.
