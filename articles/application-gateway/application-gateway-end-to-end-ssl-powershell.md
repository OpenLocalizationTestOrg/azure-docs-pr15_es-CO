<properties
    pageTitle="Configurar la directiva de SSL y SSL de extremo a extremo con Application Gateway | Microsoft Azure"
    description="Este artículo describe cómo configurar SSL de extremo a extremo con Application Gateway mediante PowerShell del Administrador de recursos de Azure"
    services="application-gateway"
    documentationCenter="na"
    authors="georgewallace"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="application-gateway"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="gwallace"/>

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurar la directiva de SSL y SSL de extremo a extremo con la puerta de enlace de aplicaciones usando PowerShell

## <a name="overview"></a>Información general

Puerta de enlace de aplicación admite cifrado de extremo a extremo de tráfico. Para ello, puerta de enlace de aplicación finaliza la conexión SSL en la puerta de enlace de la aplicación. La puerta de enlace aplica las reglas de enrutamiento para el tráfico, vuelve a cifra el paquete y reenvía el paquete del back-end adecuado basándose en las reglas de enrutamiento definidas. Cualquier respuesta desde el servidor web pasa por el mismo proceso al usuario final.

Otra característica de dicha puerta de enlace de aplicación admite es deshabilitar ciertas versiones del protocolo SSL. Puerta de enlace de aplicación admite deshabilitar la siguiente versión de protocolo; TLSv1.0, TLSv1.1 y TLSv1.2.

> [AZURE.NOTE] SSL 2.0 y SSL 3.0 están deshabilitados de forma predeterminada y no se puede habilitar. Que se consideran no seguras y no puedan utilizarse con Application Gateway

![imagen de escenario][scenario]

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de la aplicación mediante SSL de extremo a extremo con PowerShell.

Esta situación será:

- Crear un grupo de recursos denominado "appgw-rg"
- Crear una red virtual denominada "appgwvnet" con un bloque CIDR reservado de 10.0.0.0/16.
- Crear dos subredes llamado "appgwsubnet" y "appsubnet".
- Crear una puerta de enlace de pequeña aplicación compatible con el cifrado SSL de extremo a extremo que deshabilita ciertos protocolos SSL.

## <a name="before-you-begin"></a>Antes de comenzar

Para configurar SSL de extremo a extremo con una puerta de enlace de la aplicación, es necesario para la puerta de enlace de un certificado y los certificados son necesarios para los servidores back-end. El certificado de puerta de enlace se utiliza para cifrar y descifrar el tráfico enviado a través de SSL. El certificado de puerta de enlace debe estar en formato de intercambio de información Personal (pfx). Este formato de archivo permite exportar la clave privada que se requiere por la puerta de enlace de la aplicación para realizar el cifrado y descifrado de tráfico.

Para el cifrado ssl de extremo a extremo el back-end debe ser lista blanca con puerta de enlace de aplicaciones. Para ello, cargar el certificado público de la back-ends en la puerta de enlace de la aplicación. Esto garantiza que la puerta de enlace de aplicaciones se comunica sólo con instancias de servidor conocidos. Esto protege aún más la comunicación de extremo a extremo.

Este proceso se describe en los pasos siguientes:

## <a name="create-the-resource-group"></a>Crear el grupo de recursos

Esta sección le guiará a través de la creación de un grupo de recursos que contiene la puerta de enlace de la aplicación.

### <a name="step-1"></a>Paso 1

Inicie sesión en su cuenta de Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Seleccione la suscripción que se utilizará para este escenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Paso 3

