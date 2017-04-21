<properties 
    pageTitle="Cómo personalizar el portal de desarrolladores de Azure API administración utilizando plantillas | Microsoft Azure" 
    description="Aprenda a personalizar el portal de desarrolladores de Azure API administración utilizando plantillas." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Cómo personalizar el portal de desarrolladores de Azure API administración mediante plantillas

Administración de API Azure proporciona varias funciones de personalización que permiten a los administradores [Personalizar el aspecto del portal del desarrollador](api-management-customize-portal.md), así como personalizar el contenido de las páginas del portal desarrollador utilizando un conjunto de plantillas que configuran el contenido de las páginas. Mediante la sintaxis [DotLiquid](http://dotliquidmarkup.org/) y un conjunto de recursos de cadenas traducidos, iconos y controles de la página proporcionado, tiene gran flexibilidad para configurar el contenido de las páginas según su conveniencia, mediante estas plantillas.

## <a name="developer-portal-templates-overview"></a>Información general de plantillas del portal del desarrollador

Plantillas del portal de desarrolladores se administran en el portal del programador a los administradores de la instancia del servicio de administración de la API. Para administrar plantillas de desarrollador, desplácese hasta la instancia del servicio Administración de API en el Portal de Azure clásico y haga clic en **Examinar**.

![Portal para desarrolladores][api-management-browse]

Si ya están en el portal de publisher, puede acceder al portal de desarrolladores haciendo clic en el **portal para desarrolladores**.

![Menú portal del desarrollador][api-management-developer-portal-menu]

Para tener acceso a las plantillas del portal developer, haga clic en el icono de personalización de la izquierda para mostrar el menú de personalización y haga clic en **plantillas**.

![Plantillas del portal de desarrolladores][api-management-customize-menu]

La lista de plantillas muestra diversas categorías de plantillas que cubren las distintas páginas en el portal del programador. Cada plantilla es diferente, pero los pasos para editar y publicar los cambios son los mismos. Para modificar una plantilla, haga clic en el nombre de la plantilla.

![Plantillas del portal de desarrolladores][api-management-templates-menu]

Al hacer clic en una plantilla, le lleva a la página del portal de desarrolladores puede personalizar esa plantilla. En este ejemplo, la **lista de productos** se muestra la plantilla. La plantilla de **lista de producto** controla el área de la pantalla indicado por el rectángulo rojo. 

![Plantilla de lista de productos][api-management-developer-portal-templates-overview]

Algunas plantillas, como las plantillas de **Perfil de usuario** , personalizar las partes diferentes de la misma página. 

![Plantillas de perfil de usuario][api-management-user-profile-templates]

El editor para cada plantilla de developer portal tiene dos secciones que se muestran en la parte inferior de la página. La parte izquierda muestra el panel de edición de la plantilla y el lado derecho muestra el modelo de datos para la plantilla. 

El panel de edición de plantillas contiene el marcado que controla la apariencia y el comportamiento de la página correspondiente en el portal del programador. El marcado de la plantilla utiliza la sintaxis [DotLiquid](http://dotliquidmarkup.org/) . Un editor popular para DotLiquid es [DotLiquid para los diseñadores](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Se muestran los cambios realizados en la plantilla durante la edición en tiempo real en el explorador, pero no son visibles para los clientes hasta [Guardar](#to-save-a-template) y [Publicar](#to-publish-a-template) la plantilla.

![Formato de plantilla][api-management-template]

El panel de **datos de la plantilla** proporciona a una guía para el modelo de datos para las entidades que están disponibles para su uso en una plantilla determinada. Esta guía proporciona mostrando los datos que se muestran actualmente en el portal del programador. Puede ampliar los paneles de la plantilla haciendo clic en el rectángulo en la esquina superior derecha del panel de **datos de la plantilla** .

![Modelo de datos de la plantilla][api-management-template-data]

En el ejemplo anterior hay dos productos que se muestran en el portal del programador y que se han obtenido de los datos mostrados en el panel de **datos de la plantilla** , como se muestra en el ejemplo siguiente.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

El marcado de la plantilla de **lista de productos** procesa los datos para proporcionar los resultados deseados recorriendo en iteración la colección de productos para mostrar información y un vínculo a cada producto individual. Nota el `<search-control>` y `<page-control>` elementos del marcado. Controlan la visualización de la búsqueda y controles de la página de paginación. `ProductsStrings|PageTitleProducts`es una referencia de cadena adaptada que contiene el `h2` texto de encabezado de la página. Para obtener una lista de la cadena de recursos, controles de la página y los iconos disponibles para usar en plantillas del portal de desarrolladores, vea [referencia de plantillas del portal del programador de API de administración](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Guardar una plantilla

Para guardar una plantilla, haga clic en Guardar en el editor de plantillas.

![Guardar plantilla][api-management-save-template]

Cambios guardados no están activos en el portal del programador hasta que se publiquen.

## <a name="to-publish-a-template"></a>Para publicar una plantilla

Guardado plantillas se pueden publicar ya sea individualmente o todos juntos. Para publicar una plantilla de individual, haga clic en publicar en el editor de plantillas.

![Publicar plantilla][api-management-publish-template]

Haga clic en **Sí** para confirmar y realizar la plantilla activa en el portal del programador.

![Confirmar publicación][api-management-publish-template-confirm]

Para publicar todas las versiones de plantilla actualmente no publicados, haga clic en **Publicar** en la lista de plantillas. Plantillas no publicadas se indican mediante un asterisco después del nombre de la plantilla. En este ejemplo, se están publicando las plantillas de **lista de productos** y **productos** .

![Publicar plantillas][api-management-publish-templates]

Haga clic en **publicar personalizaciones** para confirmar.

![Confirmar publicación][api-management-publish-customizations]

Las plantillas recién publicadas son efectivas inmediatamente en el portal del programador.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para revertir una plantilla a la versión anterior

Para revertir una plantilla a la versión anterior publicada, haga clic en revertir en el editor de plantillas.

![Volver a plantilla][api-management-revert-template]

Haga clic en **Sí** para confirmar.

![Confirmar][api-management-revert-template-confirm]

La versión previamente publicada de una plantilla está activa en el portal del programador una vez completada la operación de reversión.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar una plantilla a la versión predeterminada

Restauración de plantillas a la versión predeterminada es un proceso de dos pasos. Primero deben restaurarse las plantillas y, a continuación, deben publicarse las versiones restauradas.

Para restaurar una única plantilla a la versión predeterminada, haga clic en restaurar en el editor de plantillas.

![Volver a plantilla][api-management-reset-template]

Haga clic en **Sí** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todas las plantillas de las versiones predeterminadas, haga clic en **Restaurar plantillas predeterminadas** en la lista de plantillas.

![Restauración de plantillas][api-management-restore-templates]

A continuación, se deben publicar las plantillas restauradas individualmente o todos a la vez siguiendo los pasos de [publicar una plantilla](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Referencia del programador de plantillas del portal

Para obtener información de referencia para el desarrollador de plantillas del portal, recursos de cadena, iconos y controles de la página, vea [referencia de plantillas del portal del programador de API de administración](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Vea un vídeo de introducción

Vea el siguiente vídeo para ver cómo agregar un panel de discusión y clasificación a las páginas de API y operación en el portal del programador utilizando plantillas.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







