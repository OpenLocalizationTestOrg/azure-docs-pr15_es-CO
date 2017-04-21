<properties
   pageTitle="Configurar una puerta de enlace de la aplicación para la descarga SSL mediante la implementación clásica | Microsoft Azure"
   description="Este artículo proporciona instrucciones para crear una puerta de enlace de la aplicación con SSL de descarga mediante el modelo de implementación de clásico de Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar una puerta de enlace de la aplicación para la descarga SSL mediante el modelo de implementación clásica

> [AZURE.SELECTOR]
-[Portal de Azure](application-gateway-ssl-portal.md)
-[PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
-[PowerShell clásico de Azure](application-gateway-ssl.md)

Puerta de enlace de aplicaciones Azure puede configurarse para terminar la sesión de Secure Sockets Layer (SSL) en la puerta de enlace para evitar costosas tareas de descifrado de SSL para producir en la granja de servidores web. Descarga SSL también simplifica la instalación de servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de comenzar

1. Instalar la versión más reciente de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Compruebe que dispone de una red virtual de trabajo con una subred válida. Asegúrese de que no hay máquinas virtuales o las implementaciones de nube utilizan la subred. La puerta de enlace de aplicación debe ser por sí mismo en una subred de red virtual.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en la red virtual o con una IP pública/VIP.

Para configurar la descarga SSL en una puerta de enlace de la aplicación, realice los pasos siguientes en el orden indicado:

1. [Crear una puerta de enlace de aplicaciones](#create-an-application-gateway)
2. [Cargar certificados SSL](#upload-ssl-certificates)
3. [Configurar la puerta de enlace](#configure-the-gateway)
4. [Establecer la configuración de puerta de enlace](#set-the-gateway-configuration)
5. [Iniciar la puerta de enlace](#start-the-gateway)
6. [Comprobar el estado de la puerta de enlace](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicaciones

Para crear la puerta de enlace, utilice el cmdlet **New-AzureApplicationGateway** , reemplazando los valores por los suyos propios. Facturación de la puerta de enlace no se inicia en este momento. Facturación comienza en un paso posterior, cuando se ha iniciado correctamente la puerta de enlace.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que la puerta de enlace se ha creado, puede utilizar el cmdlet **Get-AzureApplicationGateway** .

En el ejemplo, *Descripción*, *InstanceCount*y *GatewaySize* son parámetros opcionales. El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Grandes y pequeños son otros valores disponibles. *VirtualIPs* y *DnsName* aparecen en blanco porque aún no ha iniciado la puerta de enlace. Estos valores se crean una vez que la puerta de enlace está en el estado de ejecución.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Cargar certificados SSL

Utilizar **Add-AzureApplicationGatewaySslCertificate** para cargar el certificado de servidor en formato *pfx* en la puerta de enlace de la aplicación. El nombre del certificado es un nombre elegido por el usuario y debe ser único dentro de la puerta de enlace de la aplicación. Este certificado se conoce con este nombre en todas las operaciones de administración de certificados en la puerta de enlace de la aplicación.

Este ejemplo siguiente muestra el cmdlet, reemplace los valores en la muestra por el suyo propio.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

A continuación, validar la carga de certificados. Use el cmdlet **Get-AzureApplicationGatewayCertificate** .

El cmdlet se muestra en la primera línea, seguido de la salida.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] La contraseña del certificado debe ser entre 4 a 12 caracteres, letras o números. No se aceptan caracteres especiales.

## <a name="configure-the-gateway"></a>Configurar la puerta de enlace

Una configuración de puerta de enlace de la aplicación consta de varios valores. Los valores pueden estar vinculados entre sí para crear la configuración.

Los valores son:

- **Grupo de back-end server:** La lista de direcciones IP de los servidores back-end. Las direcciones IP enumeradas bien deben pertenecer a la subred de la red virtual o deben ser una IP pública/VIP.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene valores como afinidad basada en cookies, protocolo y puerto. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** Este puerto es el puerto público que se abre en la puerta de enlace de la aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha y el grupo de servidor back-end y define qué grupo de servidor back-end debe dirigirse al tráfico cuando llegue a un agente de escucha determinado. Actualmente, sólo la regla *básica* es compatible. La regla *básica* es la distribución de carga por turnos.

**Notas de configuración adicionales**

Para la configuración de los certificados SSL, el protocolo en **HttpListener** debería cambiar a *Https* (con distinción entre mayúsculas y minúsculas). El elemento **SslCert** se agrega al **HttpListener** con el valor establecido para el mismo nombre que se utiliza en la carga de la anterior sección de certificados SSL. Debe actualizarse el puerto front-end en 443.

**Para habilitar la afinidad de cookie**: puede configurarse una puerta de enlace de la aplicación para asegurarse de que una solicitud de una sesión de cliente siempre se dirige a la misma VM en la granja de servidores web. Este escenario se realiza mediante la inyección de una cookie de sesión que permite a la puerta de enlace dirigir el tráfico adecuadamente. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *habilitado* en el elemento **BackendHttpSettings** .



Se puede construir la configuración mediante la creación de un objeto de configuración o mediante un archivo de configuración XML.
Para construir la configuración mediante un archivo de configuración XML, utilice el ejemplo siguiente:

**Ejemplo de configuración XML**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


## <a name="set-the-gateway-configuration"></a>Establecer la configuración de puerta de enlace

A continuación, configure la puerta de enlace de la aplicación. Puede utilizar el cmdlet **Set-AzureApplicationGatewayConfig** con un objeto de configuración o con un archivo de configuración XML.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Iniciar la puerta de enlace

Una vez que se ha configurado la puerta de enlace, use el cmdlet **Start-AzureApplicationGateway** para iniciar la puerta de enlace. Facturación de una puerta de enlace de la aplicación comienza después de que se ha iniciado correctamente la puerta de enlace.


**Nota:** El cmdlet **Start-AzureApplicationGateway** puede tardar hasta 15-20 minutos en Finalizar.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Comprobar el estado de la puerta de enlace

Use el cmdlet **Get-AzureApplicationGateway** para comprobar el estado de la puerta de enlace. Si **AzureApplicationGateway de inicio** correcta en el paso anterior, debe estar ejecutando el *estado* y *VirtualIPs* y *DnsName* deben tener entradas válidas.

Este ejemplo muestra una puerta de enlace de la aplicación que está activo y está preparado para tomar el tráfico.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Próximos pasos


Si desea obtener más información sobre opciones equilibrio de carga en general, consulte:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)