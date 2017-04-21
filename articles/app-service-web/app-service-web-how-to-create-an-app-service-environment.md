<properties 
    pageTitle="Cómo crear un entorno de servicio de la aplicación" 
    description="Descripción de flujo de creación de entornos de servicio de la aplicación" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Cómo crear un entorno de servicio de la aplicación #

### <a name="overview"></a>Información general ###

Entornos de servicio de aplicación (ASE) son una opción de servicio Premium de Azure App servicio que ofrece una funcionalidad mejorada de la configuración que no está disponible en los sellos multiempresa.  Esencialmente, la característica ASE implementa el servicio de aplicación de Azure en red virtual del cliente.  Para obtener una mayor comprensión de las capacidades ofrecidas por entornos de servicio de la aplicación lea la [¿Qué es un entorno de servicio de la aplicación] [ WhatisASE] documentación.

### <a name="before-you-create-your-ase"></a>Antes de crear su ASE ###

Es importante ser consciente de las cosas que no puede cambiar.  Son aquellos aspectos que no se puede cambiar sobre su ASE después de crearlo:

- Ubicación
- Suscripción
- Grupo de recursos
- VNet utilizado
- Subred que se utiliza 
- Tamaño de la subred

Cuando un VNet de picking y especificar una subred, asegúrese de que es lo suficientemente grande como para dar cabida a cualquier futuro crecimiento.  

### <a name="creating-an-app-service-environment"></a>Creación de un entorno de servicio de la aplicación ###

Hay dos formas de acceso a la interfaz de usuario de creación de ASE.  Puede ser encontrados en búsquedas en el catálogo de soluciones de Azure para ***Entorno de servicio de la aplicación*** o a través de nuevo -> Web + Mobile -> entorno de servicio de la aplicación.  Para crear un ASE:

1. Proporcione el nombre de su ASE.  El nombre que se especifica para el ASE se utilizará para las aplicaciones creadas en la ASE.  Si el nombre de la ASE es appsvcenvdemo sería el nombre del subdominio. *appsvcenvdemo.p.azurewebsites.net*.  Si creó una aplicación denominada *mytestapp* , por tanto, sería direccionable en *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  No puede utilizar espacios en blanco en el nombre de su ASE.  Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión minúscula total de ese nombre.  Si utiliza una DCI a continuación su nombre ASE no se utiliza en el subdominio pero en su lugar se indica explícitamente durante la creación de ASE

    ![][1]

2. Active su suscripción.  La suscripción se utiliza para su ASE también es aquel con el que se creará con todas las aplicaciones en ese ASE.  No puede colocar su ASE en un VNet que se encuentra en otra suscripción

3. Seleccione o especifique un nuevo grupo de recursos.  El grupo de recursos que se utilizan para su ASE debe ser el mismo que se utiliza para su VNet.  Si selecciona un VNet preexistente la selección del grupo de recursos para su ASE se actualizará para reflejar el de la VNet.

    ![][2]

4. Realice sus selecciones de red Virtual y la ubicación.  Puede crear un nuevo VNet o seleccione una existente VNet.  Si selecciona un nuevo VNet puede especificar un nombre y una ubicación. El nuevo VNet tendrá la 192.268.250.0/23 intervalo dirección y una subred con nombre **predeterminado** que se define como 192.168.250.0/24.  Puede también seleccionar un clásico preexistente o VNet del Administrador de recursos.  La selección del tipo de VIP determina si su ASE puede obtenerse directamente de internet (externo) o si se utiliza un equilibrador de carga interno (DCI).  Para aprender más acerca de ellos leen [mediante un equilibrador de carga interno con un entorno de servicio de la aplicación][ILBASE].  Si selecciona un tipo de VIP de externo, a continuación, puede seleccionar cuántas direcciones IP externas que se crea el sistema con fines de IPSSL.  Si selecciona interno debe especificar el subdominio que utilizará su ASE.  ASEs se pueden implementar en redes virtuales que utilizan (es decir, *ambos* rangos de direcciones públicas, espacios de direcciones *o* RFC1918 direcciones privadas).  Para poder utilizar una red virtual con un intervalo de direcciones públicas, debe crear la VNet antes de tiempo.  Cuando se selecciona un VNet existente debe crear una nueva subred durante la creación de ASE.  **No puede utilizar una subred creada previamente en el portal.  Puede crear un ASE con una subred existente si crees que tu ASE utilizando una plantilla de administrador de recursos.**  Para crear un ASE desde un uso de la plantilla de la información, [la creación de un entorno de servicio de la aplicación de la plantilla de] [ ILBAseTemplate] y aquí, [crear un entorno de servicio de la aplicación ILB de plantilla][ASEfromTemplate].

### <a name="details"></a>Detalles ###

Se crea un ASE con 2 servidores Front end y 2 trabajadores.  Servidores Front end actúan como los extremos HTTP/HTTPS y enviar tráfico a los trabajadores que son las funciones que alojan sus aplicaciones.   Puede ajustar la cantidad después de la creación de ASE y puede incluso configurar reglas Autoescala en estas agrupaciones de recursos.  Para obtener más detalles alrededor de escala manual, administración y supervisión de un entorno de servicio de la aplicación van aquí: [cómo configurar un entorno de servicio de la aplicación][ASEConfig] 

Sólo la uno ASE puede existir en la subred utilizada por la ASE.  La subred no se puede utilizar para cualquier cosa diferente de la ASE

### <a name="after-app-service-environment-creation"></a>Después de la creación del entorno de servicio de la aplicación ###

Después de la creación de ASE se puede ajustar:

- Cantidad de servidores Front end (mínimo: 2)
- Cantidad de los trabajadores (mínimo: 2)
- Cantidad de direcciones IP disponibles para IP SSL
- Calcular tamaños de recursos utilizados por los trabajadores o de servidores Front end (tamaño mínimo de Front-End es P2)


Hay más detalles alrededor manual escala, administración y monitoreo de entornos de servicio de la aplicación aquí: [cómo configurar un entorno de servicio de la aplicación][ASEConfig] 

Para obtener información sobre el ajuste automático hay una guía aquí: [cómo configurar escala automática para un entorno de servicio de la aplicación][ASEAutoscale]

Existen dependencias adicionales que no están disponibles para la personalización, como la base de datos y almacenamiento de información.  Estos son controlados por Azure y se incluyen con el sistema.  Los sistemas de almacenamiento de información soporta hasta 500 GB para todo el entorno de servicio de la aplicación y la base de datos se ajusta con Azure según lo requiera la escala del sistema.


## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de servicio de la aplicación están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de la aplicación, vea [Introducción a los entornos de servicio de la aplicación][WhatisASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, consulte el [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
