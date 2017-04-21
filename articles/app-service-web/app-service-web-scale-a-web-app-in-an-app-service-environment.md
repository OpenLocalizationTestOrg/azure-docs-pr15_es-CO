<properties 
    pageTitle="Cómo escalar una aplicación en un entorno de servicio de la aplicación" 
    description="Escalar una aplicación en un entorno de servicio de la aplicación" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Escalar aplicaciones en un entorno de servicio de la aplicación #

En el servicio de aplicación de Azure hay puede escalar normalmente tres cosas:

- plan de precios
- tamaño de trabajo 
- número de instancias.

En una ASE no hay para seleccionar o cambiar el plan de precios.  En términos de capacidades está ya en una prima de precio de nivel de capacidad.  

Con respecto a los tamaños de trabajador, el administrador ASE puede asignar el tamaño del recurso de cálculo que se utilizará para cada grupo de trabajo.  Que significa que puede tener 1 de grupo de trabajo con P4 calcula 2 de grupo de trabajo y recursos con los recursos computacionales de P1, si lo desea.  No tienen que estar en orden de tamaño.  Para obtener detalles alrededor de los tamaños y su precios vea aquí el documento [Tarifas de servicio de la aplicación de Azure][AppServicePricing].  Esto deja la opciones de escalado para aplicaciones web y planes de servicio de la aplicación en un entorno de servicio de la aplicación sea:

- selección de grupo de trabajo
- número de instancias

Si cambia cualquier elemento se realiza a través de la interfaz de usuario correspondiente se muestra para su ASE organizó planes de servicio de la aplicación.  

![][1]

No puede escalar más allá de la cantidad de recursos de cálculo disponibles en el grupo de trabajo que se encuentra la página ASP en la página ASP.  Si necesita calcular recursos de ese grupo de trabajo debe obtener el administrador ASE para agregarlos.  Para obtener información alrededor de volver a configurar su ASE lea la información aquí: [cómo configurar un entorno de servicio de la aplicación][HowtoConfigureASE].  También puede aprovechar las características de escala automática ASE para agregar capacidad en función de la programación o métricas.  Para obtener más detalles sobre la configuración de escala automática para el ASE propio entorno consulte [How to configure Autoescala para un entorno de servicio de la aplicación][ASEAutoscale].

Puede crear la aplicación varios planes de servicio utilizando los recursos de cálculo de grupos de trabajo diferentes, o puede utilizar el mismo grupo de trabajo.  Por ejemplo, si tiene (10) recursos de cálculo disponibles en 1 de grupo de trabajo, puede crear un plan de servicio de la aplicación utilizando los recursos de cálculo (6) y planear un segundo servicio de la aplicación que utiliza (4) de los recursos informáticos.

### <a name="scaling-the-number-of-instances"></a>Escala el número de instancias ###

Al crear la aplicación web en un entorno de servicio de la aplicación se inicia con la 1 instancia.  A continuación, se puede escalar a instancias adicionales para proporcionar recursos de cálculo adicionales para su aplicación.   

Si tu ASE tiene suficiente capacidad esto es bastante simple.  Vaya a su Plan de servicio de la aplicación que contiene los sitios que desea escalar y seleccione escala.  Se abre la interfaz de usuario, donde puede establecer la escala de la página ASP o configurar reglas de escala automática de la página ASP manualmente.  A escala manualmente la aplicación simplemente establece ***escala por*** a ***un recuento de la instancia que escriben manualmente***.  Desde aquí arrastre el control deslizante hasta la cantidad deseada o escríbalo en el cuadro situado junto a éste.  

![][2] 

Las reglas de escala automática de un ASP en un ASE funcionan igual como lo hacen normalmente.  Puede seleccionar el ***Porcentaje de CPU*** en ***escala por*** y crear reglas de escala automática para ASP basada en un porcentaje de la CPU, o puede crear reglas más complejas utilizando ***reglas de programación y rendimiento***.  Para ver información más detallada acerca de cómo configurar autoscale usar la guía aquí [escalar una aplicación de servicio de la aplicación de Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Selección de grupo de trabajo ###

Como se mencionó anteriormente, la selección de grupo de trabajo se accede desde la interfaz de usuario de ASP.  Abra la hoja de ASP que desea escalar y seleccione el grupo de trabajo.  Verá todos los grupos de trabajo que ha configurado en el entorno del servicio de la aplicación.  Si tiene sólo un trabajador grupo sólo verá el grupo de una lista.  Para cambiar qué grupo de trabajo se encuentra en la página ASP, simplemente seleccione el grupo de trabajo que desea que su Plan de servicio de la aplicación para mover a.  

![][3]

Antes de mover la página ASP grupo de trabajo de uno a otro es importante para asegurarse de que tendrá suficiente capacidad para el ASP.  En la lista de grupos de trabajo, no sólo se muestra el nombre del grupo de trabajo, pero también puede ver cuántos trabajadores están disponibles en ese grupo de trabajo.  Asegúrese de que hay instancias suficientes contener su Plan de servicio de la aplicación.  Si necesita calcular más recursos en el grupo de trabajo que se desea desplazar, a continuación, obtener el administrador ASE para agregarlos.  

> [AZURE.NOTE] Mover que un ASP del grupo de uno trabajo provocará frío inicia las aplicaciones en que ASP.  Esto puede provocar que las solicitudes se ejecute lentamente mientras su aplicación esté fría iniciado en los nuevos recursos computacionales.  El inicio en frío puede evitarse mediante el uso de la [aplicación caliente capacidad] [ AppWarmup] en el servicio de aplicación de Azure.  El módulo de inicialización de la aplicación descrito en el artículo también funciona para frío se inicia porque el proceso de inicialización se invoca también cuando las aplicaciones estén frías iniciado en nuevos recursos computacionales. 

## <a name="getting-started"></a>Introducción

Para comenzar con entornos de servicio de la aplicación, vea [Cómo crear una aplicación servicio entorno a][HowtoCreateASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, consulte el [Servicio de aplicación de Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
