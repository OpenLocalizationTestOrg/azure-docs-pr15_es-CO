<properties
    pageTitle="Crear y utilizar un equilibrador de carga interno con un entorno de servicio de la aplicación | Microsoft Azure"
    description="Crear y utilizar un ASE con un ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilizar un equilibrador de carga interno con un entorno de servicio de la aplicación #

La característica Environments(ASE) de servicio de la aplicación es una opción de servicio Premium de Azure App servicio que ofrece una funcionalidad mejorada de la configuración que no está disponible en los sellos multiempresa.  Esencialmente, la característica ASE implementa el servicio de aplicación de Azure en su Network(VNet) Virtual de Azure.  Para obtener una mayor comprensión de las capacidades ofrecidas por entornos de servicio de la aplicación lea la [¿Qué es un entorno de servicio de la aplicación] [ WhatisASE] documentación.  Si no conoce las ventajas del funcionamiento en un VNet lea las [P+F de red Virtual de Azure][virtualnetwork].  


## <a name="overview"></a>Información general ##


Un ASE puede implementarse con un extremo puede tener acceso a internet o con una dirección IP en el VNet.  Para establecer la dirección IP a una dirección VNet que necesita para implementar su ASE con un Balancer(ILB) de carga interno.  Cuando se configura su ASE con una DCI proporciona:

- su propio dominio o subdominio.  Para que sea fácil, este documento asume el subdominio pero puede configurarlo en ambos casos.  
- el certificado utilizado para HTTPS
- Administración de DNS para el subdominio.  


Como resultado, usted puede hacer cosas como:

- alojar aplicaciones de intranet, como línea de aplicaciones empresariales, segura en la nube que accede a través de un sitio a sitio o VPN ExpressRoute
- aplicaciones de host en la nube que no se muestran en servidores DNS públicos
- crear aplicaciones de back-end de internet aislado que sus aplicaciones front-end se pueden integrar con seguridad con


#### <a name="disabled-functionality"></a>Funcionalidad deshabilitada ####

Hay algunas cosas que no puede hacer cuando se utiliza un ASE de ILB.  Esas cosas son:

- uso IPSSL
- asignar direcciones IP a las aplicaciones específicas
- adquisición y uso de un certificado con una aplicación a través del portal.  Por supuesto todavía puede obtener certificados directamente con una entidad emisora de certificados y utilizarlo con las aplicaciones, pero no mediante el portal de Azure.


## <a name="creating-an-ilb-ase"></a>Crear un ASE ILB ##

No es muy diferente de crear un ASE normalmente creando un ASE DCI.  Para una explicación más profunda sobre la creación de un ASE lea [cómo crear un entorno de servicio de la aplicación][HowtoCreateASE].  El proceso para crear un ASE DCI es el mismo entre crear un VNet durante la creación de ASE o seleccionar una existente VNet.  Para crear un ASE de ILB: 

1.  En el portal de Azure, seleccione **Nuevo -> Web + Mobile -> entorno de servicio de la aplicación**
2.  Seleccione la suscripción
3.  Seleccione o cree un grupo de recursos
4.  Seleccione o cree un VNet
5.  Crear una subred, si selecciona un VNet
6.  Seleccione **Virtual o ubicación de red-> configuración de VNet** y establezca el tipo de dirección VIP a interna
7.  Proporcionar nombre de subdominio (Esto será el subdominio que se utiliza para aplicaciones creadas en este ASE)
8.  Haga clic en Aceptar y, a continuación, crear


![][1]


En el módulo de red Virtual hay una opción de configuración VNet.  Este permite seleccionar entre una VIP externo o interno VIP.  El valor predeterminado es externo.  Si se establece como externa tiene su ASE utilizará a una VIP puede tener acceso a internet.  Si selecciona interna, se configurará la ASE con una DCI en una dirección IP dentro de su VNet.  


