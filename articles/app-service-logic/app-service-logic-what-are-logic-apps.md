<properties 
    pageTitle="¿Qué aplicaciones de lógica?" 
    description="Obtener más información acerca de la lógica de la aplicación servicio de aplicaciones" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>¿Qué aplicaciones de lógica?

Aplicaciones de lógica proporcionan una forma de simplificar e implementar integraciones escalables y flujos de trabajo en la nube. Proporciona un diseñador visual para modelar y automatizar el proceso como una serie de pasos que se conoce como un flujo de trabajo.  Hay [muchos conectores](../connectors/apis-list.md) a través de la nube y locales para integrar rápidamente a través de los servicios y protocolos.  Una aplicación lógica comienza con un desencadenador (like 'cuando se agrega una cuenta a Dynamics CRM') y después puede comenzar el desencadenamiento de muchas acciones de combinaciones, conversiones y condición lógica.

Las ventajas del uso de lógica de aplicaciones incluyen lo siguiente:  

- Ahorre tiempo al diseño de procesos complejos con fácil comprender las herramientas de diseño
- Implementación de patrones y flujos de trabajo sin problemas, que de lo contrario sería difícil de implementar en el código
- Introducción rápida desde plantillas
- Personalizar la lógica de la aplicación con su propia API, código y acciones personalizadas
- Conectar y sincronizar sistemas dispares entre local y la nube
- Construir fuera de BizTalk server, administración de API, funciones de Azure y Azure Service Bus con soporte de primera clase de integración

Lógica de aplicaciones es un iPaaS totalmente gestionado (integración de plataforma como servicio) permitiendo a los desarrolladores no tienen que preocuparse sobre la creación de alojamiento, escalabilidad, disponibilidad y administración.  Lógica de aplicaciones se escale automáticamente a satisfacer la demanda.

![Diseñador de flujo de la aplicación](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Como se mencionó, con aplicaciones de lógica, puede automatizar los procesos empresariales. Éstos son algunos ejemplos:  
 
* Mover archivos cargados en un servidor FTP en el almacenamiento de Azure
* Ruta y proceso de pedidos a través de los locales y los sistemas de nube
* Supervisar todos los tweets sobre un tema determinado, analizar la opinión y crear alertas y las tareas para los elementos que necesitan realizar su seguimiento.

Situaciones como éstas pueden configurarse desde el diseñador visual y sin escribir una sola línea de código. Obtener la [creación de la lógica de la aplicación ahora][create].  Una vez escrito - una aplicación lógica puede ser [implementado rápidamente y volver a configurar](app-service-logic-create-deploy-template.md) en múltiples entornos y regiones.

## <a name="why-logic-apps"></a>¿Por qué aplicaciones de lógica?

Lógica de aplicaciones ofrece velocidad y escalabilidad en el espacio de integración empresarial.  La facilidad de uso del diseñador, variedad de desencadenadores y acciones y eficaces herramientas de administración que centralizar su API más sencillas que nunca.  Como las empresas se mueven hacia la digitalización, lógica de aplicaciones le permite conectar dos sistemas heredados y de vanguardistas.

Además, con nuestra [Cuenta de integración de Enterprise] [ biztalk] puede escalar para madurar escenarios de integración con la potencia de una [mensajería XML][xml], [administración de socios comerciales][tpm]y mucho más.

- **Herramientas de diseño fáciles de usar** - Apps lógica puede ser diseñado end-to-end en el explorador o con las herramientas de Visual Studio. Inicie con un desencadenador, desde una simple programación para cuando se crea un problema de GitHub. A continuación, coordinar cualquier cantidad de acciones utilizando la Galería enriquecida de conectores.

- **API conectar fácilmente** -tareas de composición incluso fáciles de describir que son difíciles de implementar en el código. Lógica de aplicaciones facilita conectar sistemas dispares. ¿Desea conectar su nube solución para sus necesidades de marketing sistema de facturación? ¿Desea centralizar la mensajería a través de las API y los sistemas con un Bus de servicio empresarial? Aplicaciones de lógica son la forma más rápida y más confiable para ofrecer soluciones a estos problemas.

- **Comenzar rápidamente a partir de plantillas** : para ayudarle a empezar, le ofrecemos una [Galería de plantillas de] [ templates] que permiten crear rápidamente algunas soluciones comunes. Desde soluciones avanzadas de B2B para una conectividad sencilla SaaS y que incluso unas pocas son 'para divertirse' - la galería es la forma más rápida de empezar a trabajar con el poder de la lógica de aplicaciones.

- **Al horno de extensibilidad** - no ve el conector que necesita? Lógica de aplicaciones está diseñado para funcionar con su propia API y el código; puede crear fácilmente su propia API de la aplicación para utilizar como un conector personalizado o llamar a una [Función de Azure](https://functions.azure.com) para ejecutar fragmentos de código bajo demanda. 

- **Potencia de integración real** - iniciar fácil y crecer como sea necesario. Lógica de aplicaciones puede aprovechar fácilmente la potencia de BizTalk, industria líder integración la solución de Microsoft para habilitar profesionales de integración generar las soluciones que necesitan. Conozca más acerca del [Paquete de integración empresarial](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceptos de lógica de la aplicación

Las siguientes son algunas de las informaciones clave que conforman la experiencia de aplicaciones de lógica. 

- **Flujo de trabajo** - lógica de aplicaciones proporciona una forma gráfica para modelar los procesos de negocio como una serie de pasos o un flujo de trabajo.
- **Conectores managed** - su lógica de aplicaciones necesitan tener acceso a datos y servicios. Conectores administrados se crean específicamente para ayudarle cuando se conecta a y trabaja con los datos. Ver la lista de conectores disponibles ahora en [managed conectores][managedapis].
- **Desencadenadores** : algunos conectores administrado también puede actuar como un desencadenador. Un desencadenador inicia una nueva instancia de un flujo de trabajo basado en un evento determinado, como la llegada de un correo electrónico o un cambio en la cuenta de almacenamiento de Azure.
-  **Acciones** - cada paso después de llama a una acción del desencadenador en un flujo de trabajo. Normalmente, cada acción se asigna a una operación en su conector administrado o aplicaciones personalizadas de API.
- **Enterprise Integration Pack** - escenarios de integración más avanzados, lógica de aplicaciones incluye capacidades de BizTalk. BizTalk es la plataforma de integración de Microsoft industria líder. Los conectores de Enterprise Integration Pack permiten incluir fácilmente validación, transformación y más en sus flujos de trabajo de la lógica de la aplicación.

## <a name="getting-started"></a>Introducción  

- Para comenzar con la lógica de aplicaciones, siga el [crear una lógica de la aplicación] [ create] tutorial.  
- [Ver ejemplos y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Puede automatizar procesos de negocio con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Aprenda a integrar sus sistemas con las aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
