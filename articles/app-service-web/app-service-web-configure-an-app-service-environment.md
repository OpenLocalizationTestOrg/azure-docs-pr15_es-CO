<properties
    pageTitle="Cómo configurar un entorno de servicio de la aplicación | Microsoft Azure"
    description="Configuración, administración y monitoreo de entornos de servicio de la aplicación"
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


# <a name="configuring-an-app-service-environment"></a>Configurar un entorno de servicio de la aplicación #

## <a name="overview"></a>Información general ##

A un alto nivel, un entorno de servicio de la aplicación de Azure consta de varios componentes principales:

- Recursos de cálculo que se ejecutan en el entorno de servicio de la aplicación de servicio alojado
- Almacenamiento de información
- Una base de datos
- Una red Virtual Classic(V1) o recurso Manager(V2) Azure (VNet) 
- Una subred con el servicio de entorno de servicio de la aplicación alojado ejecuta en ella

### <a name="compute-resources"></a>Recursos informáticos

Utilice los recursos computacionales para las agrupaciones de cuatro recursos.  Cada entorno de servicio de aplicación (ASE) tiene un conjunto de front-ends y tres grupos de trabajo posible. No es necesario utilizar todos los grupos de trabajo de tres--si lo desea, puede utilizar sólo una o dos.

Los hosts de los grupos de recursos (front-ends y los trabajadores) no son accesibles directamente a los inquilinos. No puede utilizar el protocolo de escritorio remoto (RDP) para conectarse a ellos, cambiar su aprovisionamiento o actuar como administrador en ellos.

Puede establecer el tamaño y la cantidad de recursos de grupo. En una ASE, tiene cuatro opciones de tamaño, con las etiquetan P1 a P4. Para obtener más información acerca de los tamaños y sus precios, consulte [tarifas de servicio de la aplicación](../app-service/app-service-value-prop-what-is.md).
Cambiar la cantidad o tamaño se llama a una operación de escala.  Operación de escala sólo uno puede ser en curso en un momento.

**Clientes**: los front-ends son los extremos HTTP/HTTPS para las aplicaciones que se encuentran en su ASE. No ejecute cargas de trabajo en la parte delantera.

- Un ASE comienza con dos P2s, que es suficiente para las cargas de trabajo de desarrollo/pruebas y cargas de trabajo de producción de bajo nivel. Recomendamos encarecidamente P3s de moderado a cargas de trabajo pesadas de producción.
- Moderado a cargas de trabajo pesadas de producción, recomendamos que tiene al menos cuatro P3s para asegurarse de que hay suficientes front-ends que se ejecuta cuando se produce el mantenimiento programado. Las actividades de mantenimiento programado desconectará un front-end a la vez. Esto reduce en general capacidad de front-end disponible durante las actividades de mantenimiento.
- No puede agregar una nueva instancia de front-end al instante. Puede tardar entre 2 a 3 horas a disposición.
- Para ajustar de escala adicionales, debe supervisar el porcentaje de la CPU, el porcentaje de memoria y métricas de solicitudes activas para el grupo de aplicaciones para el usuario. Si los porcentajes de CPU o de memoria por encima del 70 por ciento al ejecutar P3s, agregue más front-ends. Si el valor de solicitudes activas promedio hacia fuera a 15.000 a 20.000 solicitudes por front-end, también debe agregar más front-ends. El objetivo general consiste en mantener los porcentajes de CPU y memoria a continuación el 70% y solicitudes activas un promedio hacia fuera por debajo de 15.000 solicitudes por frontal finalizan cuando ejecutas P3s.  

**Los trabajadores**: los trabajadores están donde realmente ejecutan sus aplicaciones. Al aumentar la escala de los planes de servicio de la aplicación, que utiliza seguridad de los trabajadores en el grupo de trabajo asociado.

- No se pueden agregar al instante los trabajadores. Puede tomar de 2 a 3 horas a disposición, independientemente de cuántos se agregan.
- Escalar el tamaño de un recurso de cómputo para cualquier grupo tardará 2 a 3 horas por cada dominio de actualización. Hay 20 actualizar los dominios en un ASE. Si ajusta el tamaño del cálculo de un grupo de trabajo con 10 instancias, podría tardar entre 20 y 30 horas en completarse.
- Si cambia el tamaño de los recursos de cálculo que se utilizan en un grupo de trabajo, hará que se inicie frío de las aplicaciones que se ejecutan en ese grupo de trabajo.

