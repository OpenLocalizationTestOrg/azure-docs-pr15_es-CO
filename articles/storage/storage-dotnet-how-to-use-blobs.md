<properties
    pageTitle="Introducción a almacenamiento Azure Blob (almacenamiento de objetos) mediante .NET | Microsoft Azure"
    description="Almacenar datos no estructurados en la nube con almacenamiento Azure Blob (almacenamiento de información de objeto)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Introducción a almacenamiento Azure Blob mediante .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o BLOB. Almacenamiento de blobs puede almacenar cualquier tipo de texto o datos binarios, como un documento, un archivo multimedia o un instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

### <a name="about-this-tutorial"></a>Acerca de este tutorial

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes de uso de almacenamiento Azure Blob. Escenarios cubiertos incluyen carga, listado, descargar y eliminar objetos binarios.

**Tiempo estimado para completar:** 45 minutos

**Requisitos previos:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Administrador de configuración de Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Más ejemplos

Para obtener ejemplos adicionales mediante el almacenamiento de blobs, consulte [Introducción a almacenamiento de Azure Blob en. NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Puede descargar la aplicación de ejemplo y ejecutarlo o examinar el código en GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Agregue las declaraciones de espacio de nombres

Agregue el siguiente `using` instrucciones a la parte superior de la `program.cs` archivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Crear al cliente de servicios de Blob

La clase **CloudBlobClient** permite recuperar los contenedores y objetos binarios almacenados en el almacenamiento de blobs. Esta es una forma para crear al cliente del servicio:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Ahora está preparado para escribir código que lee y escribe datos en el almacenamiento de blobs.

## <a name="create-a-container"></a>Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si aún no existe:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

De forma predeterminada, el nuevo contenedor es privado, lo que significa que debe especificar la clave de acceso de almacenamiento de información para descargar blobs de este contenedor. Si desea disponer de los archivos en el contenedor para todos los usuarios, puede establecer el contenedor públicos utilizando el código siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Cualquier usuario de Internet puede ver valores BLOB en un contenedor público, pero pueden modificar o eliminar sólo si tiene la tecla de acceso de la cuenta correspondiente o una firma de acceso compartido.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Almacenamiento de Azure Blob admite BLOB de bloque y blobs de página.  En la mayoría de los casos, blob de bloque es el tipo recomendado para utilizar.

Para cargar un archivo a un blob de bloque, obtenga una referencia de contenedor y utilizarlo para obtener una referencia de objeto binario de bloque. Una vez que tenga una referencia de objeto binario, puede cargar cualquier secuencia de datos a él llamando al método **UploadFromStream** . Esta operación creará el blob si no existe previamente o sobrescribirlo si existe.

En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para enumerar los BLOB en un contenedor, hay que obtener primero una referencia de contenedor. A continuación, puede utilizar el método de **ListBlobs** del contenedor para recuperar los objetos binarios o directorios dentro de él. Para tener acceso el amplio conjunto de propiedades y métodos para un devuelto **IListBlobItem**, debe convertirlo a un objeto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** .  Si el tipo es desconocido, puede utilizar para determinar qué convertirlo a una comprobación de tipo.  El código siguiente muestra cómo recuperar y salida el URI de cada elemento de la `photos` contenedor:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Como se indicó anteriormente, puede nombrar los BLOB con la información de ruta de acceso en sus nombres. Esto crea una estructura de directorios virtuales que se puede organizar y recorrer como lo haría con un sistema de archivos tradicional. Tenga en cuenta que la estructura de directorios sólo es virtual, los únicos recursos disponibles en el almacenamiento de blobs son contenedores y objetos binarios. Sin embargo, la biblioteca de cliente de almacenamiento ofrece un objeto **CloudBlobDirectory** para referirse a un directorio virtual y simplificar el proceso de trabajar con valores BLOB que se organiza en esta forma.

Por ejemplo, considere el siguiente conjunto de objetos binarios de bloque en un contenedor denominado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Cuando se llama a **ListBlobs** en el contenedor 'fotografías' (como en el ejemplo anterior), se devuelve una lista jerárquica. Contiene objetos **CloudBlobDirectory** y **CloudBlockBlob** , que representan los directorios y valores BLOB en el contenedor, respectivamente. La salida resultante tiene el siguiente aspecto:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, puede establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. En este caso, cada objeto binario en el contenedor se devuelve como un objeto **CloudBlockBlob** . La llamada a **ListBlobs** para devolver una lista plana tiene el siguiente aspecto:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

y el resultado tiene este aspecto:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Descargar BLOB

Descargar blobs, primero recuperar una referencia de objeto binario y, a continuación, llame al método **DownloadToStream** . En el ejemplo siguiente se utiliza el método **DownloadToStream** para transferir el contenido de blob en un objeto stream que luego puede almacenar en un archivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede utilizar el método **DownloadToStream** para descargar el contenido de un blob como una cadena de texto.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar los blobs

Para eliminar un objeto binario, primero obtenga una referencia de objeto binario y, a continuación, llama al método **Delete** en él.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Lista valores BLOB en páginas de forma asincrónica

Si estás anunciando un gran número de objetos binarios o desea controlar el número de resultados que devuelven en una sola operación de anuncio, puede enumerar valores BLOB en páginas de resultados. Este ejemplo muestra cómo devolver los resultados en páginas de forma asincrónica, por lo que la ejecución no se bloquea mientras espera a que se devuelva un gran conjunto de resultados.

Este ejemplo muestra un listado de blob plana, pero también puede realizar una lista jerárquica, estableciendo la `useFlatBlobListing` parámetro del método **ListBlobsSegmentedAsync** para `false`.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido del `async` (palabra clave) y deben devolver un objeto **Task** . La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método muestra hasta que finaliza la tarea de la lista.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Escritura de un blob de datos anexados

Un blob de datos anexados es un nuevo tipo de blob, introducida con la versión 5.x de la biblioteca de cliente de almacenamiento de Azure para. NET. Un blob de datos anexados está optimizado para operaciones de datos anexados, tales como el registro. Como un blob de bloque, un blob de datos anexados se compone de bloques, pero cuando se agrega un nuevo bloque a un blob de datos anexados, siempre se anexan al final del objeto binario. No se puede actualizar o eliminar un bloque existente en un objeto binario de datos anexados. El bloque de identificadores para un blob de datos anexados no se exponen como lo son para un blob de bloque.

Cada bloque de un blob de datos anexados puede tener un tamaño diferente, hasta un máximo de 4 MB, y un objeto binario de datos anexados puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob de datos anexados, por tanto, es un poco más de 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob de datos anexados y anexa algunos datos, simulando una operación de registro simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Consulte [comprensión bloque Blobs, Blobs de página y anexar Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obtener más información acerca de las diferencias entre los tres tipos de objetos binarios.

## <a name="managing-security-for-blobs"></a>Administración de la seguridad para blobs

De forma predeterminada, el almacenamiento de Azure protege sus datos limitando el acceso al propietario de la cuenta, que esté en posesión de las claves de acceso de la cuenta. Cuando necesite compartir datos blob en su cuenta de almacenamiento, es importante hacerlo sin poner en peligro la seguridad de las claves de acceso de la cuenta. Además, puede cifrar los datos de blob para garantizar que es seguro pasar por la red y el almacenamiento de Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controlar el acceso a los datos blob

De forma predeterminada, los datos blob en su cuenta de almacenamiento de información son accesibles sólo para el propietario de la cuenta de almacenamiento. Autenticación de solicitudes de almacenamiento de blobs, requiere la clave de acceso de la cuenta predeterminada. Sin embargo, puede hacer que determinados datos blob disponible para otros usuarios. Tiene dos opciones:

- **El acceso anónimo:** Para hacer un contenedor o sus blobs públicamente disponible para el acceso anónimo. Para obtener más información, vea [Administrar acceso de lectura anónimo a contenedores y objetos binarios](storage-manage-access-to-resources.md) .
- **Shared acceso firmas:** Proporcionar a los clientes con una firma de acceso compartido (SAS), que proporciona acceso delegado a un recurso en su cuenta de almacenamiento, con los permisos que especifique y en un intervalo que especifique. Para obtener más información, vea [Utilizando firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Cifrar datos blob

Almacenamiento de Azure permite cifrar los datos blob en el cliente y en el servidor:

- **Cifrado de cliente:** La biblioteca de cliente de almacenamiento de información para .NET admite el cifrado de datos dentro de las aplicaciones de cliente antes de cargar para el almacenamiento de Azure y descifrado de datos mientras se descarga al cliente. La biblioteca también admite la integración con Azure clave Vault para administración de almacenamiento de información cuenta clave. Para obtener más información, vea [Cifrado de cliente con .NET para el almacenamiento de Azure de Microsoft](storage-client-side-encryption.md) . Consulte también [Tutorial: cifrar y descifrar valores BLOB en almacenamiento de Azure de Microsoft mediante depósito de Azure clave](storage-encrypt-decrypt-blobs-key-vault.md).
- **Cifrado del servidor**: el almacenamiento de Azure ahora admite el cifrado del servidor. Vea [almacenamiento de Azure servicio cifrado de datos en reposo (vista previa)](storage-service-encryption.md).

## <a name="next-steps"></a>Próximos pasos

Ahora que ha aprendido los conceptos básicos de almacenamiento de blobs, siga estos vínculos para obtener más información.

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Azure de Microsoft
- [Explorador de almacenamiento de Azure de Microsoft (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación gratuita, de independiente de Microsoft que le permite trabajar visualmente con los datos del almacenamiento de Azure en Windows, OS X y Linux.

### <a name="blob-storage-samples"></a>Ejemplos de almacenamiento BLOB

- [Introducción a almacenamiento Azure Blob en .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referencia de almacenamiento BLOB

- [Biblioteca de cliente de almacenamiento de información de referencia de .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guías conceptuales

- [Transferencia de datos con la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)
- [Introducción a almacenamiento de archivos para .NET](storage-dotnet-how-to-use-files.md)
- [Cómo utilizar almacenamiento Azure blob con el SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
