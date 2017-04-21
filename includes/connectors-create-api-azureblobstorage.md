### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure; puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una [cuenta de Azure Blob almacenamiento](../articles/storage/storage-create-storage-account.md) incluyendo el nombre de la cuenta de almacenamiento y su clave de acceso. Esta información aparece en las propiedades de la cuenta de almacenamiento de información en el portal de Azure. Lea más sobre [El almacenamiento de Azure](../articles/storage/storage-introduction.md).

Antes de utilizar su cuenta de almacenamiento de Azure Blob en la lógica de la aplicación, conectarse a su cuenta de almacenamiento de Azure Blob. Puede hacerlo fácilmente dentro de la lógica de la aplicación en el portal de Azure.  

Conectarse a su cuenta de almacenamiento de Azure Blob mediante los pasos siguientes:  

1. Crear una lógica de la aplicación. En el Diseñador de aplicaciones de lógica, agregar un desencadenador y, a continuación, agregar una acción. Seleccione **Mostrar Microsoft API administradas** en la lista desplegable y, a continuación, escriba "objeto binario" en el cuadro de búsqueda. Seleccione una de las acciones:  

    ![Paso de creación de conexión de almacenamiento Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Si no ha creado ninguna conexión con el almacenamiento de Azure, se le pedirá que especifique los detalles de conexión:   

    ![Paso de creación de conexión de almacenamiento Blob Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Especifique los detalles de la cuenta de almacenamiento. Propiedades con un asterisco son necesarios.

    | Propiedad | Detalles |
|---|---|
| Nombre de conexión * | Escriba un nombre para la conexión. |
| Nombre de la cuenta de almacenamiento de Azure * | Escriba el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento de información se muestra en las propiedades de almacenamiento de información en el portal de Azure. |
| Tecla de acceso de almacenamiento de Azure cuenta * | Introduzca la clave de la cuenta de almacenamiento. Se muestran las teclas de acceso en las propiedades de almacenamiento de información en el portal de Azure. |

    Estas credenciales se utilizan para autorizar su lógica de la aplicación para conectarse y tener acceso a los datos. 

4. Seleccione **crear**.

5. Observe que la conexión se ha creado. Ahora, continúe con el resto de los pasos en la lógica de la aplicación: 

    ![Paso de creación de conexión de almacenamiento Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