Crear un grupo de recursos (Saltar este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de la aplicación

En el ejemplo siguiente se crea una red virtual y dos subredes. Una subred se utiliza para contener la puerta de enlace de la aplicación. La otra subred se utiliza para el back-ends que hospeda la aplicación web.

### <a name="step-1"></a>Paso 1

Asignar un intervalo de direcciones de la subred se utiliza para la puerta de enlace de aplicación.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Subredes configuradas para puerta de enlace de aplicación deben ajustarse correctamente. Una puerta de enlace de aplicaciones puede configurarse para un máximo de 10 instancias. Cada instancia tiene 1 dirección IP de la subred. Demasiado pequeño de una subred puede afectar negativamente el escalado de una puerta de enlace de la aplicación.

### <a name="step-2"></a>Paso 2

Asignar un intervalo de direcciones que se utilizará para el grupo de direcciones de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Paso 3

Crear una red virtual con las subredes definidas en los pasos anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Paso 4

Recuperar los recursos de la red virtual y recursos de la subred que se utilizará en los pasos siguientes:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público que se utilizará para la puerta de enlace de la aplicación. Se utiliza esta dirección IP pública un paso siguiente.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Puerta de enlace de aplicación no admite el uso de una dirección IP pública que se creó con una etiqueta de dominio definida. Se admite sólo una dirección IP pública con una etiqueta de dominio creado dinámicamente. Si necesita un nombre dns descriptivos para la puerta de enlace de la aplicación, se recomienda utilizar un registro cname como un alias.

## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de la aplicación. Los pasos siguientes crean los elementos de configuración que son necesarios para un recurso de la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de la puerta de enlace de aplicación, este valor configura qué subred utiliza la puerta de enlace de la aplicación. Cuando se inicia en la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enruta el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Paso 2

Crear una configuración de IP front-end, esta opción asigna una dirección ip pública o privada para el front-end de la puerta de enlace de la aplicación. El paso siguiente asocia la dirección IP pública en el paso anterior con la configuración IP del cliente.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Paso 3

Configurar el grupo de direcciones IP de back-end con las direcciones IP de los servidores web de back-end. Estas direcciones IP son las direcciones IP que reciben el tráfico de red que proviene el extremo IP front-end. Reemplace las siguientes direcciones IP para agregar sus propios extremos de dirección IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nombre de dominio completo (FQDN) también es un valor válido en lugar de una dirección ip para los servidores back-end mediante el modificador - BackendFqdns.

### <a name="step-4"></a>Paso 4

Configurar el puerto front-end de IP para el extremo IP público. Este puerto es el puerto en el que los usuarios finales se conectan a.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Paso 5

Configurar el certificado para la puerta de enlace de la aplicación. Este certificado se utiliza para descifrar y volver a cifrar el tráfico en la puerta de enlace de la aplicación.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Este ejemplo configura el certificado utilizado para la conexión SSL. El certificado debe estar en formato PFX y la contraseña debe tener entre 4 a 12 caracteres.

### <a name="step-6"></a>Paso 6

Crear el agente de escucha HTTP para la puerta de enlace de la aplicación. Asignar el certificado de ssl, el puerto y configuración de front-end ip a utilizar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Paso 7

Cargar el certificado que se utilizará en los recursos del grupo de back-end de ssl habilitado.

> [AZURE.NOTE] El sondeo por defecto Obtiene la clave pública de enlace SSL **predeterminado** en la dirección IP de back-end y compara el valor de clave pública que recibe al valor de clave pública que se proporciona aquí. La clave pública recuperada puede no ser necesariamente al sitio deseado al que fluirá el tráfico **Si** utiliza encabezados de host y SNI en el back-end. En caso de duda, visite https://127.0.0.1/ en el back-ends para confirmar qué certificado se utiliza para el enlace de SSL de **forma predeterminada** . En esta sección, utilice la clave pública de esa solicitud. Si usa encabezados de host y SNI en enlaces HTTPS y no recibe una respuesta y un certificado de una solicitud del explorador de manual a https://127.0.0.1/ en el back-ends, debe configurar un enlace SSL de forma predeterminada en el back-ends. Si no lo hace, se producirá un error en las sondas y el back-end no se estará en la lista blanca.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] El certificado proporcionado en este paso debe ser la clave pública de los certificados pfx presentes en el back-end. Exporte el certificado (no el certificado raíz) instalado en el servidor back-end. CER del formato y se usa en este paso. Este paso se blancas el back-end con la puerta de enlace de la aplicación.

### <a name="step-8"></a>Paso 8

Configurar los valores de http de servicios de fondo de puerta de enlace de aplicaciones. Asignar el certificado cargado en el paso anterior a la configuración de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Paso 9

Crear una regla de enrutamiento de equilibrador de carga que configura el comportamiento de equilibrador de carga. En este ejemplo, se crea una regla básica por turnos.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Paso 10

Configurar el tamaño de la instancia de puerta de enlace de aplicaciones.  Los tamaños disponibles son **estándar\_Small**, **estándar\_medio**, y **Standard\_grande**.  Capacidad, los valores disponibles son 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Puede elegir un número de instancia de 1 con fines de prueba. Es importante saber que cualquier número de instancias en dos instancias no está cubierto por el acuerdo y, por tanto, no se recomienda. Pequeñas puertas de enlace que se van a utilizar para pruebas de desarrollo y no con fines de producción.

### <a name="step-11"></a>Paso 11

Configurar la directiva SSL que se utilizará en la puerta de enlace de la aplicación. Puerta de enlace de aplicación admite la capacidad para deshabilitar determinadas versiones del protocolo SSL.

Los valores siguientes son una lista de versiones del protocolo que se pueden deshabilitar.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

En el ejemplo siguiente se deshabilita TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Crear la puerta de enlace de la aplicación

Con todos los pasos anteriores, cree la puerta de enlace de la aplicación. La creación de la puerta de enlace es un proceso de larga ejecución.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Deshabilitar las versiones del protocolo SSL en una puerta de enlace de aplicación existente

Los pasos anteriores le llevará a través de la creación de una aplicación con ssl de extremo a extremo y deshabilitar ciertas versiones del protocolo SSL. En el ejemplo siguiente se deshabilita determinadas directivas SSL en una puerta de enlace de aplicación existente.

### <a name="step-1"></a>Paso 1

Recuperar la puerta de enlace de la aplicación para actualizar.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Paso 2

Definir una directiva SSL. En el ejemplo siguiente, TLSv1.0 y TLSv1.1 están deshabilitadas.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Paso 3

Por último, actualizar la puerta de enlace. Es importante destacar que este último paso es una tarea de larga duración. Cuando esto sucede, se configura ssl de extremo a extremo en la puerta de enlace de la aplicación.

    $gw | Set-AzureRmApplicationGateway

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

Obtenga información sobre cómo reforzar la seguridad de las aplicaciones web con servidor de aplicaciones Web a través de la puerta de enlace de aplicación visitando [Introducción al Firewall de aplicaciones Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
