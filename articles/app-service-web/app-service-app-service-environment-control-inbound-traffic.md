<properties 
    pageTitle="Cómo controlar el tráfico entrante en un entorno de servicio de la aplicación" 
    description="Obtenga información sobre cómo configurar reglas de seguridad de red para controlar el tráfico entrante a un entorno de servicio de la aplicación." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Cómo controlar el tráfico entrante en un entorno de servicio de la aplicación

## <a name="overview"></a>Información general ##
Un entorno de servicio de la aplicación pueden crearse en **cualquier** una red virtual del Administrador de recursos de Azure, **o** una implementación del clásica modelo de [red virtual][virtualnetwork].  En el momento en que se crea un entorno de servicio de la aplicación, pueden definirse una nueva red virtual y la nueva subred.  Como alternativa, puede crearse un entorno de servicio de la aplicación en una red virtual existente y subred preexistente.  Con un cambio reciente realizado en junio de 2016, ASEs ahora pueden implementarse en redes virtuales que utilizan intervalos de direcciones públicas o espacios de direcciones RFC1918 (es decir direcciones privadas).  Para obtener más detalles sobre la creación de un entorno de servicio de la aplicación, vea [cómo crear un entorno de servicio de la aplicación][HowToCreateAnAppServiceEnvironment].

Un entorno de servicio de la aplicación siempre deben crearse dentro de una subred debido a una subred proporciona un límite de red que se puede utilizar para bloquear el tráfico entrante detrás de dispositivos ascendentes y servicios tal que sólo se acepta el tráfico HTTP y HTTPS desde determinadas direcciones IP precede en la cadena.

Tráfico de red entrante y saliente de una subred se controla mediante un [grupo de seguridad de red][NetworkSecurityGroups]. Control del tráfico entrante requiere la creación de reglas de seguridad de red en un grupo de seguridad de red y, a continuación, asignar la seguridad de red de la subred que contiene el entorno de servicio de la aplicación grupo.

Una vez que un grupo de seguridad de red se asigna a una subred, el tráfico entrante a las aplicaciones en el entorno de servicio de la aplicación está permitido o bloqueado según la permitir y denegar las reglas definidas en el grupo de seguridad de la red.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Puertos de red utilizados en un entorno de servicio de la aplicación ##
Antes de bloquear el tráfico de red entrante con un grupo de seguridad de red, es importante conocer el conjunto de puertos de red necesarios y opcionales utilizados por un entorno de servicio de la aplicación.  Cierre accidentalmente apagado tráfico a algunos puertos puede producir pérdida de funcionalidad en un entorno de servicio de la aplicación.

La siguiente es una lista de puertos utilizados por un entorno de servicio de la aplicación. Todos los puertos son **TCP**, a menos que se indique claramente:

