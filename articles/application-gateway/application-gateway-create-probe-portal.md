<properties
   pageTitle="Crear un sondeo personalizada para una puerta de enlace de la aplicación mediante el portal | Microsoft Azure"
   description="Aprenda a crear una sonda personalizada de puerta de enlace de aplicación mediante el portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Crear un sondeo personalizada para la puerta de enlace de aplicación mediante el portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell Resource Manager](application-gateway-create-probe-ps.md)
- [Azure PowerShell clásico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por crear un sondeo de estado personalizado en una puerta de enlace de aplicación existente.
El escenario se supone que ya ha seguido los pasos para [crear una puerta de enlace de la aplicación](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Crear el sondeo

Los sondeos están configurados en un proceso de dos pasos a través del portal. El primer paso es crear el sondeo, a continuación agregue el sondeo para la configuración de http back-end de la puerta de enlace de la aplicación.

### <a name="step-1"></a>Paso 1

Vaya a http://portal.azure.com y seleccione una puerta de enlace de aplicación existente.

![Información general sobre la puerta de enlace de aplicaciones][1]

### <a name="step-2"></a>Paso 2

**Sondeos** y haga clic en el botón **Agregar** para agregar un nuevo sondeo.

![Agregar hoja de sondeo con información rellenado][2]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para el sondeo y cuando haya terminado, haga clic en **Aceptar**.

- **Nombre** : se trata de un nombre descriptivo a la sonda que está accesible en el portal.
- **Host** : éste es el nombre de host que se utiliza para el sondeo. Aplicable sólo cuando múltiples sitios está configurado en la puerta de enlace de aplicación, en caso contrario, utilice '127.0.0.1'. Esto es diferente del nombre de host de máquina virtual.
- **Ruta de acceso** : el resto de la dirección url completa para el sondeo personalizada. Una ruta válida comienza con '/'
- **(En segundos)** - la frecuencia de ejecución de la sonda para comprobar la salud No se recomienda establecer el inferior a 30 segundos.
- **Tiempo de espera (segundos)** - la cantidad de tiempo que espera el sondeo antes de agotarse. El intervalo de tiempo de espera debe ser lo suficientemente alto como para que una llamada http puede realizarse para asegurarse de que está disponible la página de salud de back-end.
- **Umbral negativo** : número de intentos fallidos se considera en mal estado. Un umbral de 0 significa que si se produce un error de comprobación de un mantenimiento el back-end se determinará mal estado inmediatamente.

> [AZURE.IMPORTANT] el nombre de host no es el nombre del servidor. Este es el nombre del host virtual que se ejecuta en el servidor de aplicaciones. La sonda se envía a http://(host name):(port from httpsetting)/urlPath

![configuración de sondeo][3]

## <a name="add-probe-to-the-gateway"></a>Agregar el sondeo a la puerta de enlace

Ahora que se ha creado la sonda, ha llegado el momento para agregarlo a la puerta de enlace. Configuración de sondeo se establece en la configuración de http back-end de la puerta de enlace de la aplicación.

### <a name="step-1"></a>Paso 1

Haga clic en la **Configuración HTTP** de la puerta de enlace de la aplicación y, a continuación, haga clic en la configuración de http de back-end actual en la ventana para abrir la hoja de configuración.

![ventana de configuración de HTTPS][4]

### <a name="step-2"></a>Paso 2

En la hoja de configuración de **appGatewayBackEndHttp** , haga clic en **sondeo de uso personalizada** y elija el sondeo que creó en la sección [crear el sondeo](#createprobe) .
Cuando haya terminado, haga clic en **Aceptar** y se aplica la configuración.

![hoja de configuración de appgatewaybackend][5]

El sondeo predeterminado comprueba el acceso predeterminado a la aplicación web. Ahora que se ha creado una sonda personalizada, la puerta de enlace de la aplicación utiliza la ruta personalizada definida para supervisar la salud para el servidor seleccionado. Basándose en los criterios que definió, la puerta de enlace de la aplicación comprueba el archivo especificado en el sondeo. Si la llamada a host: puerto / ruta de acceso no devuelve una respuesta de estado Http 200 OK, el servidor se ponga fuera de la rotación, una vez alcanzado el umbral en mal estado. Búsqueda continúa en la instancia de mal estada para determinar cuando vuelva a estar saludable. Una vez que la instancia se agrega de nuevo al grupo de servidor en buen estado tráfico empieza fluir a él otra vez y sondeo para la instancia continúa en el intervalo especificado de usuario normal.


## <a name="next-steps"></a>Próximos pasos

Para obtener información sobre cómo configurar descarga SSL con Azure Application Gateway consulte [Configurar la descarga de SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png