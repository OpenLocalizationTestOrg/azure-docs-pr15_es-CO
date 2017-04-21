<properties
    pageTitle="Administrar la primera API en Azure API administración | Microsoft Azure"
    description="Aprenda a crear APIs, agregar operaciones y empezar a trabajar con la API de administración."
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Administrar la primera API en la gestión de la API de Azure

## <a name="overview"> </a>Visión general

Esta guía muestra cómo comenzar a trabajar en Azure utiliza API de administración rápidamente y realizar su primera llamada a la API.

## <a name="concepts"> </a>¿Qué es la administración de la API de Azure?

Puede utilizar administración de API de Azure para tomar cualquier back-end y lanzar un programa completo de API basado en ella.

Los escenarios comunes incluyen:

* **Infraestructura móvil de seguridad** mediante el control de acceso con claves API, previniendo DOS ataques mediante la regulación o mediante políticas de seguridad avanzadas como la validación de símbolo (token) de JWT.
* **Ecosistemas de asociado de negocios ISV de habilitación** al ofrecer la incorporación rápida del socio a través del portal del desarrollador y una API de fachada del edificio desacoplar de implementaciones internas que no son maduras para consumo asociado.
* **Ejecutando un programa de API interno** , ofreciendo una ubicación centralizada para la organización para comunicarse acerca de la disponibilidad y los últimos cambios de API, el control de acceso basado en las cuentas de la organización, todo basado en un canal seguro entre la puerta de enlace API y el back-end.


El sistema se compone de los siguientes componentes:

* La **puerta de enlace API** es el extremo que:
  * Acepta llamadas de API y las encamine a su back-ends.
  * Comprueba las claves API, tokens JWT, certificados y otras credenciales.
  * Exige el uso de cuotas y límites de velocidad.
  * Transforma la API sobre la marcha sin modificaciones de código.
  * Almacena en caché las respuestas de back-end donde configurar.
  * Registros de convocatoria de metadatos con fines de análisis.

* El **portal de publisher** es la interfaz administrativa donde se configura el programa API. Se utiliza para:
    * Definir o importar esquema de API.
    * Paquete API en productos.
    * Configurar las directivas como las cuotas o las transformaciones en las API.
    * Obtenga conocimientos de análisis.
    * Administrar usuarios.

* El **portal para desarrolladores** sirve como la presencia de web principal para los desarrolladores, donde podrá:
    * Documentación de la API de lectura.
    * Pruebe una API a través de la consola interactiva.
    * Crear una cuenta y suscribirse para obtener claves API.
    * Análisis de acceso de su propio uso.


## <a name="create-service-instance"> </a>Crea una instancia de la administración de la API

>[AZURE.NOTE] Para completar este tutorial, necesitará una cuenta de Azure. Si no tienes una cuenta, puede crear una cuenta gratuita en sólo un par de minutos. Para obtener más información, vea [Versión de prueba gratuita de Azure][].

Es el primer paso para trabajar con la API de administración crear una instancia de servicio. Iniciar sesión en el [Portal de Azure clásico][] y haga clic en **nuevo**, **Servicios de aplicación**, **Administración de API**, **crear**.

![Nueva instancia de la administración de la API][api-management-create-instance-menu]

Para la **dirección URL**, especifique un único nombre de subdominio que se utilizará para la dirección URL del servicio.

Elija la **suscripción** y la **región** deseada para la instancia de servicio. Después de realizar las selecciones, haga clic en el botón **siguiente** .

![Nuevo servicio de administración de la API][api-management-create-instance-step1]

Escriba **Contoso Ltd.** para el **Nombre de la organización**e introduzca su dirección de correo electrónico en el campo de **Correo electrónico del administrador** .

>[AZURE.NOTE] Esta dirección de correo electrónico se utiliza para las notificaciones del sistema de administración de la API. Para obtener más información, consulte [cómo configurar las notificaciones y plantillas de correo electrónico en la administración de la API de Azure][].

