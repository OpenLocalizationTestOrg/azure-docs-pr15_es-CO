<properties
   pageTitle="Crear una puerta de enlace de la aplicación mediante el portal | Microsoft Azure"
   description="Aprenda a crear una puerta de enlace de la aplicación mediante el portal"
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

# <a name="create-an-application-gateway-by-using-the-portal"></a>Crear una puerta de enlace de la aplicación mediante el portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásico](application-gateway-create-gateway.md)
- [Plantilla de administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones Azure es un equilibrador de carga de nivel 7. Proporciona failover, enrutamiento de rendimiento de las solicitudes HTTP entre diferentes servidores, ya estén en la nube o local. Puerta de enlace de aplicación proporciona muchas funciones de controlador de entrega de aplicaciones (ADC) incluye Equilibrio de carga HTTP, la afinidad de sesión basada en cookies, descarga de Secure Sockets Layer (SSL), sondeos de estado personalizado, soporte para múltiples sitios y muchos otros. Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de la aplicación mediante el portal de Azure.

Esta situación será:

- Crear una puerta de enlace de aplicaciones medianas con dos instancias.
- Crear una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
- Crear una subred llamada Appgatewaysubnet que utiliza 10.0.0.0/28 como su bloque CIDR.
- Configurar un certificado para la descarga SSL.

![Ejemplo de escenario][scenario]

>[AZURE.IMPORTANT] Configuración adicional de la puerta de enlace de la aplicación, incluida la salud personalizada sondeos, se configuran las direcciones de grupo de back-end y reglas adicionales después de configura la puerta de enlace de la aplicación y no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de comenzar

Puerta de enlace de aplicación Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para tener varias subredes. Una vez implementada una puerta de enlace de aplicación a una subred, puertas de enlace de aplicación adicionales sólo son capaces de agregarse a la subred.

## <a name="create-the-application-gateway"></a>Crear la puerta de enlace de la aplicación

### <a name="step-1"></a>Paso 1

Desplácese hasta el portal de Azure, haga clic en **nuevo** > **red** > **Application Gateway**

![Creación de puerta de enlace de aplicación][1]

### <a name="step-2"></a>Paso 2

A continuación, rellene la información básica acerca de la puerta de enlace de la aplicación. Cuando haya terminado, haga clic en **Aceptar**

La información necesaria para la configuración básica es:

- **Nombre** : el nombre de la puerta de enlace de la aplicación.
- **Nivel** - este es el nivel de la puerta de enlace de la aplicación. Existen dos niveles **WAFS** y **estándar**. WAFS habilita la función de cortafuegos de aplicación web.
- **Tamaño SKU** - esta configuración es el tamaño de la puerta de enlace de aplicación, las opciones disponibles son (**pequeña**, **mediana**y **grande**). *Pequeña no está disponible cuando se elige el nivel de WAFS*
- **Recuento de instancia** - el número de instancias, este valor debe ser un número entre 2 y 10.
- **Grupo de recursos** : el grupo de recursos para sostener la puerta de enlace de la aplicación, puede ser un grupo de recursos existente o uno nuevo.
- **Ubicación** : la región de la puerta de enlace de la aplicación, es la misma ubicación en el grupo de recursos. *La ubicación es importante como la red virtual y IP pública debe estar en la misma ubicación que la puerta de enlace*.

![configuración básica de Mostrar hoja][2]

>[AZURE.NOTE] Puede elegir un número de instancia de 1 con fines de prueba. Es importante saber que cualquier número de instancias en dos instancias no está cubierto por el acuerdo y, por tanto, no se recomienda. Pequeñas puertas de enlace que se van a utilizar para pruebas de desarrollo y no con fines de producción.

### <a name="step-3"></a>Paso 3

Una vez definidas las configuraciones básicas, el siguiente paso es definir la red virtual que se utilizará. La aplicación que la puerta de enlace de aplicación equilibrio de carga para casas de la red virtual.

Haga clic en **Elegir una red virtual** para configurar la red virtual.

![lámina que muestra la configuración de puerta de enlace de aplicación][3]

### <a name="step-4"></a>Paso 4

En la hoja *Seleccione la red Virtual* , haga clic en **Crear nuevo**

Mientras no se explica en este escenario, se pudo seleccionar una red Virtual existente en este momento.  Si se utiliza una red virtual existente, es importante saber que la red virtual hay una subred vacía o una subred de sólo recursos de puerta de enlace de aplicación que se utilizará.

![Seleccione el módulo de red virtual][4]

### <a name="step-5"></a>Paso 5

Rellene la información de la red en el servidor blade de **Crear una red Virtual** como se describe en la descripción del [escenario](#scenario) anterior.

![Crear hoja de red virtual con información introducida][5]

### <a name="step-6"></a>Paso 6

Una vez que se crea la red virtual, el siguiente paso es definir el front-end IP para la puerta de enlace de la aplicación. En este punto, la elección es entre un público o una dirección IP privada para el front-end. La elección depende de si la aplicación es internet enfrentadas o interno sólo. Este escenario supone que utiliza una dirección IP pública. Para elegir una dirección IP privada, puede hacer clic en el botón **privado** . Se elige una dirección IP asignada automáticamente o puede hacer clic en la casilla de verificación **Elegir una dirección IP privada específica** para introducir uno manualmente.

### <a name="step-7"></a>Paso 7

Haga clic en **Elegir una dirección IP pública**. Si hay una dirección IP pública existente se puede elegir en este momento, en este escenario se crea una nueva dirección IP pública. Haga clic en **Crear nuevo**

![Elija módulo de direcciones IP pública][6]

### <a name="step-8"></a>Paso 8

A continuación, asigne un nombre descriptivo de la dirección IP pública y haga clic en **Aceptar**

![Crear hoja de dirección IP pública][7]

### <a name="step-9"></a>Paso 9

La última opción para configurar al crear una puerta de enlace de aplicaciones es la configuración de agente de escucha.  Si se utiliza **http** , no quedará nada para configurar y se puede hacer clic en **Aceptar** . Para utilizar **https** configuración se requieren más.

Para utilizar **https**, se requiere un certificado. La clave privada del certificado es necesaria para una exportación PFX de certificado y una contraseña que deba facilitarse.

### <a name="step-10"></a>Paso 10

Haga clic en **HTTPS**, haga clic en el icono de **carpeta** junto al control textbox de **certificado PFX cargar** .
Desplácese hasta el archivo de certificado .pfx en su sistema de archivos. Una vez seleccionado, asigne un nombre descriptivo al certificado y escriba la contraseña para el archivo .pfx.

Cuando haya finalizado, haga clic en **Aceptar** para revisar la configuración de la puerta de enlace de la aplicación.

![Sección de configuración de agente de escucha en la hoja de configuración][9]

### <a name="step-11"></a>Paso 11

Revise la página Resumen y haga clic en **Aceptar**.  Ahora la puerta de enlace de la aplicación está en la cola y se creó.

### <a name="step-12"></a>Paso 12

Una vez que se ha creado la puerta de enlace de la aplicación, desplácese a él en el portal para continuar con la configuración de la puerta de enlace de la aplicación.

![Vista de recursos de puerta de enlace de aplicación][10]

Estos pasos crean una puerta de enlace de una aplicación básica con configuración predeterminada para la escucha, grupo de back-end, configuración de http de back-end y reglas. Puede modificar estos ajustes para adaptarse a la implementación una vez que el provisioning es correcto.

## <a name="next-steps"></a>Próximos pasos

Esta situación crea una puerta de enlace de aplicación predeterminado. Los siguientes pasos son configurar la puerta de enlace de aplicación agregando a miembros de grupo, modificar la configuración y ajustar las reglas en la puerta de enlace para que funcione correctamente.

Aprenda a crear sondeos de salud personalizada visitando [crear un sondeo de estado personalizado](application-gateway-create-probe-portal.md)

Aprenda a configurar la descarga de SSL y realizar el descifrado de SSL costoso fuera de sus servidores web visitando [Configurar la descarga de SSL](application-gateway-ssl-portal.md)

Aprenda a proteger sus aplicaciones con el [Servidor de aplicaciones Web](application-gateway-webapplicationfirewall-overview.md) una característica de puerta de enlace de aplicaciones.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