Después de seleccionar interno, se quita la capacidad para agregar más direcciones IP a su ASE y en su lugar debe proporcionar el subdominio de la ASE.  En un ASE con una VIP externo se utiliza el nombre de la ASE en el subdominio para aplicaciones creadas en ese ASE.  
Si tu ASE se llamaba ***contosotest*** y su aplicación en que se llamó ASE ***mytest*** entonces el subdominio sería el formato ***contosotest.p.azurewebsites.net*** y la dirección URL de esa aplicación sería ***mytest.contosotest.p.azurewebsites.net***.  
Si establece el tipo de dirección VIP como interna, su nombre ASE no se utiliza en el subdominio de la ASE.  Especificar explícitamente el subdominio.  Si tu subdominio era ***contoso.corp.net*** y ha creado una aplicación en eso ASE había denominado ***timereporting*** la dirección URL de esa aplicación sería ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Aplicaciones en un ASE ILB ##

Creación de una aplicación en un ASE DCI es lo mismo que crear una aplicación en un ASE normalmente.  

1. En el portal de Azure, seleccione **Nuevo -> Web + Mobile -> Web** o **móvil** o **API de la aplicación**
2. Escriba el nombre de la aplicación
2. Seleccionar suscripción
3. Seleccione o cree el grupo de recursos
4. Seleccione o cree la aplicación servicio de Plan(ASP).  Si crear un nuevo archivo ASP, a continuación, seleccione su ASE como la ubicación y desea que la página ASP que se creen en el grupo de trabajo.  Cuando se crea el ASP selecciona su ASE como la ubicación y el grupo de trabajo.  Cuando se especifica el nombre de la aplicación, verá que el subdominio bajo el nombre de la aplicación se sustituye por el subdominio para su ASE.   
5. Seleccione crear.  Debe seleccionar la casilla de verificación **fijar a Mi panel** si desea que se muestre en el escritorio de la aplicación.  

![][2]


Bajo el nombre de la aplicación el nombre del subdominio se actualiza para reflejar el subdominio de su ASE.  


## <a name="post-ilb-ase-creation-validation"></a>Creación de DCI ASE postvalidación ##

Un ASE DCI es ligeramente diferente que el ASE ILB.  Como se ha señalado que necesita para administrar sus propios DNS y también tiene que proporcionar su propio certificado para las conexiones HTTPS.  


Después de crear su ASE observará que el subdominio muestra el subdominio especificado y hay un nuevo elemento en el menú de **configuración** que se denomina **Certificado de DCI**.  El ASE se crea con un certificado firmado automáticamente, lo que facilita la prueba HTTPS.  El portal le indicará que debe proporcionar su propio certificado para HTTPS, pero esto es conseguir que tenga un certificado que va con tu subdominio.  

![][3]


Si son simplemente probando cosas y no sabe cómo crear un certificado, puede utilizar la aplicación de consola de MMC de IIS para crear un certificado autofirmado.  Una vez que se crea puede exportarla como un archivo .pfx y, a continuación, cargarlo en la interfaz de usuario de certificado ILB. Cuando tiene acceso a un sitio Web asegurado con un certificado autofirmado, su explorador le dará una advertencia de que el sitio que tiene acceso no es seguro debido a la imposibilidad de validar el certificado.  Si desea evitar que dicha advertencia necesita un certificado debidamente firmado que coincida con tu subdominio y tiene una cadena de confianza que es reconocido por el explorador.

![][6]

Si desea probar el flujo con sus propios certificados y probar el acceso HTTP y HTTPS a su ASE:

