<properties
    pageTitle="Proteger la API con la administración de la API de Azure | Microsoft Azure"
    description="Aprenda a proteger su API con cuotas y limitación de las directivas (limitación de velocidad)."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteger su API con límites de velocidad utilizando la administración de la API de Azure

Esta guía muestra lo fácil que es agregar protección para la API de back-end mediante la configuración de directivas de límite y cuota de tasa con la administración de la API de Azure.

En este tutorial, creará un producto "Gratuita" API que permite a los desarrolladores realizar hasta 10 llamadas por minuto y hasta un máximo de 200 llamadas por semana a la API con la [tarifa de llamada de límite por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y directivas de [cuota de uso conjunto por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . A continuación la API de publicación y probar la directiva de límite de velocidad.

Para escenarios más avanzados de limitación con las directivas de [límite de velocidad por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [cuota por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , consulte [solicitud avanzada límite con la administración de la API de Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para crear un producto

En este paso, creará un producto de la versión de prueba gratuita que no requiere la aprobación de la suscripción.

>[AZURE.NOTE] Si ya tiene un producto configurado y desea utilizarlo para este tutorial, puede pasar a [Configurar llamar tasa límite y cuota directivas][] y siga el tutorial de uso del producto en lugar del producto de prueba gratuita de allí.

Para empezar, haga clic en **Administrar** en el clásico de Azure para el servicio de administración de la API. Esto abre el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia del servicio de administración de API][] en el tutorial [administrar su primera API en Azure API de administración][] .

En el menú de **Administración de la API** de la izquierda para mostrar la página de **productos** , haga clic en **productos** .

![Agregar producto][api-management-add-product]

Haga clic en **Agregar producto** para mostrar el cuadro de diálogo **Agregar nuevo producto** .

![Agregar nuevo producto][api-management-new-product-window]

En el cuadro **título** , escriba **Prueba gratuita**.

En el cuadro **Descripción** , escriba el texto siguiente:  **los suscriptores podrán ejecutar 10 llamadas por minuto hasta un máximo de 200 llamadas por semana después de que se ha denegado el acceso.**

Productos de administración de la API se pueden proteger o abrirán. Productos protegidos deben estar suscrito a antes de que se pueden utilizar. Productos abiertos pueden utilizarse sin una suscripción. Compruebe que está seleccionado el **requieren suscripción** para crear un producto protegido que requiere una suscripción. Esta es la configuración predeterminada.

Si desea que un administrador para revisar y Aceptar o rechazar los intentos de la suscripción a este producto, seleccione **requerir la aprobación de la suscripción**. Si la casilla de verificación está desactivada, los intentos de suscripción será aprueba automáticamente. En este ejemplo, las suscripciones se aprueban automáticamente, así que no seleccione el cuadro.

Para permitir que cuentas de desarrollador suscribir varias veces para el nuevo producto, seleccione la casilla de verificación **Permitir múltiples suscripciones simultáneas** . Este tutorial no utilizan varias suscripciones simultáneas, por lo que deja desactivada.

Cuando se escriben todos los valores, haga clic en **Guardar** para crear el producto.

![Producto añadido][api-management-product-added]

Por defecto, los nuevos productos son visibles para los usuarios del grupo **administradores** . Vamos a agregar el grupo de **programadores** . Haga clic en la **Versión de prueba gratuita**y, a continuación, haga clic en la ficha **visibilidad** .

>En la API de gestión, los grupos se utilizan para administrar la visibilidad de los productos para los desarrolladores. Productos conceden visibilidad a los grupos y los desarrolladores pueden ver y suscribirse a los productos que son visibles para los grupos en los que pertenecen. Para obtener más información, vea [cómo crear y utilizar grupos de administración de la API de Azure][].

![Agregar grupo de programadores][api-management-add-developers-group]

Active la casilla de verificación de **los desarrolladores** y, a continuación, haga clic en **Guardar**.

## <a name="add-api"> </a>Para agregar una API para el producto

En este paso del tutorial, agregaremos la API eco al nuevo producto para la versión de prueba gratuita.

>Cada instancia del servicio de administración de API viene preconfigurado con una API de eco que se puede utilizar para experimentar con y aprender sobre la API de administración. Para obtener más información, consulte [administrar su primera API en la gestión de la API de Azure][].

Haga clic en **productos** en el menú de **Gestión de la API** de la izquierda y, a continuación, haga clic en la **Versión de prueba gratuita** para configurar el producto.

![Configurar el producto][api-management-configure-product]

Haga clic en **Agregar API al producto**.

![Agregar API a producto][api-management-add-api]

Seleccione **Eco API**y, a continuación, haga clic en **Guardar**.

![Agregar eco API][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar directivas de límite y cuota de tasa de llamada

Cuotas y límites de velocidad se configuran en el editor de directivas. **API de administración** del menú de la izquierda, haga clic en **directivas** . En la lista de **productos** , haga clic en **Versión de prueba gratuita**.

![Política de productos][api-management-product-policy]

Haga clic en **Agregar directiva** para importar la plantilla de directiva y empezar a crear directivas de límite y la cuota de la tasa.

![Agregar directiva][api-management-add-policy]

Para insertar las directivas, coloque el cursor en la sección de la **entrada** o **salida** de la plantilla de directiva. Las directivas de límite y cuota de tasa son las entrantes, así que coloque el cursor en el elemento de entrada.

![Editor de directivas][api-management-policy-editor-inbound]

Las dos directivas que agregamos en este tutorial son las directivas de la [tarifa de llamada de límite por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [establecer la cuota de uso por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Instrucciones de directiva][api-management-limit-policies]

Después de que el cursor se coloca en el elemento de directiva **entrante** , haga clic en la flecha al lado de la **tarifa de llamada de límite por suscripción** para insertar su plantilla de directiva.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Tarifa de llamada de límite por suscripción** puede utilizarse en el nivel de producto y también puede utilizarse en los niveles de nombre de operación individual y API. En este tutorial, políticas a nivel de producto sólo se utilizan: elimine los elementos de la **api** y la **operación** del elemento del **límite de velocidad** , tan sólo los restos de elemento exterior **límite de velocidad** , como se muestra en el ejemplo siguiente.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

En el producto de la versión de prueba gratuita, la tasa máxima permitida de llamadas 10 llamadas por minuto, así que escriba **10** como el valor para el atributo de **llamadas** y **60** para el atributo de **período de renovación** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar la directiva para **establecer la cuota de uso por suscripción** , coloque el cursor justo debajo del elemento recién agregado **límite de velocidad** dentro del elemento de **entrada** y, a continuación, haga clic en la flecha a la izquierda del **conjunto de la cuota de uso por suscripción**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Dado que esta directiva se pretende también estar en el nivel de producto, elimine los elementos de nombre **api** y el **funcionamiento** , como se muestra en el ejemplo siguiente.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Las cuotas pueden basarse en el número de llamadas por intervalo, ancho de banda o ambos. En este tutorial, no nos estamos límite de ancho de banda en función de: elimine el atributo de **ancho de banda** .

    <quota calls="number" renewal-period="seconds">
    </quota>

En el producto de la versión de prueba gratuita, la cuota es 200 llamadas por semana. Especificar **200** como el valor para el atributo de **llamadas** y especifique **604800** como valor para el atributo de **período de renovación** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalos de directiva se especifican en segundos. Para calcular el intervalo de una semana, puede multiplicar el número de días (7) por el número de horas en un día (24) por el número de minutos en una hora (60) por el número de segundos en un minuto (60): 7 *24* 60 * 60 = 604800.

Cuando haya terminado de configurar la directiva, debe coincidir con el siguiente ejemplo.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Una vez configuradas las directivas, haga clic en **Guardar**.

![Guardar la directiva][api-management-policy-save]

## <a name="publish-product"></a> Para publicar el producto

Ahora que el se agregan las API y las directivas se configuran, el producto debe publicarse para que se pueda utilizar por los desarrolladores. Haga clic en **productos** en el menú de **Gestión de la API** de la izquierda y, a continuación, haga clic en la **Versión de prueba gratuita** para configurar el producto.

![Configurar el producto][api-management-configure-product]

Haga clic en **Publicar**y, a continuación, haga clic en **Sí, publicarla** para confirmar.

![Publicar el producto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para suscribirse a una cuenta de desarrollador para el producto

Ahora que se publica el producto, está disponible suscrito y utilizado por los desarrolladores.

>Los administradores de una instancia de la administración de la API se suscribe automáticamente a cada producto. En este paso del tutorial, se suscribe una de las cuentas de desarrollador sin privilegios de administrador para el producto de la versión de prueba gratuita. Si su cuenta de desarrollador es parte de la función Administrators, a continuación, puede seguir junto con este paso, aunque ya se ha suscrito.

Haga clic en **usuarios** , en el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en el nombre de su cuenta de desarrollador. En este ejemplo, estamos utilizando la cuenta de desarrollador **Clayton Gragg** .

![Configurar developer][api-management-configure-developer]

Haga clic en **Agregar suscripción**.

![Agregar suscripción][api-management-add-subscription-menu]

Seleccione la **Versión de prueba gratuita**y, a continuación, haga clic en **suscribirse**.

![Agregar suscripción][api-management-add-subscription]

>[AZURE.NOTE] En este tutorial, no están habilitadas varias suscripciones simultáneas para el producto de la versión de prueba gratuita. Si fueran, ¿se le a nombre de la suscripción, como se muestra en el ejemplo siguiente.

![Agregar suscripción][api-management-add-subscription-multiple]

Después de hacer clic en **suscribirse**, el producto aparecerá en la lista de **suscripción** para el usuario.

![Suscripción agregada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Para llamar a una operación y probar el límite de velocidad

Ahora que el producto de la versión de prueba gratuita está configurado y publicado, podemos llamar a algunas operaciones y probar la directiva de límite de velocidad.
Cambie en el portal del programador **Developer portal** en el menú superior derecha;

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y, a continuación, haga clic en **API de eco**.

![Portal para desarrolladores][api-management-developer-portal-api-menu]

Haga clic en **Obtener el recurso**y, a continuación, haga clic en **Pruébelo**.

![Abra la consola de][api-management-open-console]

Mantener el valor predeterminado de valores de parámetro y, a continuación, seleccione la clave de suscripción para el producto de la versión de prueba gratuita.

![Clave de suscripción][api-management-select-key]

>[AZURE.NOTE] Si tiene varias suscripciones, asegúrese de seleccionar la clave de **Versión de prueba gratuita**, o bien las directivas que se han configurado en los pasos anteriores no estará en vigor.

Haga clic en **Enviar**y, a continuación, ver la respuesta. Tenga en cuenta el **estado de la respuesta** de **200 OK**.

![Resultados de la operación][api-management-http-get-results]

Haga clic en **Enviar** a una velocidad mayor que la directiva de límite de velocidad de 10 llamadas por minuto. Después de la directiva de límite de velocidad se supera, se devuelve un estado de respuesta de **429 demasiado muchas solicitudes** .

![Resultados de la operación][api-management-http-get-429]

El **contenido de la respuesta** indica el intervalo restante antes de reintentos se realizará correctamente.

Cuando la directiva de límite de velocidad de 10 llamadas por minuto está en vigor, las llamadas posteriores se producirá un error hasta que hayan transcurrido 60 segundos de la primera de las 10 llamadas correcta del producto antes de que se ha superado el límite de velocidad. En este ejemplo, el intervalo restante es 54 segundos.

## <a name="next-steps"> </a>Próximos pasos

-   Vea una demostración de la configuración de cuotas y límites de velocidad en el siguiente vídeo.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Administrar la primera API en la gestión de la API de Azure]: api-management-get-started.md
[Cómo crear y utilizar grupos de administración de la API de Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Cree una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurar directivas de límite y cuota de tarifa de llamada]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