- 454: **requiere el puerto** utilizado por la infraestructura de Azure para administrar y mantener entornos de servicio de la aplicación a través de SSL.  No bloquee el tráfico en este puerto.  Este puerto siempre está enlazado a la dirección VIP pública de un ASE.
- 455: **requiere el puerto** utilizado por la infraestructura de Azure para administrar y mantener entornos de servicio de la aplicación a través de SSL.  No bloquee el tráfico en este puerto.  Este puerto siempre está enlazado a la dirección VIP pública de un ASE.
- 80: puerto para el tráfico HTTP entrante a las aplicaciones que se ejecutan en planes de servicio de la aplicación en un entorno de servicio de la aplicación predeterminado.  En un ASE habilitado DCI, este puerto está enlazado a la dirección ILB de la ASE.
- 443: puerto para el tráfico SSL entrante para aplicaciones que se ejecutan en planes de servicio de la aplicación en un entorno de servicio de la aplicación predeterminado.  En un ASE habilitado DCI, este puerto está enlazado a la dirección ILB de la ASE.
- 21: canal de control FTP.  Este puerto se puede bloquear de forma segura si no se utiliza FTP.  En una ASE DCI habilitado, se puede enlazar este puerto a la dirección ILB para un ASE.
- 990: canal de control para FTPS.  Este puerto se puede bloquear de forma segura si no se utiliza FTPS.  En una ASE DCI habilitado, se puede enlazar este puerto a la dirección ILB para un ASE.
- 10001 10020: canales de datos FTP.  Al igual que con el canal de control, estos puertos pueden con seguridad bloquearse si no se utiliza FTP.  En un ASE DCI habilitado, se puede enlazar este puerto a la dirección ILB del ASE.
- 4016: utilizado para la depuración remota con Visual Studio de 2012.  Este puerto se puede bloquear de forma segura si no se utiliza la función.  En un ASE habilitado DCI, este puerto está enlazado a la dirección ILB de la ASE.
- 4018: se utiliza para la depuración remota con Visual Studio 2013.  Este puerto se puede bloquear de forma segura si no se utiliza la función.  En un ASE habilitado DCI, este puerto está enlazado a la dirección ILB de la ASE.
- 4020: utilizado para la depuración remota con Visual Studio 2015.  Este puerto se puede bloquear de forma segura si no se utiliza la función.  En un ASE habilitado DCI, este puerto está enlazado a la dirección ILB de la ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividad saliente y los requisitos de DNS ##
En un entorno de servicio de la aplicación funcionar correctamente, requiere el acceso de salida a varios extremos. Es una lista completa de los extremos externos de un ASE en la sección "Requiere conectividad de red" del artículo de [Configuración de red para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Entornos de servicio de la aplicación requieren una infraestructura DNS válida configurada para la red virtual.  Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno de servicio de la aplicación, los desarrolladores pueden forzar un entorno de servicio de la aplicación para recoger la nueva configuración de DNS.  Desencadenar un reinicio de entorno sucesiva mediante el icono "Reiniciar" situado en la parte superior de la hoja de administración de entorno de la aplicación del servicio en el [portal de Azure] [ NewPortal] hará que el entorno recoger la nueva configuración de DNS.

También se recomienda que los servidores DNS personalizados en el vnet ser instalación antes de tiempo antes de crear un entorno de servicio de la aplicación.  Si se cambia la configuración de DNS de una red virtual mientras se crea un entorno de servicio de la aplicación, que dará como resultado al error del proceso de creación de entorno de servicio de la aplicación.  De forma similar, si existe un servidor DNS personalizado en el otro extremo de la puerta de enlace VPN y el servidor DNS es inaccesible o no está disponible, el proceso de creación del entorno de servicio de la aplicación también fallará.

## <a name="creating-a-network-security-group"></a>Creación de un grupo de seguridad de red ##
Para información detallada sobre cómo funcionan los grupos de seguridad de la red, consulte la siguiente [información][NetworkSecurityGroups].  Resalta los detalles de contacto en red grupos de seguridad, con un enfoque en configurar y aplicar un grupo de seguridad de red en una subred que contiene un entorno de servicio de la aplicación.

**Nota:** Grupos de seguridad de red se pueden configurar gráficamente mediante el [Portal de Azure](https://portal.azure.com) o a través de PowerShell de Azure.

Primero se crean los grupos de seguridad de red como una entidad independiente asociada a una suscripción. Dado que se crean los grupos de seguridad de red en un área de Azure, asegúrese de que el grupo de seguridad de la red se crea en la misma región como el entorno de servicio de la aplicación.

A continuación muestra la creación de un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Una vez creado un grupo de seguridad de red, una o más reglas de seguridad de red se agregan a él.  Dado que el conjunto de reglas puede cambiar con el tiempo, es aconsejable espaciar el esquema de numeración usado para las prioridades de la regla que sea fácil insertar reglas adicionales con el tiempo.

En el ejemplo siguiente se muestra una regla que concede acceso a los puertos de administración necesarios para la infraestructura de Azure para administrar y mantener un entorno de servicio de la aplicación.  Tenga en cuenta que todo el tráfico de administración fluye a través de SSL y se asegura mediante certificados de cliente, por lo que incluso si se abren los puertos son inaccesibles por cualquier entidad distinta de la infraestructura de administración de Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Al bloquear el acceso al puerto 80 y 443 para "Ocultar" un entorno de servicio de la aplicación detrás de dispositivos ascendentes o servicios, debe conocer la dirección IP precede en la cadena.  Por ejemplo, si utiliza un servidor de aplicaciones web (WAF), el WAFS tendrá su propia dirección IP (o direcciones) que utiliza al tráfico de proxy a un entorno de servicio de la aplicación downstream.  Debe usar esta dirección IP en el parámetro *SourceAddressPrefix* de una regla de seguridad de la red.

En el ejemplo siguiente, el tráfico entrante de una dirección IP específica upstream está permitido explícitamente.  La dirección *1.2.3.4* se utiliza como un marcador de posición para la dirección IP de un WAFS precede en la cadena.  Cambie el valor para que coincida con la dirección utilizada por el dispositivo ascendente o el servicio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Si se desea la compatibilidad con FTP, las siguientes reglas pueden utilizarse como plantilla para conceder acceso a los datos y puerto de control FTP puertos de canal.  Puesto que FTP es un protocolo de estado, no puede enrutar tráfico FTP a través de un dispositivo tradicional de firewall o proxy HTTP/HTTPS.  En este caso debe establecer el *SourceAddressPrefix* en un valor diferente: por ejemplo el intervalo de direcciones IP de desarrolladores o equipos de implementación que FTP que utilicen. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** el intervalo de puertos de canal de datos puede cambiar durante el período de vista previa.)

Si se utiliza la depuración remota con Visual Studio, las reglas siguientes muestran cómo conceder acceso.  Hay una regla independiente para cada versión compatible de Visual Studio ya que cada versión utiliza un puerto diferente para la depuración remota.  Al igual que con el acceso FTP, tráfico de depuración remoto no fluya correctamente a través de un dispositivo de proxy o WAFS tradicional.  El *SourceAddressPrefix* en su lugar puede establecerse en el intervalo de direcciones IP de los equipos de desarrolladores ejecutando Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Asignar un grupo de seguridad de red a una subred ##
Un grupo de seguridad de la red tiene una regla de seguridad predeterminada que se deniega el acceso a todo el tráfico externo.  El resultado de la combinación de las reglas de seguridad de red descritas anteriormente y la regla de seguridad de forma predeterminada, bloqueando el tráfico entrante, es que sólo el tráfico desde la dirección origen intervalos asociados con una acción de *Permitir* poder enviar tráfico a aplicaciones que se ejecutan en un entorno de servicio de la aplicación.

Después de que un grupo de seguridad de red se rellena con las reglas de seguridad, debe asignarse a la subred que contiene el entorno de servicio de la aplicación.  El comando de asignación hace referencia el nombre de la red virtual donde reside el entorno de servicio de la aplicación, así como el nombre de la subred donde se creó el entorno de servicio de la aplicación.  

El ejemplo siguiente muestra un grupo de seguridad de red que se asigna a una subred y una red virtual:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Una vez que se realiza correctamente la asignación de grupo de seguridad de red (la asignación es a operaciones de larga duración y puede tardar unos minutos en completarse), sólo el tráfico entrante que coincida con las reglas de *Permitir* correctamente llegará a aplicaciones en el entorno de servicio de la aplicación.

Su integridad en el ejemplo siguiente se muestra cómo quitar y, por tanto, no asociar el grupo de seguridad de red de la subred:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Consideraciones especiales para SSL explícito de IP ##
Si una aplicación está configurada con un IP-SSL explícito de direcciones (aplicable a ASEs que sólo cuentan con un público VIP), en lugar de utilizar la dirección IP predeterminada del entorno de servicio de la aplicación, HTTP y HTTPS flujos de tráfico en la subred a través de un conjunto diferente de los puertos que no sean los puertos 80 y 443.

El par de puertos utilizados por cada dirección IP SSL individual se puede encontrar en la interfaz de usuario del portal de blade UX de detalles del entorno de servicio de la aplicación.  Seleccionadas "todas las configuraciones"--> "Direcciones IP".  La hoja "Direcciones IP", muestra una tabla de todas las direcciones IP SSL configuradas explícitamente para el entorno de servicio de la aplicación, junto con el par de puerto especial que se utiliza para enrutar el tráfico HTTP y HTTPS asociado a cada dirección IP SSL.  Es este par de puerto que debe utilizarse para los parámetros de DestinationPortRange al configurar reglas en un grupo de seguridad de la red.

Cuando una aplicación en un ASE está configurada para utilizar SSL de IP, los clientes externos no podrán ver y no es necesario preocuparse por la asignación de puerto especial par.  Tráfico de las aplicaciones fluirá normalmente a la dirección IP SSL configurada.  La traducción a la par de puerto especial automáticamente sucede internamente durante el tramo final de enrutar el tráfico en la subred que contenga la ASE. 

## <a name="getting-started"></a>Introducción

Para comenzar con entornos de servicio de la aplicación, vea [Introducción al entorno de servicio de la aplicación][IntroToAppServiceEnvironment]

Todos los artículos y cómo-de para entornos de servicio de la aplicación están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para detalles sobre aplicaciones de forma segura conectarse a recursos de back-end de un entorno de servicio de la aplicación, vea [la conexión segura a recursos del servidor desde un entorno de servicio de la aplicación][SecurelyConnecttoBackend]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, consulte el [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
