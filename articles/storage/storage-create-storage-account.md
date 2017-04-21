<properties
    pageTitle="Cómo crear, administrar o eliminar una cuenta de almacenamiento de información en el Portal de Azure | Microsoft Azure"
    description="Crear una nueva cuenta de almacenamiento, administrar las claves de acceso de cuenta o eliminar una cuenta de almacenamiento de información en el Portal de Azure. Obtenga información acerca de las cuentas de almacenamiento estándar y premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Acerca de las cuentas de almacenamiento de Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Información general

Una cuenta de almacenamiento de Azure proporciona un namespace único para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure. Todos los objetos de una cuenta de almacenamiento se facturan juntos como un grupo. De forma predeterminada, los datos de tu cuenta están disponibles sólo para usted, el propietario de la cuenta.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Facturación de la cuenta de almacenamiento

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Cuando se crea una máquina virtual Azure, una cuenta de almacenamiento se crea automáticamente en la ubicación de implementación si no tiene ya una cuenta de almacenamiento en esa ubicación. Por lo que no es necesario seguir los pasos siguientes para crear una cuenta de almacenamiento para los discos de la máquina virtual. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual. Consulte la [documentación de máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para más detalles.

## <a name="storage-account-endpoints"></a>Extremos de la cuenta de almacenamiento

Todos los objetos que se almacenan en el almacenamiento de Azure tiene una dirección URL única. El nombre de la cuenta de almacenamiento constituye el subdominio de dicha dirección. La combinación de nombre de dominio y el subdominio, que es específico de cada servicio, forma un *extremo* para la cuenta de almacenamiento.

Por ejemplo, si su cuenta de almacenamiento se denomina *mystorageaccount*, los extremos predeterminados para la cuenta de almacenamiento son:

- Servicio de objetos binarios: http://*mystorageaccount*. blob.core.windows.net

- Tabla de servicio: http://*mystorageaccount*. table.core.windows.net

- Cola de servicio: http://*mystorageaccount*. queue.core.windows.net

- Servicio de archivos: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Una cuenta de almacenamiento Blob sólo expone el extremo de servicio de Blob.

La dirección URL para tener acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob puede tener este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*miblob*.

También puede configurar un nombre de dominio personalizado para utilizar con su cuenta de almacenamiento. Para cuentas de almacenamiento clásico, consulte [configurar un nombre para el extremo de almacenamiento Blob de dominio personalizado](storage-custom-domain-name.md) para obtener más detalles. Para las cuentas de almacenamiento de administrador de recursos, esta capacidad todavía no ha sido añadida al [portal de Azure](https://portal.azure.com) , pero se puede configurar con PowerShell. Para obtener más información, vea el cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Iniciar sesión en el [portal de Azure](https://portal.azure.com).

2. En el menú de concentrador, seleccione el **nuevo** -> **datos + almacenamiento** -> **cuenta de almacenamiento**.

3. Escriba un nombre para la cuenta de almacenamiento. Ver [extremos de almacenamiento cuenta](#storage-account-endpoints) para obtener más información sobre cómo se utilizará el nombre de la cuenta de almacenamiento de información para enfrentar sus objetos en el almacenamiento de Azure.

    > [AZURE.NOTE] Nombres de cuenta de almacenamiento deben estar entre 3 y 24 caracteres de longitud y pueden contener números y letras minúsculas solamente.
    >  
    > Nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El portal de Azure indicará si selecciona el nombre de cuenta de almacenamiento ya está en uso.

4. Especificar el modelo de implementación que se utilizará: **Administrador de recursos** o **clásico**. **Administrador de recursos** es el modelo de implementación recomendado. Para obtener más información, vea [Administrador de recursos del conocimiento y la implementación clásica](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Cuentas de almacenamiento BLOB sólo pueden crearse mediante el modelo de implementación del Administrador de recursos.

5. Seleccione el tipo de cuenta de almacenamiento: de **Propósito General** o **el almacenamiento de blobs**. **Propósito general** es el valor predeterminado.

    Si se ha seleccionado el **Propósito General** , a continuación, especifique el nivel de rendimiento: **Standard** o **Premium**. El valor predeterminado es **estándar**. Para obtener más detalles sobre las cuentas de almacenamiento estándar y premium, vea [Introducción al almacenamiento de Azure de Microsoft](storage-introduction.md) y [Premium almacenamiento: almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual](storage-premium-storage.md).

    Si se ha seleccionado **El almacenamiento de blobs** , a continuación, especifique el nivel de acceso: **caliente** o **fría**. El valor predeterminado es **activa**. Consulte [almacenamiento de Azure Blob: enfriar y caliente niveles](storage-blob-storage-tiers.md) para obtener más detalles.

6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS**, **GRS de RA**o **ZRS**. El valor predeterminado es **RA GRS**. Para obtener más detalles sobre las opciones de replicación de almacenamiento de Azure, vea [replicación de almacenamiento de Azure](storage-redundancy.md).

7. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

8. Especificar un grupo de recursos nuevo o seleccione un grupo de recursos existente. Para obtener más información sobre grupos de recursos, vea [Introducción al administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

9. Seleccione la ubicación geográfica de su cuenta de almacenamiento. Para obtener más información acerca de qué servicios están disponibles en qué región, vea [Regiones de Azure](https://azure.microsoft.com/regions/#services) .

10. Haga clic en **crear** para crear la cuenta de almacenamiento.

## <a name="manage-your-storage-account"></a>Administrar tu cuenta de almacenamiento

### <a name="change-your-account-configuration"></a>Cambiar la configuración de la cuenta

Después de crear la cuenta de almacenamiento, puede modificar su configuración, como cambiar la opción de replicación de la cuenta o cambiar el nivel de acceso para una cuenta de almacenamiento Blob. En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento, haga clic en **todas las configuraciones** y, a continuación, haga clic en **configuración** para ver o cambiar la configuración de la cuenta.

> [AZURE.NOTE] Según el nivel de rendimiento que eligió al crear la cuenta de almacenamiento, algunas opciones de replicación no esté disponibles.

Cambiar la opción de replicación, cambiará el precio. Para obtener más detalles, consulte la página [precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) .

Para las cuentas de almacenamiento Blob, cambiar el nivel de acceso puede incurrir en gastos para el cambio, además de cambiar los precios. Vea las [cuentas de almacenamiento Blob - facturación y precios](storage-blob-storage-tiers.md#pricing-and-billing) para obtener más detalles.

### <a name="manage-your-storage-access-keys"></a>Administrar las claves de acceso de almacenamiento de información

Cuando se crea una cuenta de almacenamiento, Azure genera dos teclas de acceso de almacenamiento de 512 bits, que se utilizan para la autenticación cuando se tiene acceso a la cuenta de almacenamiento. Proporcionando dos teclas de acceso de almacenamiento Azure permite regenerar las claves con sin interrupciones en el servicio de almacenamiento de información o el acceso a dicho servicio.

> [AZURE.NOTE] Se recomienda evitar el uso compartido de las claves de acceso de almacenamiento de información con nadie. Para permitir el acceso a los recursos de almacenamiento de información sin entregar las claves de acceso, puede utilizar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso en su cuenta para un intervalo en el que define y con los permisos que especifique. Para obtener más información, vea [Utilizando firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Ver y copiar las teclas de acceso de almacenamiento de información

En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento, haga clic en **todas las configuraciones** y, a continuación, haga clic en **las teclas de acceso** para ver, copiar y volver a generar las claves de acceso de la cuenta. El módulo de **Teclas de acceso** también incluye cadenas de conexión configuradas previamente mediante sus claves primarias y secundarias que puede copiar para utilizar en las aplicaciones.

#### <a name="regenerate-storage-access-keys"></a>Generar las claves de acceso de almacenamiento de información

Se recomienda cambiar las teclas de acceso a su cuenta de almacenamiento periódicamente para ayudar a proteger las conexiones de almacenamiento de información. Dos teclas de acceso se asignan de forma que puede mantener las conexiones a la cuenta de almacenamiento utilizando una tecla de acceso mientras se regenera la otra tecla de acceso.

> [AZURE.WARNING] Volver a generar las claves de acceso puede afectar a servicios de Azure, así como sus propias aplicaciones que dependen de la cuenta de almacenamiento. Todos los clientes que utilizan la clave de acceso para tener acceso a la cuenta de almacenamiento deben actualizarse para utilizar la nueva clave.

**Servicios de medios** : si tiene servicios de medios que dependen de su cuenta de almacenamiento, deben volver a sincronizar las teclas de acceso con el servicio de medios de comunicación después de regenerar las claves.

**Aplicaciones** - si tienes aplicaciones web o servicios en la nube que utilice la cuenta de almacenamiento, se perderán las conexiones si regenerar las claves, a menos que se restaure las claves.

**Exploradores de almacenamiento** : si está utilizando las [aplicaciones de explorador de almacenamiento de información](storage-explorers.md), probablemente necesite actualizar la clave de almacenamiento de información utilizada por las aplicaciones.

Aquí es el proceso de rotación de las claves de acceso de almacenamiento de información:

1. Actualizar las cadenas de conexión en el código de la aplicación para hacer referencia a la clave de acceso secundario de la cuenta de almacenamiento.

2. Regenerar la clave de acceso principal para su cuenta de almacenamiento. En el módulo de **Teclas de acceso** , haga clic en **Regenerar Key1**y, a continuación, haga clic en **Sí** para confirmar que desea generar una nueva clave.

3. Actualizar las cadenas de conexión en el código para hacer referencia a la nueva clave de acceso principal.

4. Regenerar la clave de acceso secundaria de la misma manera.

## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no utilizas, vaya a la cuenta de almacenamiento de información en el [portal de Azure](https://portal.azure.com)y haga clic en **Eliminar**. Eliminar una cuenta de almacenamiento, elimina toda la cuenta, incluyendo todos los datos de la cuenta.

> [AZURE.WARNING] No es posible restaurar una cuenta de almacenamiento eliminados o recuperar cualquier contenido que contenía antes de la eliminación. Asegúrese de hacer copia de seguridad que desee guardar antes de eliminar la cuenta. Esto también es válido para todos los recursos en la cuenta: una vez que elimine un blob, tabla, cola o archivo, se elimina definitivamente.

Para eliminar una cuenta de almacenamiento que está asociada a una máquina virtual Azure, primero debe asegurarse de que se han eliminado los discos de la máquina virtual. Si no elimina primero los discos de la máquina virtual, a continuación, cuando se intenta eliminar la cuenta de almacenamiento, verá un mensaje de error similar al:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si la cuenta de almacenamiento utiliza el modelo de implementación estándar, puede quitar el disco de la máquina virtual, siga estos pasos en el [portal de Azure](https://manage.windowsazure.com):

1. Desplácese hasta el [portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la ficha de máquinas virtuales.
3. Haga clic en la ficha de discos.
4. Seleccione su disco de datos, haga clic en disco de eliminar.
5. Para eliminar las imágenes de disco, vaya a la ficha imágenes y elimine las imágenes que se almacenan en la cuenta.

Para obtener más información, consulte la [documentación de la máquina Virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Próximos pasos

- [Almacenamiento de Azure Blob: Niveles frío y caliente](storage-blob-storage-tiers.md)
- [Replicación de almacenamiento de Azure](storage-redundancy.md)
- [Configurar cadenas de conexión de almacenamiento de Azure](storage-configure-connection-string.md)
- [Transferencia de datos con la utilidad de línea de comandos de AzCopy](storage-use-azcopy.md)
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).
