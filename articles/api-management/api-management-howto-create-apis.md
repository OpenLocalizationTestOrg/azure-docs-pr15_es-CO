<properties 
    pageTitle="Cómo crear API de administración de la API de Azure" 
    description="Aprenda a crear y configurar la API en la gestión de la API de Azure." 
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
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-apis-in-azure-api-management"></a>Cómo crear API de administración de la API de Azure

Una API en la API de administración representa un conjunto de operaciones que se pueden invocar mediante aplicaciones cliente. Se crean nuevas API en el portal de publisher y, a continuación, se agregan las operaciones deseadas. Una vez que se agregan las operaciones, la API se agrega a un producto y puede ser publicada. Una vez que se publica una API, puede suscrito y utilizado por los desarrolladores.

Esta guía muestra el primer paso en el proceso: cómo crear y configurar una nueva API de administración de API. Para obtener más información sobre las operaciones de agregar y publicar un producto, consulte [cómo agregar operaciones a una API][] y [cómo crear y publicar un producto][].

## <a name="create-new-api"> </a>Crear una nueva API

Las API se crean y se configuran en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico para el servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia del servicio de administración de API][] en el tutorial para [comenzar con la administración de la API de Azure][] .

Haga clic en **API** desde el menú de la **API de administración** de la izquierda y, a continuación, haga clic en **Agregar API**.

![Crear API][api-management-create-api]

Utilice la ventana **Agregar nueva API** para configurar la nueva API.

![Agregar nueva API][api-management-add-new-api]

Los siguientes campos se utilizan para configurar la nueva API.

-   **Nombre de la API de Web** proporciona un nombre único y descriptivo para la API. Se muestra en los portales de desarrolladores y publicadores.
-   El servicio HTTP que implementa la API hace referencia a **URL de servicio Web** . Administración de API reenvía las solicitudes a esta dirección.
-   **Sufijo de dirección URL de Web API** se anexa a la dirección URL base del servicio de administración de la API. La base de la dirección URL es común para todas las API alojadas en una instancia del servicio de administración de API. Administración de API distingue API por su sufijo y, por tanto, el sufijo debe ser único para cada API para un fabricante determinado.
-   **Esquema de dirección URL de Web API** determina qué protocolos pueden utilizarse para tener acceso a la API. HTTPs se especifica de forma predeterminada.
-   Para agregar opcionalmente esta nueva API para un producto, haga clic en la lista desplegable **productos (opcionales)** y elija un producto. Este paso puede repetirse varias veces para agregar la API a varios productos.

Una vez configurados los valores que desee, haga clic en **Guardar**. Una vez creada la nueva API, se muestra la página de resumen de la API en el portal de publisher.

![Resumen de API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Configuración configurar API

Puede utilizar la ficha **configuración** para comprobar y modificar la configuración de una API. **Nombre de API Web**, **dirección URL del servicio Web**y **sufijo de dirección URL de Web API** se establecen inicialmente cuando se crea la API y se puede modificar aquí. **Descripción** proporciona una descripción opcional y **esquema de dirección URL de Web API** determina qué protocolos pueden utilizarse para tener acceso a la API.

![Configuración de API][api-management-api-settings]

Para configurar la autenticación de puerta de enlace para el servicio back-end, implementación de la API, seleccione la ficha **seguridad** . **Con credenciales** desplegable puede utilizarse para configurar la autenticación **HTTP básica** o **los certificados de cliente** . Para utilizar autenticación básica de HTTP, sólo tiene que escribir las credenciales. Para obtener información acerca de cómo utilizar la autenticación de certificados de cliente, consulte [cómo proteger mediante la autenticación de certificados de cliente en Azure API de administración de servicios de fondo][].

La ficha **seguridad** también puede utilizarse para configurar la **autorización del usuario** mediante OAuth 2.0. Para obtener más información, vea [cómo autorizar a cuentas de desarrollador utilizando OAuth 2.0 en la gestión de la API de Azure][].

![Configuración de autenticación básica][api-management-api-settings-credentials]

Haga clic en **Guardar** para guardar los cambios realizados en la configuración de la API.

## <a name="next-steps"> </a>Próximos pasos

Una vez que se crea una API y la configuración, los pasos siguientes son agregar las operaciones a la API, agregar la API a un producto y publicarlo para que esté disponible para los desarrolladores. Para obtener más información, consulte los artículos siguientes.

-   [Cómo agregar operaciones a un API][]
-   [Cómo crear y publicar un producto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Cómo agregar operaciones a un API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md

[Comenzar con la administración de la API de Azure]: api-management-get-started.md
[Cree una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance
[Cómo proteger servicios back-end mediante el cliente de autenticación de certificados en la gestión de la API de Azure]: api-management-howto-mutual-certificates.md
[Cómo autorizar a cuentas de desarrollador utilizando OAuth 2.0 en la gestión de la API de Azure]: api-management-howto-oauth2.md