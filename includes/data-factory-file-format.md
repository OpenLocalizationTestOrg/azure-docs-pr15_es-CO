## <a name="specifying-formats"></a>Especificar formatos

Generador de datos Azure admite los siguientes tipos de formato: 

- [Formato de texto](#specifying-textformat)
- [Formato JSON](#specifying-jsonformat)
- [Formato de Avro](#specifying-avroformat)
- [Formato ORC](#specifying-orcformat)
- [Formato de parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Especificar FormatoDeTexto (TextFormat)

Si se establece el formato en **FormatoDeTexto (TextFormat)**, puede especificar las siguientes propiedades **opcionales** en la sección de **formato** .

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | El carácter utilizado para separar columnas de un archivo. | Se permite sólo un carácter. El valor predeterminado es el punto y coma (','). <br/><br/>Para utilizar un carácter Unicode, consulte [Caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obtener el código correspondiente. Por ejemplo, puede utilizar un carácter no imprimible raro que probablemente no existe en los datos: especificar "\u0001" que representa el inicio del título (SOH). | No |
| rowDelimiter | El carácter utilizado para separar las filas en un archivo. | Se permite sólo un carácter. El valor predeterminado es cualquiera de los siguientes valores de lectura: ["\r\n", "\r", "\n"] y "\r\n" en escritura. | No |
| escapeChar | El carácter especial que se utiliza para escapar un delimitador de columna en el contenido del archivo de entrada. <br/><br/>No se puede especificar escapeChar y quoteChar para una tabla. | Se permite sólo un carácter. Ningún valor predeterminado. <br/><br/>Ejemplo: Si tienes coma (', ') como el delimitador de columnas pero desea que el carácter de coma en el texto (ejemplo: "Hello, world"), se puede definir '$' como carácter de escape y utilizar la cadena "$Hello, world" en el origen. | No | 
| propiedad quoteChar | El carácter utilizado para delimitar un valor de cadena. Los delimitadores de filas y columnas dentro de los caracteres de comillas se trataría como parte del valor de cadena. Esta propiedad es aplicable a los conjuntos de datos de entrada y salida.<br/><br/>No se puede especificar escapeChar y quoteChar para una tabla. | Se permite sólo un carácter. Ningún valor predeterminado. <br/><br/>Por ejemplo, si tiene coma (', ') como el delimitador de columnas pero desea tener el carácter de coma en el texto (ejemplo: < Hello, world >), puede definir "(comillas dobles) como el carácter de comillas y utilice la cadena"Hello, world"en el origen. | No |
| nullValue | Uno o más caracteres que permite representar un valor nulo. | Uno o más caracteres. Los valores predeterminados son "\N" y "NULL" en "\N" y de lectura de escritura. | No |
| encodingName | Especifique el nombre de codificación. | Un nombre de codificación válido. vea [Encoding.EncodingName (propiedad).](https://msdn.microsoft.com/library/system.text.encoding.aspx) Ejemplo: windows-1250 o shift_jis. El valor predeterminado es UTF-8. | No | 
| firstRowAsHeader | Especifica si se considera la primera fila como encabezado. Para un conjunto de datos de entrada, factoría de datos lee la primera fila como encabezado. Para un conjunto de datos de salida, factoría de datos escribe la primera fila como encabezado. <br/><br/>Vea [escenarios de uso de **firstRowAsHeader** y **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) de escenarios de ejemplo. | True<br/>False (valor predeterminado) | No |
| skipLineCount | Indica el número de filas que se omiten al leer datos de archivos de entrada. Si se especifican tanto skipLineCount como firstRowAsHeader, las líneas se omiten en primer lugar y, a continuación, se lee la información del encabezado del archivo de entrada. <br/><br/>Vea [escenarios de uso de firstRowAsHeader y skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) de escenarios de ejemplo. | Entero | No | 
| treatEmptyAsNull | Especifica si se va a tratar una cadena nula o vacía como un valor null al leer datos de un archivo de entrada. | True (predeterminado)<br/>False | No |  

#### <a name="textformat-example"></a>Ejemplo FormatoDeTexto (TextFormat)
En el ejemplo siguiente muestra algunas de las propiedades de formato para FormatoDeTexto (TextFormat).

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Para utilizar un escapeChar en lugar de la propiedad quoteChar, reemplace la línea con quoteChar con lo escapeChar siguiente:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Escenarios de uso de firstRowAsHeader y skipLineCount

- Está copiando desde un origen no es un archivo en un archivo de texto y desea agregar una línea de encabezado que contiene los metadatos de esquema (por ejemplo: esquema SQL). Especificar **firstRowAsHeader** como true en el conjunto de datos de salida para este escenario. 
- Está copiando un archivo de texto que contiene una línea de encabezado a un receptor no es un archivo y desea quitar esa línea. Especificar **firstRowAsHeader** como true en el conjunto de datos de entrada.
- Está copiando un archivo de texto y desea omitir unas líneas al principio que no contienen ninguna información de datos o de encabezado. Especificar **skipLineCount** para indicar el número de líneas que se omitirán. Si el resto del archivo contiene una línea de encabezado, también puede especificar **firstRowAsHeader**. Si se especifican tanto **skipLineCount** como **firstRowAsHeader** , las líneas se omiten en primer lugar y, a continuación, se lee la información del encabezado del archivo de entrada

### <a name="specifying-avroformat"></a>Especificación de AvroFormat
Si el formato se establece en AvroFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "AvroFormat",
    }

Para utilizar el formato de Avro en una tabla de la sección, consulte [tutorial del subárbol de Apache](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenga en cuenta los siguientes puntos:  

- No se admiten [tipos de datos complejos](http://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumeraciones, matrices, mapas, uniones y fija)

### <a name="specifying-jsonformat"></a>Especificación de JsonFormat

Si el formato se establece en **JsonFormat**, puede especificar las siguientes propiedades **opcionales** en la sección de **formato** .

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| filePattern | Indicar el modelo de datos almacenados en cada archivo JSON. Valores permitidos son: **setOfObjects** y **arrayOfObjects**. El valor **predeterminado** es: **setOfObjects**. Consulte las siguientes secciones para obtener más información sobre estos modelos.| No |
| encodingName | Especifique el nombre de codificación. Para la lista de nombres de codificación válidos, vea: propiedad de [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) . Por ejemplo: windows-1250 o shift_jis. El valor **predeterminado** es: **UTF-8**. | No | 
| nestingSeparator | Carácter que se utiliza para separar niveles de anidamiento. El valor predeterminado es '.' (punto). | No | 


#### <a name="setofobjects-file-pattern"></a>modelo de archivo de setOfObjects

Cada archivo contiene el único objeto o línea-delimitado o concatenados varios objetos. Cuando se elige esta opción en un conjunto de datos de salida, actividad de copia genera un único archivo JSON con cada objeto por línea (delimitado por línea).

**objeto único** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**delimitado por línea JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenado**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>modelo de archivo arrayOfObjects. 

Cada archivo contiene una matriz de objetos. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Ejemplo de JsonFormat

Si tiene un archivo JSON con el siguiente contenido:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

y desea copiarlo en una tabla de SQL Azure en el formato siguiente: 

Id.  | Apellido.nombre | Name.Middle | Dirección | Etiquetas
--- | ---------- | ----------- | --------- | ----
1 | John | null | Pérez | ["Datos de fábrica", "Azure"]

El conjunto de datos de entrada con el tipo de JsonFormat se define como sigue: (definición parcial con sólo las partes relevantes)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Si no se define la estructura, la actividad de copia aplana la estructura predeterminada y copie cada cosa. 

#### <a name="supported-json-structure"></a>Estructura JSON admitida
Tenga en cuenta los siguientes puntos: 

- Cada objeto con una colección de pares nombre/valor se asigna a una fila de datos en formato tabular. Los objetos se pueden anidar y se puede definir cómo aplanar la estructura de un conjunto de datos con el separador de anidamiento (.) de forma predeterminada. Vea el [ejemplo JsonFormat](#jsonformat-example) sección para obtener un ejemplo anterior.  
- Si no se define la estructura del conjunto de datos del generador de datos, la actividad de copia se detecta el esquema a partir del primer objeto y acoplar todo el objeto. 
- Si la entrada JSON tiene una matriz, la actividad de copia convierte el valor de la matriz completa en una cadena. Puede omitirlo mediante [asignación de columnas o filtros](#column-mapping-with-translator-rules).
- Si hay nombres duplicados en el mismo nivel, la actividad de copia toma el último.
- Los nombres de propiedad distinguen mayúsculas de minúsculas. Dos propiedades con el mismo nombre pero diferentes tripas se tratan como dos propiedades independientes. 

### <a name="specifying-orcformat"></a>Especificación de OrcFormat
Si el formato se establece en OrcFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Si no copia archivos ORC **como-es** entre local y la nube de almacenes de datos, necesitará instalar el 8 de JRE (Java Runtime Environment) en el equipo de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y 32 bits gateway JRE de 32 bits. Encontrará ambas versiones desde [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Seleccione el adecuado.

Tenga en cuenta los siguientes puntos:

-   Datos complejos tipos no son compatibles (STRUCT, mapa, lista, unión)
-   Archivo ORC tiene tres [Opciones de compresión](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): ninguno, ZLIB, SNAPPY. Factoría de datos admite la lectura de datos de archivo ORC en cualquiera de estos formatos comprimidos. Utiliza la compresión códec está en los metadatos para leer los datos. Sin embargo, al escribir en un archivo ORC, factoría de datos elige ZLIB, que es el valor predeterminado de ORC. Actualmente, no hay ninguna opción para invalidar este comportamiento. 

### <a name="specifying-parquetformat"></a>Especificación de ParquetFormat
Si el formato se establece en ParquetFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Si no copia archivos de Parquet **como-es** entre local y la nube de almacenes de datos, necesitará instalar el 8 de JRE (Java Runtime Environment) en el equipo de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y 32 bits gateway JRE de 32 bits. Encontrará ambas versiones desde [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Seleccione el adecuado.

Tenga en cuenta los siguientes puntos:

-   Datos complejos tipos no son compatibles (mapa, lista)
-   Archivo de parquet tiene las siguientes opciones relacionadas con la compresión: NONE, SNAPPY, GZIP y LZO. Factoría de datos admite la lectura de datos de archivo ORC en cualquiera de estos formatos comprimidos. Utiliza el códec de compresión en los metadatos para leer los datos. Sin embargo, al escribir en un archivo de parqué, factoría de datos elige SNAPPY, que es el valor predeterminado para el formato de Parquet. Actualmente, no hay ninguna opción para invalidar este comportamiento. 