![Nuevo servicio de administración de la API][api-management-create-instance-step2]

Instancias de servicio de administración de la API están disponibles en tres niveles: Developer, Standard y Premium. De forma predeterminada, se crean nuevas instancias del servicio de administración de API en el nivel de desarrollador. Para seleccionar el nivel estándar o Premium, la casilla de verificación **Opciones avanzadas** y seleccione el nivel deseado en la pantalla siguiente.

>[AZURE.NOTE] Es el nivel de desarrollador para desarrollo, pruebas y programas piloto de API donde la alta disponibilidad no es una preocupación. En los niveles estándar y Premium, puede escalar el número de unidades reservadas para controlar más tráfico. Los niveles estándar y Premium de proporcionan el servicio de administración de la API con la mayor potencia de procesamiento y el rendimiento. Puede completar este tutorial utilizando cualquier nivel. Para obtener más información acerca de los niveles de administración de la API, vea [API de administración de precios][].

Haga clic en la casilla de verificación para crear una instancia de servicio.

![Nuevo servicio de administración de la API][api-management-instance-created]

Una vez creada la instancia de servicio, el siguiente paso es crear o importar una API.

## <a name="create-api"> </a>Importar una API

Una API consiste en un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones de la API son procesadas por el proxy para servicios web existentes.

Las API se pueden crear (y se pueden agregar operaciones) manualmente, o se pueden importar. En este tutorial, se importará la API para servicios web ejemplo Calculadora proporcionados por Microsoft y alojada en Azure.

>[AZURE.NOTE] Para obtener instrucciones sobre cómo crear una API y agregar manualmente las operaciones, consulte [cómo crear API](api-management-howto-create-apis.md) y [cómo agregar operaciones a una API](api-management-howto-add-operations.md).

Las API se configuran desde el portal de publisher, que se tiene acceso a través del Portal clásico de Azure. Para llegar al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico para el servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

Para importar la API de la Calculadora, haga clic en **API** desde el menú de la **API de administración** de la izquierda y, a continuación, haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Realice los pasos siguientes para configurar la API de la Calculadora:

1. Haga clic en **Desde dirección URL**, escriba **http://calcapi.cloudapp.net/calcapi.json** en el cuadro de texto **dirección URL del documento de especificación** y haga clic en el botón de opción **Swagger** .
2. En el cuadro de texto **sufijo de dirección URL de API de Web** , escriba **calc** .
3. Haga clic en el cuadro de **productos (opcionales)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Agregar nueva API][api-management-import-new-api]

