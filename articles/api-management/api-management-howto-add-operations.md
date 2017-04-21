<properties 
    pageTitle="Cómo agregar operaciones a una API de administración de la API de Azure | Microsoft Azure" 
    description="Aprenda a agregar operaciones a una API de administración de la API de Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Cómo agregar operaciones a una API de administración de la API de Azure

Antes de que se puede utilizar una API en la API de administración, deben agregarse las operaciones. Esta guía muestra cómo agregar y configurar los distintos tipos de operaciones a una API en la API de administración.

## <a name="add-operation"> </a>Agregar una operación

Las operaciones se agrega y configura una API en el portal de publisher. Para acceder al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico para el servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia del servicio de administración de API][] en el tutorial para [comenzar con la administración de la API de Azure][] .

Seleccione la API deseada en el portal de publisher y, a continuación, seleccione la ficha **operaciones** . 

![Operaciones][api-management-operations]

Haga clic en la **Operación de agregar** para agregar una nueva operación. La **nueva operación** se mostrará y la ficha **firma** se seleccionará de forma predeterminada.

![Agregar operación][api-management-add-operation]

Especifique el **verbo HTTP** , elija en la lista desplegable.

![Método HTTP][api-management-http-method]

<a name="url-template"></a>

Definir la plantilla de dirección URL escribiendo en un fragmento de dirección URL que consta de uno o más segmentos de ruta de acceso de dirección URL y cero o más parámetros de cadena de consulta. La plantilla de dirección URL, anexada a la dirección URL base de la API, identifica una única operación de HTTP. Pueden contener una o más partes variables que se identifican mediante llaves con nombre. Estas partes variables se denominan parámetros de plantilla y se les asignan dinámicamente valores extraídos desde la dirección URL de la solicitud cuando se procesa la solicitud por la plataforma de administración de la API.

