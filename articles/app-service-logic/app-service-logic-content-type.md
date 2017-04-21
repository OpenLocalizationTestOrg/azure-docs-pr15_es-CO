<properties
   pageTitle="Aplicaciones de lógica contenidas escriba control | Microsoft Azure"
   description="Comprender cómo trata la lógica de aplicaciones con tipos de contenido en tiempo de ejecución y de diseño"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Control de tipo de contenido de aplicaciones de lógica

Hay muchos tipos diferentes de contenido que puede fluir a través de una aplicación lógica - incluidos los datos binarios, archivos sin formato, XML y JSON.  Aunque se admiten todos los tipos de contenido, algunos comprende de manera innata el motor de lógica de aplicaciones y otros puede que requieran conversión o conversiones según sea necesario.  El siguiente artículo describirá cómo el motor controla los diferentes tipos de contenido y cómo se pueden correctamente controlar según sea necesario.

## <a name="content-type-header"></a>Encabezado Content-Type

Para iniciar una sencilla, echemos un vistazo a los dos `Content-Types` que no requieren ninguna conversión o conversión a utilizar dentro de una aplicación lógica - `application/json` y `text/plain`.

### <a name="applicationjson"></a>Application/json

El motor de flujo de trabajo se basa en el `Content-Type` encabezado de HTTP se llama para determinar el tratamiento adecuado.  Cualquier solicitud con el tipo de contenido `application/json` se almacenará y se trata como un objeto JSON.  Además, se puede analizar contenido JSON por defecto sin necesidad de ninguna conversión.  Así que una solicitud que tenga el encabezado content-type `application/json ` como esta:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

se pudo analizar un flujo de trabajo con una expresión como `@body('myAction')['foo'][0]` para obtener un valor (en este caso, `bar`).  No es necesaria ninguna conversión adicional.  Si está trabajando con datos que es JSON, pero no tenían un encabezado especificado, manualmente puede convertir en JSON usando el `@json()` función (por ejemplo: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texto sin formato

Similar a `application/json`, mensajes HTTP recibidos con el `Content-Type` encabezado de `text/plain` se almacenará en su forma sin procesar.  Además, si incluye en una acciones posteriores sin ninguna conversión la solicitud saldrá con un `Content-Type`: `text/plain` encabezado.  Por ejemplo, si trabaja con un archivo plano puede aparecer el siguiente contenido HTTP:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Si en la siguiente acción envía como el cuerpo de otra solicitud (`@body('flatfile')`), la solicitud tendría un `text/plain` encabezado Content-Type.  Si está trabajando con datos que es texto sin formato, pero no tenían un encabezado especificado, manualmente puede convertir en texto utilizando la `@string()` función (por ejemplo: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplicación/xml y Application/octet-stream y funciones de convertidor

El motor de aplicación lógica mantendrá siempre la `Content-Type` que se recibió en la solicitud o respuesta HTTP.  Lo que esto significa es que si se recibe un contenido con `Content-Type` de `application/octet-stream`, que en una acción posterior con ninguna conversión resultará en una solicitud saliente con `Content-Type`: `application/octet-stream`.  De este modo, el motor pueden guaruntee no perderán datos mientras se desplaza por el flujo de trabajo.  Sin embargo, el estado de la acción (entradas y salidas) se almacenan en un objeto JSON que fluye a lo largo del flujo de trabajo.  Esto significa que para conservar algunos tipos de datos, el motor convertirá el contenido en una cadena binary base64 codificado con metadatos adecuados que conserva ambos `$content` y `$content-type` -que se convertirán automáticamente.  Puede convertir manualmente entre los tipos de contenido usando el convertidor funciones integradas:

* `@json()`-Convierte datos a`application/json`
* `@xml()`-Convierte datos a`application/xml`
* `@binary()`-Convierte datos a`application/octet-stream`
* `@string()`-Convierte datos a`text/plain`
* `@base64()`-Convierte el contenido en una cadena base64
* `@base64toString()`-Convierte una cadena codificada en base64 en`text/plain`
* `@base64toBinary()`-Convierte una cadena codificada en base64 en`application/octet-stream`
* `@encodeDataUri()`-Codifica una cadena como una matriz de bytes dataUri
* `@decodeDataUri()`-Descodifica un dataUri en una matriz de bytes

Por ejemplo, si recibe una solicitud HTTP con `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Pude convertir y utilizar posteriormente con algo como `@xml(triggerBody())`, o dentro de una función como `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Tipos de otro contenido

Otros tipos de contenido son compatibles y funcionan con una lógica de la aplicación, pero pueden requerir recuperar manualmente el cuerpo del mensaje mediante la descodificación de la `$content`.  Por ejemplo, si hubiese activación fuera de un `application/x-www-url-formencoded` solicitud que parecía lo siguiente:

```
CustomerName=Frank&Address=123+Avenue
```

Puesto que esto un no texto sin formato o JSON se almacenará en la acción como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Donde `$content` es la carga que se codifican como una cadena en base64 para conservar todos los datos.  Puesto que actualmente no hay una función nativa para formularios de datos, todavía podría utilizar estos datos dentro de un flujo de trabajo manualmente accediendo a los datos con una función como `@string(body('formdataAction'))`.  Si quería que mi solicitud saliente también tener la `application/x-www-url-formencoded` encabezado content-type, sólo tendría que agregarlo para el cuerpo de acción sin ninguna conversión como `@body('formdataAction')`.  Sin embargo, esto sólo funcionará si el cuerpo es el único parámetro en el `body` entrada.  Si lo intenta `@body('formdataAction')` dentro de un `application/json` solicitud obtendrá un error en tiempo de ejecución que enviará el cuerpo codificado.
