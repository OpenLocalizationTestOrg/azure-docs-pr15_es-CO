<properties
   pageTitle="Crear, iniciar o eliminar una puerta de enlace de la aplicación mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de la aplicación para Azure mediante el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Crear, iniciar o eliminar una puerta de enlace de la aplicación mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásico](application-gateway-create-gateway.md)
- [Plantilla de administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones Azure es un equilibrador de carga de nivel 7. Proporciona failover, enrutamiento de rendimiento de las solicitudes HTTP entre diferentes servidores, ya estén en la nube o local. Puerta de enlace de aplicación proporciona muchas funciones de controlador de entrega de aplicaciones (ADC) incluye Equilibrio de carga HTTP, la afinidad de sesión basada en cookies, descarga de Secure Sockets Layer (SSL), sondeos de estado personalizado, soporte para múltiples sitios y muchos otros. Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

Este artículo explica los pasos necesarios para crear, configurar, iniciar y eliminar una puerta de enlace de la aplicación.

>[AZURE.IMPORTANT] Antes de trabajar con recursos de Azure, es importante saber que Azure actualmente tiene dos modelos de implementación: el Administrador de recursos y clásica. Asegúrese de que comprende [Herramientas y modelos de implementación](../azure-classic-rm.md) antes de trabajar con cualquier recurso de Azure. Puede ver la documentación de herramientas diferentes haciendo clic en las fichas en la parte superior de este artículo. Este documento cubre la creación de una puerta de enlace de la aplicación mediante el Administrador de recursos de Azure. Para utilizar la versión clásica, vaya a [crear una puerta de enlace de aplicación clásica implementación mediante PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Antes de comenzar

1. Instalar la versión más reciente de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Si tiene una red virtual existente, seleccione una subred vacía existente o crear una subred en la red existente virtual exclusivamente para su uso por la puerta de enlace de la aplicación. No se puede implementar la puerta de enlace de aplicación a una red virtual diferente que los recursos que se pretende implementar detrás de la puerta de enlace de la aplicación.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en la red virtual o con una IP pública/VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?

- **Grupo de back-end server:** La lista de direcciones IP de los servidores back-end. Las direcciones IP enumeradas bien deben pertenecer a la subred de la red virtual o deben ser una IP pública/VIP.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene valores como afinidad basada en cookies, protocolo y puerto. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** Este puerto es el puerto público que se abre en la puerta de enlace de la aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza a la escucha, el grupo de servidor back-end y define qué grupo de servidor back-end debe dirigirse al tráfico cuando llegue a un agente de escucha determinado.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicaciones

La diferencia entre el uso clásico de Azure y Azure Resource Manager es el orden en el que crear la puerta de enlace de la aplicación y los elementos que deben configurarse.

Con el Administrador de recursos, todos los elementos que hacen que una puerta de enlace de aplicaciones se configuran individualmente y, a continuación, colocar juntos para crear el recurso de puerta de enlace de la aplicación.

Los siguientes son los pasos necesarios para crear una puerta de enlace de la aplicación.

## <a name="create-a-resource-group-for-resource-manager"></a>Crear un grupo de recursos para el Administrador de recursos

Asegúrese de que está utilizando la versión más reciente de PowerShell de Azure. Más información está disponible en el [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure

    Login-AzureRmAccount

Deberá autenticarse con sus credenciales.

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Paso 3

Elegir cuál de sus suscripciones a utilizar Azure.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (Saltar este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Administrador de recursos del Azure exige que todos los grupos de recursos especifican una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de la aplicación utiliza el mismo grupo de recursos.

En el ejemplo anterior, hemos creado un grupo de recursos denominado "appgw-RG" y ubicación "Oeste nosotros".

>[AZURE.NOTE] Si necesita configurar un sondeo para la puerta de enlace de aplicación personalizada, vea [crear una puerta de enlace de aplicaciones con sondeos personalizados mediante el uso de PowerShell](application-gateway-create-probe-ps.md). Consulte los [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de la aplicación

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos.

### <a name="step-1"></a>Paso 1

Asignar la 10.0.0.0/24 intervalo dirección a la variable de la subred que se utilizará para crear una red virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Paso 2

Crear una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región occidental estadounidense con el 10.0.0.0/16 prefijo de subred 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Paso 3

Asignar una variable de subred para los pasos siguientes, que crea una puerta de enlace de la aplicación.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público "publicIP01" en recursos grupo "appgw-rg" para la región occidental EE.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de la aplicación. Los pasos siguientes crean los elementos de configuración que son necesarios para un recurso de la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la aplicación Gateway, toma una dirección IP de la subred configurada y enruta el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Paso 2

Configurar el conjunto de direcciones IP de servidor denominado "pool01" con direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50". Estas direcciones IP son las direcciones IP que reciben el tráfico de red que proviene el extremo IP front-end. Reemplazar las direcciones IP anteriores para agregar sus propios extremos de dirección IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Paso 3

Configurar la configuración de puerta de enlace de aplicación "poolsetting01" para el tráfico de red con equilibrio de carga en el grupo de servicios de fondo.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Paso 4

Configurar el puerto front-end de IP denominado "frontendport01" para el extremo IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Paso 5

Crear la configuración de IP front-end llamada "fipconfig01" y asociar la dirección IP pública a la configuración IP del cliente.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Paso 6

Crear la escucha de nombre "listener01" y asociar el puerto front-end para la configuración IP del cliente.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Paso 7

Crear la regla de enrutamiento de equilibrador de carga denominada "rule01" que configura el comportamiento de equilibrador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Paso 8

Configurar el tamaño de la instancia de puerta de enlace de aplicaciones.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Crear una puerta de enlace de la aplicación mediante el nuevo AzureRmApplicationGateway

Crear una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En este ejemplo, la puerta de enlace de la aplicación se llama "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Paso 9

Recuperar detalles DNS y la dirección VIP de la puerta de enlace de la aplicación desde el recurso IP público adjunto a la puerta de enlace de la aplicación.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Eliminar una puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de la aplicación, siga estos pasos:

### <a name="step-1"></a>Paso 1

Obtener el objeto de puerta de enlace de la aplicación y asociarlo a una variable "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Paso 2

Utilice **AzureRmApplicationGateway de parada** para detener la puerta de enlace de la aplicación.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Una vez que la puerta de enlace de la aplicación está en un estado detenido, utilice el cmdlet **Remove-AzureRmApplicationGateway** para quitar el servicio.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] El **-Forzar** modificador puede utilizarse para suprimir el mensaje de confirmación de quitar.

Para comprobar que se ha quitado el servicio, puede utilizar el cmdlet **Get-AzureRmApplicationGateway** . Este paso no es necesario.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

## <a name="get-application-gateway-dns-name"></a>Obtener el nombre DNS de puerta de enlace de aplicación

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, puerta de enlace de aplicación requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puede golpear la puerta de enlace de aplicación un registro CNAME puede utilizarse para señalar el extremo de la puerta de enlace de aplicación público. [Configurar un nombre de dominio personalizado para en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para ello, recuperar los detalles de la puerta de enlace de la aplicación y su nombre IP/DNS asociado con el elemento PublicIPAddress conectado a la puerta de enlace de la aplicación. Nombre DNS del gateway de aplicación debe utilizarse para crear un registro CNAME, que apunta a las aplicaciones web de dos a este nombre DNS. No se recomienda el uso de registros a ya que puede cambiar la dirección VIP en el reinicio de puerta de enlace de aplicaciones.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Próximos pasos

Si desea configurar la descarga SSL, vea [Configurar una puerta de enlace de la aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno, vea [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (DCI)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones equilibrio de carga en general, consulte:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