![Plantilla de dirección URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Si lo desea, especifique la **plantilla de la reescritura de URL**. Esto permite utilizar la plantilla de dirección URL estándar para procesar las solicitudes entrantes en el front-end, al llamar al back-end mediante una dirección URL convertida según la plantilla de reescritura. Parámetros de plantilla desde la plantilla de dirección URL se utilizarán en la plantilla de reescritura. En el ejemplo siguiente se muestra cómo contenido tipo codificado como segmento de ruta de acceso en el servicio web del ejemplo anterior puede proporcionarse como parámetro de consulta en la API publicada a través de la plataforma de administración de la API con las plantillas de dirección URL.

![Reescritura de la plantilla de dirección URL][api-management-url-template-rewrite]

Los llamadores a la operación utilizará el formato de `/customers?customerid=ALFKI` y éste se asignará a `/Customers('ALFKI')` cuando se invoque el servicio back-end.


Nombre para **Mostrar** y la **Descripción** proporcionan una descripción de la operación y se utilizan para proporcionar documentación para los desarrolladores que usan esta API en el portal del programador.

![Descripción][api-management-description]

La descripción de la operación puede especificarse como texto sin formato o HTML en el cuadro de texto **Descripción** .

## <a name="operation-caching"> </a>Operación de almacenamiento en caché

Almacenamiento en caché de respuesta reduce la latencia percibida por los consumidores de API, disminuye el consumo de ancho de banda y reduce la carga en la web HTTP servicio de implementación de la API. 

Para fácil y rápidamente Habilitar almacenamiento en caché para la operación, seleccione la ficha **caché** y marque la casilla **Habilitar** .

![Almacenamiento en caché][api-management-caching-tab]

**Duración** especifica el período de tiempo durante el cual la respuesta de la operación permanece en la caché. El valor predeterminado es 3.600 segundos o 1 hora.

Claves de caché se utilizan para diferenciar entre las respuestas para que obtenga su propio valor independiente en caché la respuesta correspondiente a cada clave de caché diferente. Opcionalmente, escriba parámetros de cadena de consulta específica o los encabezados HTTP que se utilizará en informática valores de clave de caché en los cuadros de texto **variar por parámetros de cadena de consulta** y **variar por encabezados** respectivamente. Cuando ninguno es la dirección URL especificada, completa la solicitud y se utilizan los siguientes valores de encabezado HTTP en la generación de claves de caché: **Accept** and **Accept-Charset**.

>Para obtener más información sobre el almacenamiento en caché y el almacenamiento en caché de directivas, consulte [cómo resultados de la operación de caché en la administración de la API de Azure][].


## <a name="request-parameters"> </a>Parámetros de la petición

Parámetros de operación se administran en la ficha parámetros. Los parámetros especificados en la **Plantilla de dirección URL** en la ficha **firma** se agregan automáticamente y se pueden cambiar mediante la edición de la plantilla de dirección URL. Parámetros adicionales pueden escribirse manualmente.

Para agregar un nuevo parámetro de consulta, haga clic en **Agregar el parámetro de consulta** e introduzca la siguiente información:

-   **Nombre** : nombre del parámetro.
-   **Descripción** : una descripción breve del parámetro (opcional).
-   **Tipo** : tipo de parámetro, seleccionado en la lista hacia abajo.
-   **Valores** : valores que pueden asignarse a este parámetro. Uno de los valores puede marcarse como valor predeterminado (opcional).
-   **Requerido** - que el parámetro sea obligatoria activando la casilla de verificación. 

![Parámetros de la petición][api-management-request-parameters]

## <a name="request-body"> </a>Cuerpo de la solicitud

Si se permite la operación (por ejemplo, PUT, POST) y requiere un cuerpo puede proporcionar un ejemplo de la misma en todos los formatos de representación compatible (por ejemplo, json, XML). 

>El cuerpo de la solicitud se utiliza sólo con fines de documentación y no se valida.

Para introducir un cuerpo de la solicitud, cambie a la ficha **cuerpo** .

Haga clic en **Agregar representación**, comience a escribir el nombre de tipo de contenido deseado (por ejemplo, application/json), selecciónelo en la lista desplegable y pegue el ejemplo de cuerpo de solicitud deseada en el formato seleccionado en el cuadro de texto. 

![Cuerpo de la solicitud][api-management-request-body]

En adicional para las representaciones, también puede especificar una descripción de texto opcional en el cuadro de texto **Descripción** .

## <a name="responses"> </a>Las respuestas

Es una buena práctica para proporcionar ejemplos de respuestas para todos los códigos de estado que puede producir la operación. Cada código de estado puede tener más de un ejemplo de cuerpo de respuesta, uno para cada uno de los tipos de contenido admitidos. 

Para agregar una respuesta, haga clic en **Agregar** y comience a escribir el código de estado deseado. En este ejemplo es el código de estado **200 OK**. Una vez que el código se muestra en la lista desplegable, selecciónela, y el código de respuesta y se agrega a la operación.

![Código de respuesta][api-management-response-code]

Haga clic en **Agregar representación**, comience a escribir el nombre de tipo de contenido deseado (por ejemplo, application/json) y a continuación, seleccione en la lista hacia abajo.

![Tipo de contenido del cuerpo][api-management-response-body-content-type]

Pegue el ejemplo de cuerpo de respuesta en el formato seleccionado en el cuadro de texto. 

![Cuerpo de la respuesta][api-management-response-body]

Si lo desea, agregue una descripción opcional en el cuadro de texto **Descripción** .

Una vez configurada la operación, haga clic en **Guardar**.


## <a name="next-steps"> </a>Próximos pasos

Una vez que las operaciones se agregan a una API, el siguiente paso es asociar la API con un producto y publicarlo para que los desarrolladores pueden llamar a sus operaciones.

-   [Cómo crear y publicar un producto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Comenzar con la administración de la API de Azure]: api-management-get-started.md
[Cree una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Cómo crear y publicar un producto]: api-management-howto-add-products.md
[Cómo almacenar en caché la operación da como resultado una administración de API de Azure]: api-management-howto-cache.md