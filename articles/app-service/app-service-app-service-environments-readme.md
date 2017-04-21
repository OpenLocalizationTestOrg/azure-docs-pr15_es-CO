<properties 
    pageTitle="Entorno de servicio de la aplicación | Microsoft Azure" 
    description="¿Qué es un entorno de servicio de la aplicación de Azure? Introducción al entorno de servicio de la aplicación." 
    keywords="entorno de servicio de aplicación de Azure, red virtual, secure networking"
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>

# <a name="app-service-environment-documentation"></a>Documentación del entorno de servicio de la aplicación

Un entorno de servicio de la aplicación es una [prima] [ PremiumTier] opción de plan de servicio de la aplicación de Azure que proporciona un entorno completamente aislado y dedicado de forma segura ejecutando aplicaciones de servicio de la aplicación de Azure en alta escala, incluyendo [Aplicaciones Web]servicios[WebApps], [Aplicaciones móviles][MobileApps]y [Aplicaciones de la API][APIApps].  

Entornos de servicio de la aplicación son ideales para cargas de trabajo de aplicaciones que requieren:

- Muy alta escala
- Aislamiento y acceso seguro a redes

Los clientes pueden crear múltiples entornos de servicio de la aplicación dentro de una única región de Azure, así como en varias regiones de Azure.  Esto hace que los entornos de servicio de la aplicación ideal para escalar horizontalmente los niveles de aplicación sin estado para soportar altas cargas de trabajo RPS.

Entornos de servicio de la aplicación están aislados para ejecutar sólo las aplicaciones de un único cliente y siempre se implementan en una red virtual.  Los clientes tienen un control específico sobre ambos tráfico de red entrante y saliente de aplicación utilizando [grupos de seguridad de red][NetworkSecurityGroups].  Las aplicaciones también pueden establecer conexiones seguras a alta velocidad sobre redes virtuales a los recursos corporativos locales.

Aplicaciones necesitan con frecuencia tener acceso a los recursos corporativos, como bases de datos internas y servicios web.  Aplicaciones que se ejecutan en entornos de servicio de la aplicación pueden tener acceso a los recursos accesibles a través de [Sitio a sitio] [ SiteToSite] [ExpressRoute de Azure] y VPN[ ExpressRoute] las conexiones.

* [¿Qué es un entorno de servicio de la aplicación?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creación de un entorno de servicio de la aplicación](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creación de aplicaciones en un entorno de servicio de la aplicación](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Crear y utilizar un equilibrador de carga interno con entornos de servicio de la aplicación](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurar un entorno de servicio de la aplicación](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Escalar aplicaciones en un entorno de servicio de la aplicación](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Arquitectura y seguridad de la red](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Cómo de

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vídeos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
