<properties
    pageTitle="Crear una cuenta de Azure lote | Microsoft Azure"
    description="Aprenda a crear una cuenta de Azure lote en el portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Crear una cuenta de Azure lote mediante el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](batch-account-create-portal.md)
- [Gestión de lotes .NET](batch-management-dotnet.md)

Aprenda a crear una cuenta de Azure lote en el [portal de Azure][azure_portal]y dónde encontrar importantes propiedades de la cuenta, como las teclas de acceso y las direcciones URL de cuentas. También analizaremos por lotes precios y vincular una cuenta de almacenamiento de Azure para la cuenta de proceso por lotes para que puedan utilizar los [paquetes de aplicaciones](batch-application-packages.md) y [conservar los resultados de trabajo y tareas](batch-task-output.md).

## <a name="create-a-batch-account"></a>Crear una cuenta de proceso por lotes

1. Iniciar sesión en el [portal de Azure][azure_portal].

2. Haga clic en **nuevo** > **Compute** > **servicio de lote**.

    ![Lote en el mercado][marketplace_portal]

3. Se muestra la hoja **Nueva cuenta por lotes** . Ver elementos *un* a *e* a continuación para obtener descripciones de cada elemento de la hoja.

    ![Crear una cuenta de proceso por lotes][account_portal]

    a. **Nombre de cuenta**: un nombre único para la cuenta de proceso por lotes. Este nombre debe ser único dentro de la región de Azure que se crea la cuenta (consulte la *ubicación* ). Puede contener únicamente caracteres en minúsculas, números y debe ser 3-24 caracteres de longitud.

    b. **Suscripción**: una suscripción en la que se va a crear la cuenta de proceso por lotes. Si tiene sólo una suscripción, se selecciona de forma predeterminada.

    c. **Grupo de recursos**: un recurso existente de grupo para la nueva cuenta de lote o, opcionalmente, crear uno nuevo.

    d. **Ubicación**: región de un Azure en la que se va a crear la cuenta de proceso por lotes. Sólo las regiones compatibles con su suscripción y el grupo de recursos se muestran como opciones.

    e. **Cuenta de almacenamiento** (opcional): una cuenta de almacenamiento de **Propósito General** asociar (vincular) a la nueva cuenta de proceso por lotes. Vea [cuenta de almacenamiento de Azure vinculado](#linked-azure-storage-account) a continuación para obtener más detalles.

4. Haga clic en **crear** para crear la cuenta.

  El portal indica que es **implementar** la cuenta y, al finalizar, aparece una notificación de **éxito de las implementaciones** en *las notificaciones*.

## <a name="view-batch-account-properties"></a>Ver propiedades de la cuenta de proceso por lotes

Una vez creada la cuenta, puede abrir la **hoja de lote cuenta** para tener acceso a sus propiedades y configuración. Puede tener acceso a todas las propiedades y configuración de la cuenta utilizando el menú de la izquierda de la hoja de cuenta por lotes.

![Hoja de cuenta por lotes en el portal de Azure][account_blade]

* **Lote cuenta URL**: aplicaciones creadas con el [desarrollo de Batch API](batch-technical-overview.md#batch-development-apis) necesitan una dirección URL de cuenta para administrar los recursos y ejecutar trabajos en la cuenta. Un lote cuenta URL tiene el formato siguiente:

    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de cuenta por lotes en el portal][account_url]

* **Teclas de acceso**: las aplicaciones también necesitan una tecla de acceso cuando se trabaja con recursos en su cuenta por lotes. Para ver o volver a generar las claves de acceso de la cuenta de proceso por lotes, escriba `keys` en el cuadro de **búsqueda** del menú de la izquierda en la hoja de cuenta por lotes, a continuación, seleccione **las teclas**.

    ![Claves de cuenta por lotes en el portal de Azure][account_keys]

## <a name="pricing"></a>Precios

Cuentas de proceso por lotes se ofrecen sólo en un "nivel libre", lo que significa que no está a cargo de la propia cuenta de lote. Se cargan los recursos subyacentes de Azure compute que consumen las soluciones por lotes y los recursos utilizados por otros servicios cuando ejecutan sus cargas de trabajo. Por ejemplo, se le cargará para los nodos de cálculo en los grupos y para los datos almacena en el almacenamiento de Azure como entrada o salida para las tareas. De forma similar, si utiliza la característica de [paquetes de aplicaciones](batch-application-packages.md) de proceso por lotes, se cargan los recursos de almacenamiento de Azure utilizados para almacenar los paquetes de aplicaciones. Consulte [precios por lotes] [ batch_pricing] para obtener más información.

## <a name="linked-azure-storage-account"></a>Cuenta vinculada de almacenamiento de Azure

Como se mencionó anteriormente, puede vincular una cuenta de almacenamiento de **Propósito General** (opcionalmente) a la cuenta de proceso por lotes. La característica de [paquetes de aplicaciones](batch-application-packages.md) de proceso por lotes utiliza el almacenamiento de blobs en una cuenta de almacenamiento vinculado de propósito General que la biblioteca de [.NET de convenciones de archivo por lotes](batch-task-output.md) . Estas características opcionales ayudará a implementar las aplicaciones que se ejecutan las tareas por lotes y conservar los datos que generan.

Lote actualmente admite *sólo* el tipo de cuenta de almacenamiento de información de **Propósito General** como se describe en el paso 5, [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account), [cuentas de almacenamiento acerca de Azure](../storage/storage-create-storage-account.md). Al vincular una cuenta de almacenamiento de Azure para la cuenta de proceso por lotes, ser seguro de vínculo *sólo* una cuenta de almacenamiento de **Propósito General** .

![Crear una cuenta de almacenamiento de información de "Propósito General"][storage_account]

Se recomienda que cree una cuenta de almacenamiento para uso exclusivo por su cuenta por lotes.

>[AZURE.WARNING] Tenga cuidado al volver a generar las claves de acceso de una cuenta de almacenamiento vinculada. Regenerar solo clave de la cuenta de almacenamiento y haga clic en **Teclas de sincronización** en el servidor blade cuenta de almacenamiento vinculado. Espere cinco minutos para que las teclas se propagan a los nodos de cálculo en los grupos, a continuación, volver a generar y sincronizar la otra clave si es necesario. Si vuelve a generar las dos teclas al mismo tiempo, los nodos de cálculo no podrá sincronizar cualquier tecla y se pierde el acceso a la cuenta de almacenamiento.

  ![Regeneración de claves de la cuenta de almacenamiento][4]

## <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio por lotes

Ten en cuenta que al igual que con la suscripción de Azure y otros servicios de Azure, determinadas [cuotas y límites](batch-quota-limit.md) aplicables a las cuentas por lotes. Cuotas actuales para una cuenta de lote aparecen en el portal de **Propiedades**de la cuenta.

![Cuotas de cuenta por lotes en el portal de Azure][quotas]

Tenga estos contingentes en cuenta cuando diseñan y escalar sus cargas de trabajo por lotes. Por ejemplo, si su grupo no alcanzar el número de destino de los nodos de cálculo que haya especificado, se haya alcanzado el límite de cuota de núcleo para la cuenta de proceso por lotes.

Tenga en cuenta que no se limitan a una única cuenta de lote para la suscripción de Azure. Puede ejecutar múltiples cargas de trabajo por lotes en una única cuenta de proceso por lotes, o distribuir las cargas de trabajo entre cuentas de proceso por lotes en la suscripción de la misma, pero en diferentes regiones de Azure.

Muchos de estos contingentes pueden aumentarse simplemente con una solicitud de soporte técnico de producto gratuito presentada en el portal de Azure. Para obtener más información sobre cómo se solicita aumentar la cuota, consulte [cuotas y límites para el servicio de proceso de Azure](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de proceso por lotes

Además de utilizar el portal de Azure, también puede crear y administrar cuentas de proceso por lotes con lo siguiente:

* [Cmdlets de PowerShell lote](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](../xplat-cli-install.md)
* [Gestión de lotes .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximos pasos

* Vea la [Introducción a las características Azure lote](batch-api-basics.md) para obtener más información sobre características y conceptos de servicio de lote. El artículo analiza los recursos primarios de lote como piscinas, nodos de cómputo, trabajos y tareas y proporciona una visión general de las características del servicio que permiten la ejecución de la carga de trabajo de cálculo a gran escala.

* Conozca los principios básicos del desarrollo de una aplicación habilitada por lotes utilizando la [biblioteca de cliente .NET de lote](batch-dotnet-get-started.md). El [artículo de introducción](batch-dotnet-get-started.md) le guiará por una aplicación operativa que utiliza el servicio de proceso por lotes para ejecutar una carga de trabajo en varios nodos de cálculo y que incluye el uso de almacenamiento de Azure para ensayo de archivo de carga de trabajo y recuperación.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneración de claves de la cuenta de almacenamiento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
