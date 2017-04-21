### <a name="compression-support"></a>Compatibilidad con la compresión  
Procesamiento de grandes conjuntos de datos puede causar cuellos de botella de E/S y red. Por lo tanto, los datos comprimidos en tiendas pueden no sólo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, pero también aportan importantes mejoras de rendimiento en el procesamiento de datos grandes. En la actualidad, la compresión es compatible para almacenes de datos basados en archivos como Azure Blob o sistema de archivos local.  

> [AZURE.NOTE] No se admite la configuración de compresión para los datos en el **AvroFormat**, **OrcFormat**o **ParquetFormat**. 

Para especificar la compresión para un conjunto de datos, utilice la propiedad **compression** del conjunto de datos JSON como en el ejemplo siguiente:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La sección de **compresión** tiene dos propiedades:  
  
- **Tipo:** el códec de compresión, que puede ser **GZIP**y **Deflate** , **BZIP2**.  
- **Nivel:** la razón de compresión, que puede ser **óptima** o **más rápida**. 
    - **Más rápido:** La operación de compresión debe completar lo más rápidamente posible, incluso si el archivo resultante no se comprime de forma óptima. 
    - **Optimal**: la operación de compresión debe estar óptimamente comprimida, incluso si la operación tarda más tiempo en completarse. 
    
    Para obtener más información, vea el tema de [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supongamos que el conjunto de datos de ejemplo anterior se utiliza como el resultado de una actividad de copia, la actividad de copia comprime los datos de salida con el códec GZIP con proporción óptima y, a continuación, escriba los datos comprimidos en un archivo denominado pagecounts.csv.gz en el almacenamiento de Azure Blob.   

Cuando se especifica la propiedad compression en un conjunto de datos de entrada JSON, la canalización puede leer datos comprimidos desde el origen y cuando se especifica la propiedad de un conjunto de datos de salida JSON, la actividad de copia puede escribir datos comprimidos en el destino. A continuación presentamos algunos escenarios de ejemplo: 

- Datos comprimidos de GZIP de lectura de un blob de Azure, descomprimirlo y escribir datos de resultados en una base de datos de SQL Azure. Definir el conjunto de datos de entrada Azure Blob con la propiedad JSON de compresión en este caso. 
- Leer datos desde un archivo de texto sin formato del sistema de archivos local, comprimir con formato GZip y escribir los datos comprimidos en un blob de Azure. Definir un conjunto de datos de Azure Blob de salida con la propiedad JSON de compresión en este caso.  
- Leer los datos comprimidos con GZIP de un blob de Azure, descomprimir el archivo, comprimirlo mediante BZIP2 y escribir datos de resultados en un objeto binario de Azure. Definir el conjunto de datos de Azure Blob entrada con el tipo de compresión establecido en GZIP y el conjunto de datos de salida con el tipo de compresión establecido a BZIP2 en este caso.   
