<properties
    pageTitle="Información general detallada de planes de servicio de aplicación Azure | Microsoft Azure"
    description="Aprenda cómo planes de servicio de la aplicación para el trabajo de servicio de la aplicación de Azure y cómo se benefician de la experiencia de administración."
    keywords="App, azure app servicio, plan de servicio de aplicación escalable, escala, coste de los servicios de la aplicación"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Información general detallada de planes de servicio de aplicación Azure#

Un plan de servicio de la aplicación representa un conjunto de características y la capacidad que se puede compartir entre varias aplicaciones. Aplicaciones Web, aplicaciones móviles, aplicaciones de función o API de aplicaciones, en todos los [Servicios de Azure App](http://go.microsoft.com/fwlink/?LinkId=529714) se ejecutan en un plan de servicio de la aplicación. Estos planes admiten cinco niveles de precios: *libre*, *Shared*, *básico*, *estándar*y *Premium*. Cada nivel tiene sus propias posibilidades y la capacidad. Aplicaciones en la misma suscripción y ubicación geográfica pueden compartir un plan. Todas las aplicaciones que comparten un plan pueden utilizar todas las funciones y características que se definen en función del nivel del plan. Todas las aplicaciones que están asociadas con un plan de ejecución en los recursos que define el plan.

Por ejemplo, si el plan está configurado para utilizar dos instancias "pequeñas" en el nivel de servicio estándar, todas las aplicaciones que están asociadas con ese plan ejecutan en ambas instancias y tienen acceso a la funcionalidad de nivel de servicio estándar. Instancias de plan donde se ejecutan aplicaciones son altamente disponible y completamente administrado.

Este artículo explora las características clave, tales como el nivel y la escala de un plan de servicio de la aplicación y cómo entran en juego al administrar sus aplicaciones.

## <a name="apps-and-app-service-plans"></a>Aplicaciones y planes de servicio de la aplicación

Una aplicación de servicio de la aplicación puede asociarse a un único plan de servicio de la aplicación en un momento dado.

Aplicaciones y planes están contenidos en un grupo de recursos. Un grupo de recursos actúa como límite del ciclo de vida para cada recurso que está dentro de él. Puede utilizar grupos de recursos para administrar todas las partes de una aplicación de forma conjunta.

Como un único grupo de recursos puede tener varios planes de servicio de la aplicación, puede asignar diferentes aplicaciones a diferentes recursos físicos. Por ejemplo, puede separar los recursos entre entornos de desarrollo, prueba y producción. Tener entornos independientes para producción y prueba/desarrollo le permite aislar los recursos. De esta forma, pruebas de carga con una nueva versión de las aplicaciones no compiten por los mismos recursos que las aplicaciones de producción que están sirviendo a los clientes reales.

Cuando haya varios planes en un único grupo de recursos, también puede definir una aplicación que abarca las regiones geográficas. Por ejemplo, una aplicación de alta disponibilidad ejecutando en dos regiones incluye al menos dos planes, uno para cada región y una aplicación asociada con cada plan. En tal situación, todas las copias de la aplicación, a continuación, se encuentran en un único grupo de recursos. Con varios planes y varias aplicaciones de un grupo de recursos facilita la administrar, controlar y ver el estado de la aplicación.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Crear un plan de servicio de la aplicación o utilizar uno existente

Cuando se crea una aplicación, considere la posibilidad de crear un grupo de recursos. Por otra parte, si la aplicación que va a crear es un componente de una aplicación de mayor tamaño, esta aplicación debe crearse dentro del grupo de recursos que se asigna a esa aplicación de mayor tamaño.

Si la nueva aplicación es una aplicación totalmente nueva o parte de una más grande, puede elegir utilizar un plan de servicio de la aplicación existente para hospedarlo o crear uno nuevo. Esta decisión es más una cuestión de capacidad y la carga esperada.

Si esta nueva aplicación va a utilizar muchos recursos y han alojado diferentes factores de las otras aplicaciones de escala de un plano existente, recomendamos que se aislar en su propio plan.

Cuando se crea un plan, puede asignar un nuevo conjunto de recursos para la aplicación y tener un mayor control sobre la asignación de recursos porque cada plan obtiene su propio conjunto de instancias.

Ya puede mover aplicaciones a través de los planes, puede cambiar la forma en que se asignan los recursos a través de la aplicación más grande.

Finalmente, si desea crear una aplicación en una región distinta y esa región no tiene un plan existente, cree un plan de esa región para poder alojar su aplicación allí.

## <a name="create-an-app-service-plan"></a>Crear un plan de servicio de la aplicación

>[AZURE.TIP] Si tiene un entorno de servicio de la aplicación se puede consultar la documentación específica de la aplicación servicio de entornos aquí: [crear un Plan de servicio de la aplicación en un entorno de servicio de la aplicación](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Puede crear un plan de servicio de la aplicación vacío de la experiencia de examinar plan de servicio de la aplicación o como parte de la creación de la aplicación.

Haga clic en **nuevo**en el [portal de Azure](https://portal.azure.com), > **Web + mobile**y a continuación, seleccione **La aplicación Web** u otro tipo de aplicación de servicio de la aplicación.
![Crear una aplicación en el portal de Azure.][createWebApp]

A continuación, puede seleccionar o crear el plan de servicio de la aplicación para la nueva aplicación.

 ![Crear un plan de servicio de la aplicación.][createASP]

Para crear un nuevo plan de servicio de la aplicación, haga clic en **[+] Crear nueva**, escriba el nombre del **plan de servicio de la aplicación** y, a continuación, seleccione una **ubicación**de adecuada. Haga clic en **el nivel de precios**y, a continuación, seleccione un nivel de precios adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **libre** y **Shared**. Después de haber seleccionado el nivel de precios, haga clic en el botón **Seleccionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover una aplicación a un plan de servicio de aplicación diferente

Puede mover una aplicación a un plan de servicio diferente de la aplicación en el [portal de Azure](https://portal.azure.com). Puede mover aplicaciones entre planes como los planes están en el mismo grupo de recursos y la región geográfica.

Para mover una aplicación a otro plan, vaya a la aplicación que desea mover. En el menú de **configuración** , busque el **Plan de servicio de cambio de la aplicación**.

**Plan de servicio de cambio de la aplicación** abre el selector de **plan de servicio de la aplicación** . En este punto, puede elegir un plan existente o crear uno nuevo. Se muestran sólo los planes válidos (en el mismo grupo de recursos y la ubicación geográfica).

![Selector de plan de servicio de la aplicación.][change]

Cada plan tiene su propio nivel de precios. Por ejemplo, al mover un sitio desde un nivel libre a un nivel estándar, la aplicación ahora puede utilizar todas las características y recursos del nivel estándar.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar una aplicación a un plan de servicio de aplicación diferente
Si desea mover la aplicación a un área diferente, una alternativa es la aplicación de clonación. La clonación realiza una copia de su aplicación en un entorno de servicio de la aplicación en cualquier región o plan de servicio de la aplicación nuevo o existente.

 ![Clonar una aplicación.][appclone]

Puede encontrar el **Clon de la aplicación** en el menú **Herramientas** .

Clonación tiene algunas limitaciones que se pueden leer acerca de en el [servicio de aplicación de Azure App clonación mediante el portal de Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Un plan de servicio de aplicación de escala

Hay tres formas de adaptar un plan:

- **Nivel de precios de cambio del plan**. Por ejemplo, puede convertir un plan en el nivel básico hasta un nivel Standard o Premium, y todas las aplicaciones que están asociadas con ese plan ahora pueden utilizar las características que ofrece el nuevo nivel de servicio.
- **Cambiar el tamaño de la instancia del plan**. Por ejemplo, un plan en el nivel básico que utiliza instancias pequeñas puede cambiarse para utilizar instancias de gran tamaño. Todas las aplicaciones que están asociadas con ese plan ahora pueden utilizar la memoria adicional y recursos de la CPU que ofrece el mayor tamaño de la instancia.
- **Cambiar el número de instancias del plan**. Por ejemplo, un plan estándar que se ha escalado a tres instancias puede ampliarse a 10 instancias. Un plan avanzado puede ser escalado a 20 instancias (sujeto a disponibilidad). Todas las aplicaciones que están asociadas con ese plan ahora pueden utilizar la memoria adicional y recursos de la CPU que ofrece el mayor número de instancia.

Puede cambiar el tamaño de instancia y de nivel de precios haciendo clic en el elemento **De la escala** en configuración de la aplicación o el plan de servicio de la aplicación. Cambios aplican el plan de servicios de la aplicación y afecta a todas las aplicaciones que lo aloja.

 ![Establecer los valores para escalar una aplicación.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Liberador de espacio en el Plan de servicio de la aplicación
**Planes de servicio de la aplicación** con ninguna aplicación asociada a ellas que incurra en cargos puesto que siguen reservar la capacidad de cómputo configurada en las propiedades de escala del plan de servicio de la aplicación.
Para evitar gastos inesperados, cuando se elimina el último app hospedada en un plan de servicio de la aplicación, también se elimina el plan de servicio de la aplicación vacío resultante.


## <a name="summary"></a>Resumen

Planes de servicio de aplicación representan un conjunto de características y la capacidad que se puede compartir entre las aplicaciones. Planes de servicio de la aplicación ofrecen la flexibilidad para asignar aplicaciones específicas a un conjunto de recursos y optimizar aún más la utilización de los recursos de Azure. De este modo, si desea ahorrar dinero en su entorno de pruebas, puede compartir un plan en varias aplicaciones. También puede maximizar el rendimiento para el entorno de producción ajustando en múltiples regiones y planes.

## <a name="whats-changed"></a>¿Qué ha cambiado

* Para obtener una guía para el cambio de sitios Web al servicio de la aplicación, vea: [servicio de aplicación de Azure y su impacto en los servicios existentes de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png
