<properties
   pageTitle="Aplicaciones de lógica como extremos invocables"
   description="Cómo crear y configurar extremos del desencadenador y utilizarlos en una aplicación lógica en el servicio de aplicación de Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>


# <a name="logic-apps-as-callable-endpoints"></a>Aplicaciones de lógica como extremos invocables

Lógica de aplicaciones de forma nativa puede exponer un extremo HTTP sincrónico como desencadenador.  También puede utilizar el modelo de extremos invocables para invocar la lógica de aplicaciones como un flujo de trabajo anidado a través de la acción de "flujo de trabajo" en la lógica de la aplicación.

Hay 3 tipos de desencadenadores que pueden recibir solicitudes:

* Solicitud
* ApiConnectionWebhook
* HttpWebhook

Para el resto del artículo, utilizaremos la **solicitud** como en el ejemplo, pero todos los principios se aplican idénticamente a los 2 tipos de desencadenadores.

## <a name="adding-a-trigger-to-your-definition"></a>Agregar un desencadenador a la definición de
El primer paso es agregar un desencadenador a la definición de la aplicación de lógica que puede recibir solicitudes entrantes.  Puede buscar en el Diseñador de "Solicitud de HTTP" agregar la ficha desencadenador. Puede definir un cuerpo de solicitud JSON esquema y el diseñador generará tokens para ayudarle a analizar y pasar datos desde el desencadenador manual a través del flujo de trabajo.  Recomiendo usar una herramienta como [jsonschema.net](http://jsonschema.net) para generar un esquema JSON de una carga de cuerpo del ejemplo.

![Solicitar tarjeta de desencadenador][2]

Después de guardar la definición de la lógica de la aplicación, se generará una dirección URL de devolución de llamada similar a esta:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Esta dirección URL contiene una clave SAS en los parámetros de consulta que se utiliza para la autenticación.

También puede obtener este extremo en el portal de Azure:

![][1]

O llamando al:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Seguridad para la dirección URL de desencadenador

Direcciones URL de devolución de llamada de lógica de la aplicación se generan de forma segura mediante una firma de acceso compartido.  La firma se pasa como parámetro de consulta y debe validarse antes de que se desencadenará la lógica de la aplicación.  Se genera mediante una combinación única de una clave secreta por la lógica de la aplicación, el nombre del desencadenador y la operación que se realiza.  A menos que alguien tiene acceso a la clave de aplicación lógica secreta, no podrían generar una firma válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Llamar a extremo del desencadenador de lógica de la aplicación

Una vez haya creado el extremo para el desencadenador, se puede activar a través de un `POST` a la dirección URL completa. Puede incluir encabezados adicionales y cualquier contenido en el cuerpo.

Si el tipo de contenido es `application/json` podrá a las propiedades de referencia desde dentro de la solicitud. De lo contrario, se tratará como una sola unidad binaria que se puede pasar a otras API pero no puede hacer referencia sin convertir el contenido dentro del flujo de trabajo.  Por ejemplo, si se pasa `application/xml` contenido podría utilizar `@xpath()` realizar una extracción de xpath, o `@json()` para convertir de XML a JSON.  Para obtener más información sobre cómo trabajar con contenido de tipos [se pueden encontrar aquí](app-service-logic-content-type.md)

Además, puede especificar un esquema JSON en la definición. Esto hace que el diseñador generar tokens que puede pasar en pasos.  Por ejemplo hará que el siguiente un `title` y `name` disponibles en el Diseñador de token:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Referencias al contenido de la solicitud entrante

El `@triggerOutputs()` función mostrará el contenido de la solicitud entrante. Por ejemplo, aparecería como:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Puede utilizar el `@triggerBody()` acceso directo para tener acceso a la `body` propiedad específicamente. 

## <a name="responding-to-the-request"></a>Responder a la solicitud

Para algunas peticiones que comienzan una lógica de la aplicación, desea responder con algún contenido al llamador. Hay un nuevo tipo de acción denominado **respuesta** que se puede utilizar para construir el código de estado, cuerpo y encabezados para la respuesta. Nota que, si no hay ninguna forma de **respuesta** está presente, el extremo de la aplicación lógica será *inmediatamente* responder con **202 aceptado**.

![Acción de respuesta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Las respuestas tienen lo siguiente:

| Propiedad | Descripción |
| -------- | ----------- |
| statusCode | El código de estado HTTP para responder a la solicitud entrante. Puede ser cualquier código de estado válido que comience por 2xx, 4xx o 5xx. no se permiten códigos de estado 3xx. | 
| cuerpo | Un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso binario contenido referenciado en un paso anterior. | 
| encabezados | Puede definir cualquier número de encabezados que se incluirán en la respuesta | 

Todos los pasos en la lógica de la aplicación que se requieren para la respuesta se deben completar dentro de *60 segundos* para la solicitud original para recibir la respuesta **a menos que el flujo de trabajo se llama como anidado la lógica de la aplicación**. Si no se alcanza ninguna acción de respuesta dentro de 60 segundos, a continuación, la solicitud entrante será el tiempo de espera y recibir una respuesta de **408 tiempo de espera de cliente** HTTP.  Para aplicaciones de lógica anidada, principal lógica de la aplicación continuará a esperar una respuesta hasta que se complete, independientemente de la cantidad de tiempo que se tarda.

## <a name="advanced-endpoint-configuration"></a>Configuración avanzada de extremo

Aplicaciones de lógica dispone de compatibilidad integrada para el extremo de acceso directo y utilizar siempre el `POST` método para iniciar una ejecución de la lógica de la aplicación. La aplicación de **Agente de escucha HTTP** API también admitía cambiar los segmentos de dirección URL y el método HTTP. Podría incluso configurar seguridad adicional o un dominio personalizado agregándolo al host de la aplicación API (la aplicación Web que hospeda la aplicación API). 

Esta funcionalidad está disponible a través de la **administración de la API**:
* [Cambiar el método de la solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Cambiar los segmentos de dirección URL de la solicitud](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar los dominios de administración de API en la ficha **Configurar** en el portal de Azure clásico
* Configurar la directiva para comprobar la autenticación básica (**necesita un enlace**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumen de migración desde Vista preliminar 2014-12-01

|  vista previa de 2014-12-01 | 2016-06-01 |
|---------------------|--------------------|
| Haga clic en la aplicación de **Agente de escucha HTTP** API | Haga clic en **desencadenador Manual** (sin app API requerido) |
| Establecer "*envía respuesta automáticamente*" agente de escucha HTTP | Bien incluir una acción de **respuesta** o no en la definición de flujo de trabajo |
| Configurar la autenticación básica o OAuth | mediante la administración de la API |
| Configurar el método HTTP | mediante la administración de la API |
| Configurar la ruta de acceso relativa | mediante la administración de la API |
| El cuerpo entrante a través de referencia`@triggerOutputs().body.Content` | Referencia a través de`@triggerOutputs().body` |
| Acción de **respuesta enviar HTTP** en el agente de escucha HTTP | Haga clic en **responder a la solicitud HTTP** (sin app API requerido)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