1.  Vaya a ASE de interfaz de usuario una vez creado ASE **ASE -> Configuración -> certificados de ILB**
2.  Establecer certificado de DCI seleccionando archivo pfx de certificado y proporcionar la contraseña.  Este paso toma un poco de tiempo para procesar y aparecerá el mensaje que está en curso una operación de escala.
3.  Obtener la dirección ILB su ASE (**ASE -> Propiedades -> dirección IP Virtual**)
4.  Crear una aplicación web en ASE después de su creación 
5.  Crear una máquina virtual si no tienes una en ese VNET (no en la misma subred que el salto ASE o cosas)
6.  Configurar DNS para el subdominio.  Puede utilizar un comodín con el subdominio en el DNS o si desea hacer algunas pruebas simples, edite el archivo de hosts en la máquina virtual para establecer el nombre de la aplicación web para la dirección IP de la dirección VIP.  Si tu ASE tenía el nombre del subdominio. ilbase.com y se hace la mytestapp de la aplicación web de modo que podría ser dirigido a mytestapp.ilbase.com entonces que establecer en el archivo de hosts.  (Sobre Windows el archivo hosts se encuentra en C:\Windows\System32\drivers\etc\)
7.  Utilizar un explorador en esa máquina virtual e ir a http://mytestapp.ilbase.com (o lo que es el nombre de la aplicación web con tu subdominio)
8.  Utilizar un explorador en esa máquina virtual e ir a https://mytestapp.ilbase.com que tendrá que aceptar la falta de seguridad si utiliza un certificado autofirmado.  


La dirección IP para el ILB aparece en las propiedades como la dirección IP Virtual

![][4]


## <a name="using-an-ilb-ase"></a>Utilizando un ASE ILB ##

#### <a name="network-security-groups"></a>Grupos de seguridad de red ####

ASE de una DCI permite el aislamiento de red para sus aplicaciones las aplicaciones no sean accesibles o incluso conocidos por internet.  Esta acción es perfecta para hospedar sitios de intranet como línea de aplicaciones empresariales.  Cuando necesite restringir el acceso incluso más aún sirve Groups(NSGs) de seguridad de red para controlar el acceso a nivel de red. 


Si desea utilizar NSGs para restringir más el acceso necesario para asegurarse de que no se interrumpe la comunicación que el ASE necesita para poder funcionar.  Aunque el acceso HTTP/HTTPS es sólo a través de la DCI usado por el ASE la ASE todavía depende de recursos fuera de la VNet.  Para ver qué acceso a la red sigue siendo necesario buscar la información en el documento de [Controlar el tráfico entrante a un entorno de servicio de la aplicación] [ ControlInbound] y el documento en [Los detalles de configuración de red para entornos de servicio de la aplicación con ExpressRoute][ExpressRoute].  


Para configurar los NSGs debe conocer la dirección IP que Azure sirve para administrar su ASE.  Esa dirección IP también es la dirección IP saliente desde su ASE si resulta que las solicitudes de internet.  Para buscar esta dirección IP dirección vaya a **Opciones -> Propiedades** y buscar la **Dirección IP saliente**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Administración general DCI ASE ####

Administrar un ASE DCI es prácticamente igual que administrar un ASE normalmente.  Es necesario escalar sus grupos de trabajo para alojar varias instancias ASP y escalar los servidores Front-End para manejar mayores cantidades de tráfico HTTP/HTTPS.  Para obtener información general acerca de cómo administrar la configuración de un ASE, lea el documento sobre [cómo configurar un entorno de servicio de la aplicación][ASEConfig].  


Los elementos adicionales de administración son la administración de certificados y la administración de DNS.  Debe obtener y cargar el certificado utilizado para HTTPS después de la creación de DCI ASE y sustituirlo antes de que caduque.  Ya que Azure posee el dominio base podemos proporcionar certificados para ASEs con una VIP externo.  Dado que el subdominio utilizado por un ASE DCI puede ser cualquier cosa, debe proporcionar su propio certificado para HTTPS. 


#### <a name="dns-configuration"></a>Configuración de DNS ####

Al utilizar a una VIP externo el DNS está administrado por Azure.  Cualquier aplicación creada en su ASE se agrega automáticamente al DNS de Azure, que es un DNS público.  En una ASE DCI tendrá que administrar sus propios DNS.  Para un determinado subdominio como contoso.corp.net debe crear DNS A registros que apuntan a la dirección de DCI para:

    * 
    publicar *.SCM ftp 


## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de servicio de la aplicación están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de la aplicación, vea [Introducción a los entornos de servicio de la aplicación][WhatisASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, consulte el [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
