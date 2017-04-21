## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especifica la definición de la estructura de los conjuntos de datos rectangular
La sección de la estructura de los conjuntos de datos JSON es una sección **opcional** para tablas rectangulares (con filas y columnas) y contiene una colección de columnas de la tabla. La sección estructura utilizará para cualquiera con información de tipo para las conversiones de tipos o realizando las asignaciones de columnas. Las secciones siguientes describen estas características en detalle. 

Cada columna contiene las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre | Nombre de la columna. | Sí |
| tipo | Tipo de datos de la columna. Consulte la sección de conversiones de tipo a continuación para más detalles sobre cuándo debe especificar información de tipo | No |
| referencia cultural | .NET según la referencia cultural que se utilizará cuando el tipo se especifica y es .NET tipo Datetime o Datetimeoffset. Valor predeterminado es "en-us".  | No |
| formato | Cadena de formato que se utilizará cuando el tipo se especifica y .NET tipo Datetime o Datetimeoffset. | No |

En el siguiente ejemplo se muestra la sección de estructura JSON para una tabla con tres columnas Id, nombre y lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilice las siguientes directrices sobre cuándo incluir información de "estructura" y qué desea incluir en la sección de la **estructura** .

- **Para orígenes de datos estructurados** que almacenar la información de esquema y el tipo de datos junto con los datos en Sí (fuentes como tabla de Azure SQL Server, Oracle, etc.), debe especificar la sección de "estructura" sólo si desea realizar una asignación de columna específicos de columnas de origen a columnas específicas de receptor y sus nombres no son la misma (consulte los detalles en la sección de asignación de columna a continuación). 

    Como se mencionó anteriormente, la información de tipos es opcional en la sección "estructura". Para los orígenes estructurados, ya está disponible la información de tipo como parte de la definición de conjunto de datos del almacén de datos, por lo que no debería incluir la información de tipo cuando se incluye la sección "estructura".
- **Esquema en orígenes de datos de lectura (específicamente Azure blob)** que puede almacenar datos sin almacenar ninguna información de tipo o esquema con los datos. Para estos tipos de orígenes de datos debe incluir "estructura" en los siguientes 2 casos:
    - Desea realizar una asignación de columna.
    - Cuando el conjunto de datos es un origen en una actividad de copia, puede proporcionar información de tipo en "estructura" y generador de datos utilizará esta información de tipo para la conversión a los tipos nativos del receptor. Consulte el artículo de [mover datos a y desde Azure Blob](../articles/data-factory/data-factory-azure-blob-connector.md) para obtener más información.

### <a name="supported-net-based-types"></a>Compatible. Tipos de red 
Factoría de datos admite los siguientes CLS compatibles con .NET según tipo valores para proporcionar información de tipo en "estructura" para el esquema de fuentes de datos leídos como Azure blob.

- Int16
- Int32 
- Int64
- Único
- Doble
- Decimal
- Byte]
- BOOL
- Cadena 
- GUID
- Fecha y hora
- DateTimeOffset
- TimeSpan 

Para Datetime y Datetimeoffset opcionalmente, también puede especificar la cadena "formato" y "cultura" para facilitar el análisis de la cadena de fecha y hora personalizada. Vea ejemplo de conversión de tipo a continuación.