Es la forma más rápida para cambiar el tamaño de recursos computacionales de un grupo de trabajo que no se está ejecutando alguna de las aplicaciones:

- Reducir el recuento de la instancia a 0. Tardará unos 30 minutos para desasignar las instancias.
- Seleccione el nuevo tamaño de cálculo y el número de instancias. Desde aquí, se tardará entre 2 a 3 horas en completarse.

Si las aplicaciones requieren un tamaño mayor de recursos computacionales, no pueden aprovechar la orientación anterior. En lugar de cambiar el tamaño del grupo de trabajo que aloja las aplicaciones, puede llenar otro grupo de trabajo con trabajadores del tamaño deseado y mover las aplicaciones a ese grupo.

- Crear las instancias adicionales del tamaño necesario compute en otro grupo de trabajo. Esto le llevará de 2 a 3 horas en completarse.
- Reasignar los planes de servicio de la aplicación que alojan las aplicaciones que necesitan un tamaño mayor para el grupo de trabajo recién configurado. Esto es una operación rápida que debería tardar menos de un minuto en completarse.  
- Escalar el primer grupo de trabajo si ya no necesita esas instancias no utilizadas. Esta operación tarda unos 30 minutos en completarse.

**Ajuste automático**: una de las herramientas que pueden ayudarle a administrar el consumo de recursos de cálculo es de ajuste automático. Puede utilizar el ajuste automático para el front-end o grupos de trabajo. Puede hacer cosas como el aumento de las instancias de cualquier tipo de grupo en la mañana y reducirlo en la noche. O tal vez puede agregar instancias cuando el número de trabajadores que están disponibles en un grupo de trabajo cae por debajo de un umbral determinado.

Si desea establecer reglas autoscale alrededor de métricas de compute resource pool, a continuación, tenga en cuenta el tiempo que requiere provisioning. Para obtener más detalles acerca de ajuste automático en entornos de servicio de la aplicación, consulte [How to configure Autoescala en un entorno de servicio de la aplicación][ASEAutoscale].

### <a name="storage"></a>Almacenamiento de información

Cada ASE está configurado con 500 GB de almacenamiento. Este espacio se utiliza en todas las aplicaciones en la ASE. Este espacio de almacenamiento de información es una parte de la ASE y actualmente no se puede cambiar para utilizar el espacio de almacenamiento. Si está realizando ajustes para el enrutamiento de red virtual o la seguridad, necesita permitir el acceso al almacenamiento de Azure--todavía o no puede funcionar el ASE.

### <a name="database"></a>Base de datos

La base de datos contiene la información que define el entorno, así como los detalles acerca de las aplicaciones que se ejecutan en él. Lo cual también es una parte de la suscripción celebrada en Azure. No es algo que tiene una capacidad directa para manipular. Si está realizando ajustes para el enrutamiento de red virtual o la seguridad, necesita permitir el acceso a SQL Azure--todavía o no puede funcionar el ASE.

### <a name="network"></a>Red

El VNet que se utiliza con su ASE puede ser uno de los que realizó cuando creó el ASE o uno que ha realizado con anterioridad. Cuando se crea la subred durante la creación de ASE, fuerza la ASE en el mismo grupo de recursos como la red virtual. Si necesita el grupo de recursos utilizado por su ASE sea diferente de su VNet, debe crear su ASE mediante una plantilla de administrador de recursos.

Existen algunas restricciones de la red virtual que se utiliza para un ASE:
 
- La red virtual debe ser una red virtual regional.
- Debe haber una subred con 8 o más direcciones donde está implementada el ASE.
- Después de una subred se utiliza para alojar un ASE, no se puede cambiar el intervalo de direcciones de la subred. Por este motivo, se recomienda que la subred contiene por lo menos 64 direcciones para adaptarse a cualquier futuro crecimiento ASE.
- Puede haber nada más en la subred pero el ASE.

A diferencia del servicio hospedado que contiene la ASE, la [red virtual] [ virtualnetwork] y subred están bajo el control de usuario.  Puede administrar su red virtual a través de la interfaz de usuario de red Virtual o PowerShell.  Un ASE puede implementarse en un clásico o VNet del Administrador de recursos.  El portal y experiencias de la API son ligeramente diferentes entre clásico y el Administrador de recursos VNets, pero la experiencia ASE es el mismo.

