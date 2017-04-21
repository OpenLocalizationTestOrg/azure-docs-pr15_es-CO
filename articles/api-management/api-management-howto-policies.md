<properties 
    pageTitle="Las directivas de administración de API de Azure | Microsoft Azure" 
    description="Aprenda a crear, editar y configurar las directivas de administración de la API." 
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


#<a name="policies-in-azure-api-management"></a>Directivas de administración de API de Azure

En administración de la API de Azure, las directivas son una eficaz capacidad del sistema que permiten el publicador cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de instrucciones que se ejecutan de forma secuencial en la solicitud o la respuesta de una API. Instrucciones populares incluyen la conversión de formato de XML a JSON y llamar a limitación para restringir la cantidad de llamadas entrantes de un programador de velocidad. Existen muchas más directivas fuera de la caja.

Vea la [Referencia de la directiva][] para obtener una lista completa de las instrucciones de directiva y su configuración.

Las directivas se aplican dentro de la puerta de enlace que se sitúa entre el consumidor de API y la API administrada. La puerta de enlace recibe todas las solicitudes y las envía normalmente sin modificaciones al API subyacente. Sin embargo una directiva puede aplicar cambios a la petición de entrada y la respuesta saliente.

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de administración de la API, a menos que se especifique lo contrario en la directiva. Algunas directivas, como las directivas de [flujo de Control][] y [establezca la variable][] se basan en las expresiones de directiva. Para obtener más información, vea [Directivas avanzadas][] y [las expresiones de directiva][].

## <a name="scopes"> </a>Cómo configurar las directivas
Directivas pueden configurarse globalmente o en el ámbito de un [producto][], la [API][] o la [operación][]. Para configurar una directiva, vaya al editor de directivas en el portal de publisher.

![Menú directivas][policies-menu]

El editor de directivas se compone de tres secciones principales: el ámbito de la directiva (arriba), la definición de la directiva que edite directivas (izquierda) y las instrucciones (derecha) de la lista:

![Editor de directivas][policies-editor]

Para empezar a configurar una directiva primero debe seleccionar el ámbito en el que debe aplicarse la directiva. En el screenshot de abajo del **motor de arranque** se selecciona el producto. Tenga en cuenta que el símbolo cuadrado situado junto al nombre de directiva indica que ya se ha aplicado una directiva en este nivel.

![Ámbito de aplicación][policies-scope]

Puesto que ya se ha aplicado una directiva, la configuración se muestra en la vista de la definición.

![Configurar][policies-configure]

La directiva se muestra como de sólo lectura al principio. Para editar la definición, haga clic en la acción de **Configuración de la directiva** .

![Editar][policies-edit]

La definición de la directiva es un documento XML simple que describe una secuencia de instrucciones de entrada y de salida. El código XML puede editarse directamente en la ventana de definición. Se proporciona una lista de instrucciones a la derecha y declaraciones aplicables al ámbito actual están habilitados y resaltados; como se muestra en la instrucción de **Límite llamar a velocidad** en la captura de pantalla anterior.

Al hacer clic en una instrucción habilitada agregará el XML adecuado en la ubicación del cursor en la vista de la definición. 

>[AZURE.NOTE] Si no está habilitada la directiva que desea agregar, asegúrese de que está en el ámbito correcto para esa directiva. Cada declaración de directiva está diseñado para su uso en determinados ámbitos y secciones de directiva. Para revisar las secciones de directiva y los ámbitos de una directiva, consulte la sección **uso** de esa directiva en la [Directiva de referencia][].

Una lista completa de las instrucciones de directiva y su configuración está disponibles en la [Referencia de la directiva][].

Por ejemplo, para agregar una nueva instrucción para restringir las solicitudes entrantes a direcciones IP especificadas, coloque el cursor justo dentro del contenido de la `inbound` elemento XML y haga clic en la instrucción **Restrict llamador IPs** .

![Directivas de restricción de][policies-restrict]

Esto agregará un fragmento de código XML para la `inbound` elemento que proporciona instrucciones sobre cómo configurar la instrucción.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Para limitar las solicitudes entrantes y Aceptar sólo aquellos desde una dirección IP 1.2.3.4 modificar el XML como sigue:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Guardar][policies-save]

Cuando completan configurar las instrucciones de la directiva, haga clic en **Guardar** y los cambios se propagarán a la puerta de enlace API administración inmediatamente.

##<a name="sections"> </a>Configuración de la directiva de conocimiento

Una directiva es una serie de instrucciones que se ejecutan en orden para una solicitud y una respuesta. La configuración se divide correctamente en `inbound`, `backend`, `outbound`, y `on-error` secciones como se muestra en la siguiente configuración.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Si se produce un error durante el procesamiento de una solicitud, los restantes pasos de la `inbound`, `backend`, o `outbound` se pasan por alto las secciones y saltos de ejecución a las instrucciones en el `on-error` sección. Mediante la colocación de instrucciones de directiva en el `on-error` sección puede revisar el error mediante el uso de la `context.LastError` propiedad, inspeccionar y personalizar la respuesta de error utilizando la `set-body` directiva y configurar lo que ocurre si se produce un error. Existen códigos de error de pasos integrados y de errores que pueden producirse durante el procesamiento de instrucciones de directiva. Para obtener más información, vea [control de errores en las políticas de administración de la API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Dado que las directivas pueden especificarse en diferentes niveles (global, producto, api y operación) la configuración proporciona un método especificar el orden en que las instrucciones de la definición de la directiva ejecutan con respecto a la directiva principal. 

Los ámbitos de la política se evalúan en el orden siguiente.

1. Ámbito global
2. Ámbito del producto
3. Ámbito de la API
4. Ámbito de la operación

Se evalúan las instrucciones dentro de ellos según la posición de la `base` elemento, si está presente.

Por ejemplo, si tiene una directiva en el nivel global y una directiva configurada para una API, a continuación, siempre que se utilice dicha API particular ambas directivas se aplicará. Administración de API permite un orden determinista de instrucciones de directiva combinada a través del elemento base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

En la definición de la directiva de ejemplo anterior, el `cross-domain` instrucción se ejecutaría antes de ir seguida de las directivas más alta que a su vez, el `find-and-replace` directiva.

Si la misma directiva aparece dos veces en la instrucción de directiva, se aplica la directiva más recientemente evaluada. Puede utilizar esto para reemplazar las directivas que se definen en un ámbito mayor. Para ver las directivas en el ámbito actual en el editor de directivas, haga clic en **Recalcular la directiva efectiva del ámbito seleccionado**.

Tenga en cuenta que la directiva global carece de una directiva principal y usando el `<base>` el elemento en el no tiene ningún efecto. 

## <a name="next-steps"></a>Próximos pasos

Desproteger el siguiente vídeo en las expresiones de directiva.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referencia de la directiva]: api-management-policy-reference.md
[Producto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operación]: api-management-howto-add-operations.md

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Variable de conjunto]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
