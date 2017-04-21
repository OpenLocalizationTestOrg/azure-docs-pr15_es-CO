<properties
   pageTitle="Configurar una puerta de enlace de la aplicación para la descarga SSL mediante el portal | Microsoft Azure"
   description="Esta página contiene instrucciones para crear una puerta de enlace de la aplicación con SSL de descarga a través del portal"
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurar una puerta de enlace de la aplicación para la descarga SSL mediante el portal

> [AZURE.SELECTOR]
-[Portal de Azure](application-gateway-ssl-portal.md)
-[PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
-[PowerShell clásico de Azure](application-gateway-ssl.md)

Puerta de enlace de aplicaciones Azure puede configurarse para terminar la sesión de Secure Sockets Layer (SSL) en la puerta de enlace para evitar costosas tareas de descifrado de SSL para producir en la granja de servidores web. Descarga SSL también simplifica la instalación de servidor front-end y la administración de la aplicación web.

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por configurar SSL de descarga en una puerta de enlace de aplicación existente. El escenario se supone que ya ha seguido los pasos para [crear una puerta de enlace de la aplicación](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de comenzar

Para configurar la descarga SSL con una puerta de enlace de aplicaciones, se requiere un certificado. Este certificado se carga en la puerta de enlace de la aplicación y se utiliza para cifrar y descifrar el tráfico enviado a través de SSL. El certificado debe estar en formato de intercambio de información Personal (pfx). Este formato de archivo permite exportar la clave privada que se requiere por la puerta de enlace de la aplicación para realizar el cifrado y descifrado de tráfico.

## <a name="add-an-https-listener"></a>Agregar un agente de escucha HTTPS

El agente de escucha HTTPS busca tráfico basándose en su configuración y ayuda a enrutar el tráfico a las agrupaciones de back-end.

### <a name="step-1"></a>Paso 1

Desplácese hasta el portal de Azure y seleccione una puerta de enlace de aplicación existente

### <a name="step-2"></a>Paso 2

Los agentes de escucha y haga clic en el botón Agregar para agregar un agente de escucha.

![hoja de resumen de puerta de enlace de App][1]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para el agente de escucha y cargar el certificado .pfx, cuando haya terminado, haga clic en Aceptar.

**Nombre** : este valor es un nombre descriptivo del agente de escucha.

**Configuración IP de cliente** : este valor es la configuración IP de cliente que se utiliza para la escucha.

**Puerto front-end (nombre/puerto)** : un nombre descriptivo para el puerto de la parte delantera de la puerta de enlace de la aplicación y el puerto real utilizado.

**Protocolo** - un modificador para determinar si se utiliza https o http para el front-end.

**Certificado (nombre y contraseña)** - descarga SSL si se usa, se requiere para esta configuración un certificado .pfx y un nombre descriptivo y la contraseña son necesarios.

![Agregar hoja de escucha][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Crear una regla y asociarlo a la escucha

Se ha creado la escucha. Es el momento de crear una regla para manejar el tráfico desde el agente de escucha.

### <a name="step-1"></a>Paso 1

Haga clic en las **reglas** de la puerta de enlace de la aplicación y, a continuación, haga clic en Agregar.

![hoja de reglas de puerta de enlace de la aplicación][3]

### <a name="step-2"></a>Paso 2

En el módulo **Agregar regla básica** , escriba el nombre descriptivo para la regla y elija el agente de escucha que se crea en el paso anterior. Elija la configuración de http y el grupo de back-end adecuados y haga clic en **Aceptar**

![ventana de configuración de HTTPS][4]

Ahora, la configuración se guarda en la puerta de enlace de la aplicación. El proceso de almacenamiento de esta configuración puede tardar unos minutos antes de que estén disponibles para ver a través del portal o a través de PowerShell. Una vez guardada la puerta de enlace de la aplicación controla el cifrado y descifrado de tráfico. Controlará todo el tráfico entre los servidores de web back-end y de la puerta de enlace de aplicación sobre http. Cualquier comunicación al cliente si ha iniciado a través de https se devolverá al cliente cifrado.

## <a name="next-steps"></a>Próximos pasos

Para aprender a configurar un sondeo de estado personalizado con Azure Application Gateway, vea [crear un sondeo de estado personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png