El VNet que se utiliza para alojar un ASE puede utilizar ambos direcciones RFC1918 IP privadas o puede utilizar direcciones IP públicas.  Si desea utilizar un intervalo IP que no está cubierto por RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), a continuación, debe crear su VNet y subred para ser utilizado por su ASE por delante de la creación de ASE.

Debido a esta capacidad coloca el servicio de aplicación de Azure en la red virtual, significa que las aplicaciones que se hospedan en su ASE ahora pueden acceder a recursos que están disponibles a través de ExpressRoute o redes privadas virtuales (VPN) de sitio a sitio directamente. Las aplicaciones que están dentro de su entorno de servicio de la aplicación no requieren características de red adicionales para tener acceso a los recursos disponibles para la red virtual que aloja el entorno del servicio de la aplicación. Esto significa que no es necesario utilizar VNET integración o conexiones híbrido para obtener recursos en o conectado a la red virtual. Todavía puede utilizar dos de esas características aunque tenga acceso a recursos en redes que no están conectadas a la red virtual.

Por ejemplo, puede utilizar VNET integración integrar con una red virtual que está en su suscripción, pero no está conectada a la red virtual que se encuentra en su ASE. Todavía también puede utilizar conexiones híbridas para tener acceso a los recursos que se encuentran en otras redes, como ocurre normalmente.  