>[AZURE.NOTE] **API de administración** actualmente admite la versión 1.2 y 2.0 de Pavoneo documento para la importación. Asegúrese de que, aunque la [especificación 2.0 Swagger](http://swagger.io/specification) declara que `host`, `basePath`, y `schemes` propiedades son opcionales, el documento de Swagger 2.0 **debe** contener estas propiedades; de lo contrario no se importarán. 

Una vez importada la API, se muestra la página de resumen de la API en el portal de publisher.

![Resumen de API][api-management-imported-api-summary]

La sección API tiene varias fichas. La ficha **Resumen** muestra métricas básicas e información acerca de la API. La ficha [configuración](api-management-howto-create-apis.md#configure-api-settings) se utiliza para ver y editar la configuración de una API. La ficha [operaciones](api-management-howto-add-operations.md) se utiliza para administrar las operaciones de la API. La ficha de **seguridad** puede utilizarse para configurar la autenticación de puerta de enlace para el servidor back-end utilizando autenticación básica o [autenticación de certificado mutuo](api-management-howto-mutual-certificates.md)y configurar la [autorización del usuario mediante OAuth 2.0](api-management-howto-oauth2.md).  La ficha **Issues** se utiliza para ver los problemas notificados por los desarrolladores que utilizan las API. La ficha de **productos** se utiliza para configurar los productos que contengan esta API.

De forma predeterminada, cada instancia de API administración viene con dos productos de ejemplo:

-   **Motor de arranque**
-   **Ilimitado**

En este tutorial, la API de calculadora básica se agregó al producto Starter cuando se importó la API.

Con el fin de realizar llamadas a una API, los desarrolladores deben suscribirse a un producto que da acceso a él. Los desarrolladores pueden suscribirse a los productos en el portal para desarrolladores o administradores pueden suscribirse a los desarrolladores de los productos en el portal de publisher. Es administrador desde que se creó la instancia de administración de la API en los pasos anteriores en el tutorial, por lo que se ha suscrito a todos los productos de forma predeterminada.

## <a name="call-operation"> </a>Llamar a una operación desde el portal del programador

Las operaciones pueden llamarse directamente desde el portal para desarrolladores, que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial, llamará a operación de **Agregar dos números enteros** de la API de calculadora básica. Haga clic en **Developer portal** desde el menú en la parte superior derecha del portal de publisher.

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** del menú superior y, a continuación, haga clic en **Calculadora básica** para ver las operaciones disponibles.

![Portal para desarrolladores][api-management-developer-portal-calc-api]

Tenga en cuenta las descripciones de ejemplo y los parámetros que se han importado junto con las API y las operaciones, proporcionar documentación para los desarrolladores que va a utilizar esta operación. También se pueden agregar estas descripciones cuando las operaciones se agregan manualmente.

Para llamar a la operación de **Agregar dos números enteros** , haga clic en **Pruébelo**.

![Pruébelo][api-management-developer-portal-calc-api-console]

Puede introducir algunos valores para los parámetros o conserve los valores predeterminados y, a continuación, haga clic en **Enviar**.

![HTTP Get][api-management-invoke-get]

Después de invoca una operación, el portal del programador muestra el **estado de la respuesta**, los **encabezados de respuesta**y cualquier **contenido de la respuesta**.

![Respuesta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Ver analytics

Para ver el análisis de calculadora básica, cambiar volver al portal de publisher seleccionando **Administrar** desde el menú en la parte superior derecha del portal del programador.

![Administrar][api-management-manage-menu]

La vista predeterminada para el portal de publisher es el **panel**, que proporciona una visión general de la instancia de administración de la API.

![Tablero de mandos][api-management-dashboard]

Sitúe el ratón sobre el gráfico para la **Calculadora básica** ver las métricas específicas para el uso de la API para un período de tiempo determinado.

>[AZURE.NOTE] Si no ve todas las líneas en el gráfico, volver al portal para desarrolladores realizar algunas llamadas a la API de, espere unos momentos y vuelva al escritorio.

Haga clic en **Ver detalles** para ver la página Resumen de la API, incluyendo una versión más grande de las métricas de muestra.

![Analytics][api-management-mouse-over]

![Resumen][api-management-api-summary-metrics]

Para métricas detalladas e informes, haga clic en **análisis** desde el menú de la **API de administración** de la izquierda.

![Información general][api-management-analytics-overview]

La sección de **análisis** consta de las siguientes cuatro fichas:

-   **Un vistazo** proporciona indicadores generales de mantenimiento y uso, así como los principales desarrolladores, mejores productos, API superiores y las operaciones principales.
-   **Uso** ofrece una mirada exhaustiva de ancho de banda, incluyendo una representación geográfica y llamadas a la API.
-   Enfoques de la **salud** en los códigos de estado, las tasas de éxito, tiempos de respuesta y API de caché y tiempos de respuesta de servicio.
-   **Actividad** proporciona informes que profundicen en la actividad específica por el desarrollador, producto, API y operación.

## <a name="next-steps"> </a>Próximos pasos

- Aprenda cómo [proteger su API con límites de velocidad](api-management-howto-product-with-rules.md).

[Versión de prueba gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Cómo configurar notificaciones y plantillas de correo electrónico en la administración de la API de Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API Administración de precios]: http://azure.microsoft.com/pricing/details/api-management/

[Portal clásico de Azure]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
