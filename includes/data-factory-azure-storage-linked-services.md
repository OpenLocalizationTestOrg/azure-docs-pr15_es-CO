## <a name="azure-storage-linked-service"></a>Almacenamiento de Azure vinculado servicio

El **almacenamiento de Azure vinculado servicio** permite vincular una cuenta de almacenamiento de Azure a un generador de datos Azure mediante la **clave de la cuenta**. Esto proporciona el generador de datos con acceso global para el almacenamiento de Azure. En la tabla siguiente proporciona la descripción elementos JSON específicos al servicio de almacenamiento de Azure vinculado.

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| tipo | Debe establecerse la propiedad type en: **AzureStorage** | Sí |
| connectionString | Especificar la información necesaria para conectarse al almacenamiento de Azure para la propiedad connectionString. | Sí |

Consulte el siguiente artículo para conocer los pasos para ver o copiar la clave de la cuenta para el almacenamiento de Azure: [Ver, copiar y las teclas de acceso de almacenamiento Regenerar (Regenerate)](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Ejemplo:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Sas de almacenamiento de Azure vinculado servicio  
Una firma de acceso compartido (SAS) proporciona delega el acceso a los recursos de su cuenta de almacenamiento. Esto significa que puede conceder a que un cliente de permisos limitados a los objetos en su cuenta de almacenamiento durante un período específico de tiempo y con un conjunto de permisos especificado sin tener que compartir las claves de acceso de la cuenta. El SA es un URI que abarca en sus parámetros de consulta toda la información necesaria para autenticar el acceso a un recurso de almacenamiento de información. Para tener acceso a los recursos de almacenamiento de información con las asociaciones de seguridad, el cliente sólo necesita pasar las asociaciones de seguridad al método o constructor adecuado. Para obtener más información acerca de SAS, [firmas de acceso compartido: descripción del modelo de SAS](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
El servicio de SAS de almacenamiento de Azure vinculado permite vincular una cuenta de almacenamiento de Azure a un generador de datos Azure mediante una firma de acceso compartido (SAS). Esto proporciona el generador de datos con acceso restringido o plazos específicas o todos los recursos (blob/contenedor) en el almacenamiento de información. En la tabla siguiente proporciona la descripción elementos JSON específicos al servicio de SAS de almacenamiento de Azure vinculado. 

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| tipo | Debe establecerse la propiedad type en: **AzureStorageSas**  | Sí |
| sasUri | Especificar URI de firma de acceso compartido a los recursos de almacenamiento de Azure como blob, contenedor o tabla. Vea las notas abajo para obtener más detalles. | Sí | 


**Ejemplo:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Al crear un **URI de SAS**, teniendo en cuenta lo siguiente:  

- Generador de datos Azure admite sólo **Servicio SAS**, SAS de cuenta no. Para obtener más información acerca de estos dos tipos, vea [Tipos de firmas de acceso compartido](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- Necesita **permisos** de lectura y escritura adecuados a establecerse en los objetos en función de cómo se utilizará el servicio vinculado (lectura, escritura, lectura y escritura) en el generador de datos.
- **Tiempo de expiración** debe establecerse correctamente. Asegúrese de que el acceso a objetos de almacenamiento de Azure no caduca dentro del período activo de la canalización.
- URI debe crearse en el nivel de tabla basado en la necesidad o el contenedor derecho/blob. Un Uri de SAS a un blob Azure permite al servicio de generador de datos tener acceso a ese objeto binario determinado. Un Uri de SAS a un contenedor de Azure blob permite al servicio de generador de datos recorrer los valores BLOB en ese contenedor. Si necesita proporcionar acceso más o menos objetos más tarde o actualice el URI SAS, recuerde actualizar el servicio vinculado con el nuevo URI.   
