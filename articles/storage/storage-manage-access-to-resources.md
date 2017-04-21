<properties
    pageTitle="Administre el acceso de lectura anónimo a contenedores y objetos binarios | Microsoft Azure"
    description="Aprenda cómo hacer contenedores y objetos binarios estén disponibles para el acceso anónimo y tener acceso a ellos mediante programación."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Administre el acceso de lectura anónimo a contenedores y objetos binarios

## <a name="overview"></a>Información general

De forma predeterminada, sólo el propietario de la cuenta de almacenamiento puede tener acceso a los recursos de almacenamiento de información dentro de esa cuenta. Para el almacenamiento de blobs, puede establecer los permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y sus blobs, por lo que se puede conceder acceso a dichos recursos sin compartir la clave de la cuenta.

El acceso anónimo es mejor para escenarios donde desee ciertos blobs esté siempre disponible para el acceso de lectura anónimo. Para un control preciso, puede crear una firma de acceso compartido, que permite el acceso delegado restringido mediante permisos diferentes y durante un intervalo de tiempo especificado. Para obtener más información sobre cómo crear firmas de acceso compartido, consulte [Uso compartido acceso firmas (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permisos de usuarios anónimos a contenedores y objetos binarios

De forma predeterminada, un contenedor y los objetos binarios dentro de él pueden tener acceso a sólo el propietario de la cuenta de almacenamiento. Para dar a los usuarios anónimos leer permisos a un contenedor y sus blobs, puede establecer los permisos del contenedor para permitir el acceso del público. Los usuarios anónimos pueden leer objetos binarios dentro de un contenedor accesible públicamente sin autenticar la solicitud.

Los contenedores proporcionan las siguientes opciones para administrar el acceso de contenedor:

- **Pública acceso de lectura total:** Contenedor y el blob de datos se pueden leer mediante solicitud anónima. Los clientes pueden enumerar blobs dentro del contenedor mediante solicitud anónima, pero no pueden enumerar los contenedores dentro de la cuenta de almacenamiento.

- **Público, acceso para blobs sólo de lectura:** Datos BLOB dentro de este contenedor pueden leerse mediante solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs dentro del contenedor mediante solicitud anónima.

- **Acceso de lectura no público:** Contenedor y el blob de datos se pueden leer sólo el propietario de la cuenta.

Puede establecer los permisos del contenedor de las maneras siguientes:

- Desde el [Portal de Azure](https://portal.azure.com).
- Mediante programación, usando la biblioteca de cliente de almacenamiento de información o la API de REST.
- Mediante el uso de PowerShell. Para obtener información acerca de la configuración de permisos del contenedor de PowerShell de Azure, consulte [Usar PowerShell de Azure con el almacenamiento de Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Permisos del contenedor de configuración desde el Portal de Azure

Para establecer los permisos del contenedor desde el [Portal de Azure](https://portal.azure.com), siga estos pasos:

1. Vaya al panel de su cuenta de almacenamiento.
2. Seleccione el nombre del contenedor de la lista. Al hacer clic en el nombre expone los BLOB en el contenedor elegido
3. Seleccione **la directiva de acceso** desde la barra de herramientas.
4. En el campo **tipo de acceso** , seleccione el nivel de permisos deseado como se muestra en la captura de pantalla siguiente.

    ![Editar metadatos de contenedor diálogo](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Permisos del contenedor de configuración mediante programación con .NET

Para establecer permisos para un contenedor utilizando la biblioteca de cliente .NET, en primer lugar, recuperar los permisos existentes del contenedor llamando al método **GetPermissions** . A continuación, establezca la propiedad **PublicAccess** para el objeto **BlobContainerPermissions** que es devuelto por el método **GetPermissions** . Por último, llame al método **SetPermissions** con los permisos actualizados.

El ejemplo siguiente establece los permisos del contenedor para acceso de lectura pública completa. Para establecer permisos para acceso de lectura pública para blobs sólo, establezca la propiedad de **PublicAccess** en **BlobContainerPublicAccessType.Blob**. Para quitar todos los permisos para los usuarios anónimos, establezca la propiedad en **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Tener acceso a contenedores y objetos binarios de forma anónima

Un cliente que tiene acceso a los contenedores y objetos binarios de forma anónima puede utilizar constructores que no requieran credenciales. Los ejemplos siguientes muestran algunas maneras diferentes para hacer referencia a recursos de servicio de Blob anónimamente.

### <a name="create-an-anonymous-client-object"></a>Crear un objeto de cliente anónimo

Puede crear un nuevo objeto de cliente de servicio para el acceso anónimo al proporcionar el extremo de servicio de Blob de la cuenta. Sin embargo, debe conocer también el nombre de un contenedor en esa cuenta que está disponible para el acceso anónimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Hacer referencia a un contenedor de forma anónima

Si tiene la dirección URL a un contenedor que está disponible de forma anónima, puede utilizar referencia directamente el contenedor.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Hacer referencia a un blob de forma anónima

Si tiene la dirección URL a un objeto binario que está disponible para el acceso anónimo, puede hacer referencia el blob directamente con esa dirección URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Características disponibles para los usuarios anónimos

La siguiente tabla muestra las operaciones que pueden denominarse los usuarios anónimos cuando ACL del contenedor está configurado para permitir el acceso público.

| Operación de resto                                         | Permiso con acceso de lectura pública completa | Permiso con acceso de lectura pública para blobs sólo |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Contenedores de la lista                                        | Sólo el propietario                              | Sólo el propietario                                        |
| Crear contenedor                                       | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener propiedades de contenedor                               | Todos los                                     | Sólo el propietario                                        |
| Obtener metadatos del contenedor                                 | Todos los                                     | Sólo el propietario                                        |
| Metadatos del contenedor de conjunto                                 | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener la ACL del contenedor                                      | Sólo el propietario                              | Sólo el propietario                                        |
| Establecer ACL del contenedor                                      | Sólo el propietario                              | Sólo el propietario                                        |
| Eliminar el contenedor                                       | Sólo el propietario                              | Sólo el propietario                                        |
| Lista Blobs                                             | Todos los                                     | Sólo el propietario                                        |
| Poner Blob                                               | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener el Blob                                               | Todos los                                     | Todos los                                               |
| Obtener propiedades de Blob                                    | Todos los                                     | Todos los                                               |
| Establecer propiedades de Blob                                    | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener metadatos de Blob                                      | Todos los                                     | Todos los                                               |
| Establecer los metadatos de Blob                                      | Sólo el propietario                              | Sólo el propietario                                        |
| Coloque el bloque                                              | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener lista de bloque (sólo bloques comprometidos)                 | Todos los                                     | Todos los                                               |
| Obtener lista de bloque (sólo bloques no comprometidos o todos los bloques) | Sólo el propietario                              | Sólo el propietario                                        |
| Coloque la lista de bloqueo                                         | Sólo el propietario                              | Sólo el propietario                                        |
| Eliminar Blob                                            | Sólo el propietario                              | Sólo el propietario                                        |
| Copiar Blob                                              | Sólo el propietario                              | Sólo el propietario                                        |
| Blob de instantánea                                          | Sólo el propietario                              | Sólo el propietario                                        |
| Blob de concesión                                             | Sólo el propietario                              | Sólo el propietario                                        |
| Publicar una página                                               | Sólo el propietario                              | Sólo el propietario                                        |
| Obtener intervalos de páginas                                        | Todos los                                     | Todos los                                                  |
| Anexar Blob                                            | Sólo el propietario                              | Sólo el propietario                                                  |


## <a name="see-also"></a>Vea también

- [Autenticación de los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [El uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Delegar el acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx)