Si tiene su red virtual configurado con una VPN ExpressRoute, debe tener en cuenta algunas de las necesidades de enrutamiento que tiene un ASE. Hay algunas configuraciones de ruta definidos por el usuario (UDR) que son incompatibles con un ASE. Para obtener más detalles acerca de cómo ejecutar un ASE en una red virtual con ExpressRoute, consulte [ejecutar un entorno de servicio de la aplicación en una red virtual con ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Proteger el tráfico entrante

Existen dos métodos principales para controlar el tráfico entrante a su ASE.  Groups(NSGs) de seguridad de red puede utilizar para controlar qué IP direcciones pueden tener acceso a su ASE como se describe aquí [cómo controlar el tráfico entrante en un entorno de servicio de la aplicación](app-service-app-service-environment-control-inbound-traffic.md) y también puede configurar su ASE con un Balancer(ILB) de carga interno.  Estas características también pueden utilizarse conjuntamente si desea restringir el acceso con NSGs a su ASE de ILB.

Cuando se crea un ASE, creará a una VIP en su VNet.  Hay dos tipos de VIP, internos y externos.  Cuando se crea un ASE con una VIP externo sus aplicaciones en su ASE será accesibles mediante una dirección IP enrutable de internet. Al seleccionar interno su ASE se configurarán con una DCI y no estará directamente accesible de internet.  Un ASE DCI aún requiere a una VIP externo, pero sólo se utiliza para el acceso de administración y mantenimiento de Azure.  

Durante la creación de DCI ASE proporcionan el subdominio utilizado por el ASE DCI y tendrá que administrar su propio DNS para el subdominio que especifique.  Dado que se establece el nombre del subdominio debe administrar el certificado usado para acceso HTTPS.  Después de la creación de ASE deberá proporcionar el certificado.  Para obtener más información acerca de la creación y uso de un ASE DCI leen [mediante un equilibrador de carga interno con un entorno de servicio de la aplicación][ILBASE]. 


## <a name="portal"></a>Portal

Puede administrar y supervisar el entorno de la aplicación del servicio mediante la interfaz de usuario en el portal de Azure. Si tienes un ASE, entonces es probable que vea el símbolo de servicio de la aplicación en la barra lateral. Este símbolo se utiliza para representar entornos de servicio de la aplicación en el portal de Azure:

![Símbolo del entorno de servicio de la aplicación][1]

Para abrir la interfaz de usuario que enumera todos los entornos de servicio de la aplicación, puede utilizar el icono o seleccione las comillas angulares (">" símbolo) en la parte inferior de la barra lateral para seleccionar entornos de servicio de la aplicación. Al seleccionar uno de lo ASEs aparece, abra la interfaz de usuario que se utiliza para supervisar y administrar.

![Interfaz de usuario para supervisar y administrar su entorno de servicio de la aplicación][2]

Esta primera hoja muestra algunas propiedades de su ASE, junto con un gráfico de métrica por cada grupo de recursos. Algunas de las propiedades que se muestran en el bloque de **Essentials** también son hipervínculos que va a abrir la hoja en la que está asociada a ella. Por ejemplo, puede seleccionar el nombre de **Red Virtual** para abrir la interfaz de usuario asociada con la red virtual que está ejecutando su ASE en. **Planes de servicio de la aplicación** y **las aplicaciones** se abren módulos que muestran estos elementos en su ASE.  

### <a name="monitoring"></a>Supervisión

Los gráficos permiten ver una gran variedad de métricas de performance en cada grupo de recursos. Para el grupo de aplicaciones para el usuario, puede supervisar la CPU y la memoria promedio. Para grupos de trabajo, puede supervisar la cantidad que se utiliza y la cantidad que está disponible.

Servicio App múltiples planes para hacer uso de los trabajadores de un grupo de trabajo. La carga no se distribuye de la misma manera como con los servidores de aplicaciones, por lo que el uso de la CPU y la memoria no proporcionan un alto nivel de información útil. Es más importante realizar un seguimiento de cuántos trabajadores que ha utilizado y están disponibles, especialmente si está administrando este sistema para otros usuarios.  

También puede utilizar todas las métricas que pueden realizar un seguimiento de los planes para configurar alertas. Configurar alertas aquí funciona igual como en otras partes en el servicio de la aplicación. Puede establecer una alerta en la parte de interfaz de usuario de **alertas** o de perforación en cualquier interfaz de usuario de métricas y seleccionando **Agregar alerta**.

![Métrica de interfaz de usuario][3]

Las métricas que acabamos de analizar son las métricas de entorno de servicio de la aplicación. También son métricas que están disponibles en el nivel del plan de servicio de la aplicación. Aquí es donde la supervisión de la CPU y la memoria hace mucho sentido.

En un ASE, todos los planes de servicio de la aplicación son los planes de servicio de la aplicación dedicados. Esto significa que las aplicaciones sólo a los que se ejecutan en los hosts asignados a que el plan de servicio de la aplicación son las aplicaciones de ese plan de servicio de la aplicación. Para ver los detalles de su plan de servicio de la aplicación, traiga su plan de servicio de la aplicación de cualquiera de las listas en la interfaz de usuario de ASE o de **planes de servicio de la aplicación vaya** (que se enumeran todas ellas).   

### <a name="settings"></a>Configuración

Dentro de la hoja de ASE, hay una sección de **configuración** que contiene varias funciones importantes:

**Configuración de** > **Propiedades**: la hoja de **configuración** se abre automáticamente cuando se coloca la placa ASE. En la parte superior es **Propiedades**. Hay una serie de artículos aquí que son redundantes en lo que se ve en **Essentials**, pero lo que es muy útil es la **Dirección IP Virtual**, así como **Direcciones IP saliente**.

![Hoja de configuración y propiedades][4]

**Configuración de** > **Direcciones IP**: cuando se crea una aplicación IP Secure Sockets Layer (SSL) en su ASE, necesita una dirección IP SSL. Para obtenerlo, su ASE necesita direcciones IP SSL que posee y que se pueden asignar. Cuando se crea un ASE, tiene una dirección IP SSL para este propósito, pero puede agregar más. Hay un cargo para direcciones adicionales SSL IP, como se muestra en [La aplicación de tarifas de servicio] [ AppServicePricing] (en la sección sobre conexiones SSL). El precio adicional es la IP SSL.

**Configuración de** > **Grupo de servidores frontales** / **Grupos de trabajo**: cada uno de estos módulos de agrupación de recursos ofrece la posibilidad de ver información sólo sobre ese fondo de recursos, además de proporcionar controles para escalar completamente ese fondo de recursos.  

La placa base para cada grupo de recursos proporciona un gráfico con métricas de ese grupo de recursos. Al igual que con los gráficos de la escobilla ASE, puede ir al gráfico y configurar alertas como desee. Configuración de una alerta de la escobilla ASE de un fondo de recursos específicos, hace lo mismo que hacerlo desde el fondo de recursos. En la hoja de **configuración de** grupo de trabajo, tendrá acceso a todas las aplicaciones o planes de servicio de la aplicación que se ejecutan en este grupo de trabajo.

![Interfaz de usuario de configuración de la agrupación de trabajo][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala Portal  

Hay tres operaciones de escala:

- Cambiar el número de direcciones IP en el ASE que están disponibles para el uso de SSL IP.
- Cambiar el tamaño del recurso de cálculo que se usa en un fondo de recursos.
- Cambiar el número de recursos de cálculo que se utilizan en un fondo de recursos, ya sea manualmente o a través de ajuste automático.

En el portal, existen tres maneras de controlar cuántos servidores hay en los grupos de recursos:

- Una operación de escala de la hoja de ASE principal en la parte superior. Puede hacer cambios en la configuración de escala de varios grupos de aplicaciones para usuario y trabajo. Todos se aplican como una sola operación.
- Una operación manual de escala de la hoja de **escala** grupo de recurso individual, que se encuentra en **configuración**.
- Ajuste automático, que se configura desde el blade de **escala** de grupo de recursos individuales.

Para utilizar la operación de escala en el blade ASE, arrastre el control deslizante a la cantidad que desee y guarde. Esta interfaz de usuario también permite cambiar el tamaño.  

![Escala de interfaz de usuario][6]

Para utilizar la funcionalidad manual o ajustar automáticamente en un fondo de recursos específicos, vaya a **configuración de** > **Grupo de servidores frontales** / **Grupos de trabajo** según corresponda. A continuación, abra el grupo que desea cambiar. Vaya a **configuración de** > **escalada** o **configuración** > **escalar**. El módulo de **Ampliación en horizontal** permite controlar la cantidad de la instancia. **Scale Up** permite controlar el tamaño del recurso.  

![Configuración de escala de interfaz de usuario][7]

## <a name="fault-tolerance-considerations"></a>Consideraciones de tolerancia a errores

Puede configurar un entorno de servicio de la aplicación para utilizar los recursos de cálculo total hasta 55. De los recursos computacionales 55, 50 sólo puede utilizarse para cargas de trabajo de host. La razón de esto es doble. No hay un mínimo de 2 recursos de cálculo front-end.  Que deja hasta 53 para admitir la asignación de grupo de trabajo. Para proporcionar tolerancia a errores, debe tener un recurso de cálculo adicional que se asigna según las siguientes reglas:

- Cada grupo de trabajo necesita al menos 1 recurso de cálculo adicional que no está disponible para ser asignado a una carga de trabajo.
- Cuando la cantidad de recursos de proceso en un grupo de trabajo supera un determinado valor, otro recurso de cálculo es necesaria para la tolerancia a errores. No es el caso en el grupo de aplicaciones para el usuario.

Dentro de un grupo de trabajo, los requisitos de tolerancia a errores son para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si X está entre 2 y 20, la cantidad de recursos de cálculo utilizable que puede utilizar para cargas de trabajo es X-1.
- Si X es entre 21 y 40, la cantidad de recursos de cálculo utilizable que puede utilizar para cargas de trabajo es X-2.
- Si X está entre 41 y 53, la cantidad de recursos de cálculo utilizable que puede utilizar para cargas de trabajo es X-3.

El espacio mínimo tiene 2 servidores front-end y 2 trabajadores.  Con las declaraciones anteriores a continuación, presentamos algunos ejemplos para aclarar:  

- Si tiene 30 trabajadores en un solo grupo, 28 de ellos puede utilizarse para cargas de trabajo de host.
- Si tiene 2 trabajadores en un solo grupo, 1 puede utilizarse para cargas de trabajo de host.
- Si tiene 20 trabajadores en un solo grupo, 19 puede utilizarse para cargas de trabajo de host.  
- Si tienes 21 trabajadores en un solo grupo, aún después de 19 sólo puede utilizarse para cargas de trabajo de host.  

El aspecto de la tolerancia a errores es importante, pero debe mantenerse en mente como escalar por encima de determinados umbrales. Si desea agregar más capacidad de 20 instancias, a continuación, ir a 22 o superior porque 21 no agrega ninguna más capacidad. Lo mismo es válido pasar por encima de 40, donde el número siguiente que agrega capacidad es 42.  

## <a name="deleting-an-app-service-environment"></a>Eliminación de un entorno de servicio de la aplicación ##

Si desea eliminar un entorno de servicio de la aplicación, basta con utilizar la acción **Eliminar** en la parte superior de la hoja del entorno de servicio de la aplicación. Al hacerlo, pedirá que escriba el nombre del entorno de servicio de la aplicación para confirmar que realmente desea hacer esto. Tenga en cuenta que al eliminar un entorno de servicio de la aplicación, elimine todo el contenido dentro de él.  

![Eliminar un entorno de servicio de la aplicación de interfaz de usuario][9]  

## <a name="getting-started"></a>Introducción

Para comenzar con entornos de servicio de la aplicación, vea [cómo crear un entorno de servicio de la aplicación](app-service-web-how-to-create-an-app-service-environment.md).

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
