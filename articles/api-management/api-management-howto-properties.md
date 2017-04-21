<properties 
    pageTitle="Cómo utilizar propiedades de directivas de administración de la API de Azure" 
    description="Aprenda a utilizar las propiedades en las directivas de administración de la API de Azure." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Cómo utilizar propiedades de directivas de administración de la API de Azure

Políticas de administración de la API son una eficaz capacidad del sistema que permiten el publicador cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de instrucciones que se ejecutan de forma secuencial en la solicitud o la respuesta de una API. Instrucciones de directiva se pueden construir mediante propiedades, valores de texto literal y las expresiones de directiva. 

Cada instancia del servicio de administración de API tiene una colección de propiedades de pares de clave/valor que son globales para la instancia de servicio. Estas propiedades se pueden utilizar para administrar los valores de cadena constante en todas las directivas y configuración de la API. Cada propiedad tiene los siguientes atributos.


| Atributo | Tipo            | Descripción                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nombre      | cadena          | El nombre de la propiedad. Puede contener sólo letras, dígitos, período, guiones y caracteres de subrayado. |
| Valor     | cadena          | El valor de la propiedad. No puede estar vacío ni constan sólo de espacios en blanco.                           |
| Secreto    | valor booleano         | Determina si el valor es un secreto y se debe cifrar o no.                                |
| Etiquetas      | matriz de cadena | Etiquetas opcional que cuando proporciona puede utilizarse para filtrar la lista de propiedades.                               |

Propiedades se configuran en el portal de publisher en la ficha **Propiedades** . En el ejemplo siguiente, se configuran tres propiedades.

![Propiedades][api-management-properties]

Los valores de propiedad pueden contener cadenas de literales y [las expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx). En la tabla siguiente muestra las propiedades de tres muestra anteriores y sus atributos. El valor de `ExpressionProperty` es una expresión de directiva que devuelve una cadena que contiene la fecha y hora actuales. La propiedad `ContosoHeaderValue` está marcado como un secreto, así que no se muestra su valor.

| Nombre               | Valor                      | Secreto | Etiquetas    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | False  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | False  |         |

## <a name="to-use-a-property"></a>Para utilizar una propiedad

Para utilizar una propiedad en una directiva, coloque el nombre de propiedad dentro de un par de llaves como doble `{{ContosoHeader}}`, como se muestra en el ejemplo siguiente.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

En este ejemplo, `ContosoHeader` se utiliza como el nombre de un encabezado en un `set-header` directiva, y `ContosoHeaderValue` se utiliza como el valor de ese encabezado. Cuando esta directiva se evalúa durante una solicitud o una respuesta a la puerta de enlace API administración, `{{ContosoHeader}}` y `{{ContosoHeaderValue}}` se reemplazan con sus respectivos valores de propiedad.

Propiedades que pueden utilizarse como atributo completa o valores de elemento, como se muestra en el ejemplo anterior, pero se pueden también insertan o combinados con parte de una expresión de texto literal, tal como se muestra en el ejemplo siguiente:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propiedades también pueden contener expresiones de directiva. En el ejemplo siguiente, la `ExpressionProperty` se utiliza.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Cuando esta directiva se evalúa, `{{ExpressionProperty}}` se reemplaza por su valor: `@(DateTime.Now.ToString())`. Dado que el valor es una expresión de directiva, se evalúa la expresión y la directiva continúa con su ejecución.

Se puede comprobar en el portal del programador mediante una llamada a una operación que tiene una política con propiedades en el ámbito. En el ejemplo siguiente, se denomina una operación con el ejemplo anterior de dos `set-header` directivas con propiedades. Tenga en cuenta que la respuesta contiene dos encabezados personalizados que se configuraron mediante directivas con propiedades.

![Portal para desarrolladores][api-management-send-results]

Si mira el [API de Inspector seguimiento](api-management-howto-api-inspector.md) para una llamada que incluye las dos directivas de ejemplo anterior con las propiedades, puede ver los dos `set-header` directivas con los valores de propiedad insertados, así como la evaluación de la expresión de directiva para la propiedad que contiene la expresión de directiva.

![Traza de API Inspector][api-management-api-inspector-trace]

Tenga en cuenta que aunque los valores de propiedad pueden contener expresiones de directiva, los valores de propiedad no pueden contener otras propiedades. Si el texto que contiene una referencia de propiedad se utiliza como valor de una propiedad, `Property value text {{MyProperty}}`, esa referencia de propiedad no se reemplaza y se incluirán como parte del valor de la propiedad.

## <a name="to-create-a-property"></a>Para crear una propiedad

Para crear una propiedad, haga clic en **Agregar propiedad** en la ficha **Propiedades** .

![Agregar propiedad][api-management-properties-add-property-menu]

**Nombre** y **valor** son valores requeridos. Si el valor de esta propiedad es un secreto, compruebe que la casilla de verificación **Éste es un secreto** . Escriba una o más etiquetas opcionales para ayudar con las propiedades de la organización y haga clic en **Guardar**.

![Agregar propiedad][api-management-properties-add-property]

Cuando se guarda una nueva propiedad, el cuadro de **Propiedades de búsqueda de** texto se rellena con el nombre de la nueva propiedad y se muestra la nueva propiedad. Para mostrar todas las propiedades, desactive el cuadro de texto de **la propiedad de búsqueda** y presione ENTRAR.

![Propiedades][api-management-properties-property-saved]

Para obtener información sobre cómo crear una propiedad utilizando la API de REST, vea [crear una propiedad utilizando la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar una propiedad

Para editar una propiedad, haga clic en **Editar** junto a la propiedad para editar.

![Editar propiedad][api-management-properties-edit]

Realice los cambios necesarios y haga clic en **Guardar**. Si cambia el nombre de propiedad, las directivas que hacen referencia a esa propiedad se actualizan automáticamente para utilizar el nuevo nombre.

![Editar propiedad][api-management-properties-edit-property]

Para obtener información acerca de cómo modificar una propiedad utilizando la API de REST, consulte [Editar una propiedad utilizando la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para eliminar una propiedad

Para eliminar una propiedad, haga clic en **Eliminar** junto a la propiedad para eliminar.

![Eliminar propiedad][api-management-properties-delete]

Haga clic en **Sí, eliminarlo** para confirmar.

![Confirmar la eliminación][api-management-delete-confirm]

>[AZURE.IMPORTANT] Si la propiedad se hace referencia a ninguna de las directivas, podrá eliminarla correctamente hasta que se quite la propiedad de todas las directivas que lo utilizan.

Para obtener información acerca de cómo eliminar una propiedad utilizando la API de REST, vea [Eliminar una propiedad utilizando la API de REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para buscar y filtrar propiedades

La ficha **Propiedades** incluye la búsqueda y el filtrado de capacidades para ayudar a administrar sus propiedades. Para filtrar la lista de propiedades por nombre de propiedad, escriba un término de búsqueda en el cuadro de texto de **la propiedad de búsqueda** . Para mostrar todas las propiedades, desactive el cuadro de texto de **la propiedad de búsqueda** y presione ENTRAR.

![Búsqueda][api-management-properties-search]

Para filtrar la lista de propiedades por valores de etiqueta, escriba una o varias etiquetas en el cuadro de texto **Filtro por etiquetas** . Para mostrar todas las propiedades, desactive el textbox **filtrar por etiquetas** y presione ENTRAR.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Próximos pasos

-   Más información sobre cómo trabajar con directivas
    -   [Directivas de administración de la API](api-management-howto-policies.md)
    -   [Referencia de la directiva](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Vea un vídeo de introducción

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

