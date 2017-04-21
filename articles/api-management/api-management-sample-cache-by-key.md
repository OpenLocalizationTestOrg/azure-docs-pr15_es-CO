<properties
    pageTitle="Caché personalizada en la gestión de la API de Azure"
    description="Obtenga información sobre cómo almacenar en caché los elementos clave en la gestión de la API de Azure"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>Caché personalizada en la gestión de la API de Azure
Servicio de administración de la API de Azure tiene compatibilidad integrada para [el almacenamiento en caché de la respuesta HTTP](api-management-howto-cache.md) utilizando la URL como clave. La clave puede ser modificada por los encabezados de solicitud utilizando la `vary-by` propiedades. Esto resulta útil para almacenar en caché las respuestas HTTP completas (también conocido como representaciones), pero a veces resulta útil para una parte de una representación de sólo caché. Las nuevas directivas de [caché valor de búsqueda](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) y el [valor del almacén de caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) proporcionan la capacidad de almacenar y recuperar fragmentos arbitrarios de datos desde dentro de definiciones de directiva. Esta capacidad también agrega valor a la directiva previamente introducidas [Enviar solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) porque ahora puede almacenar en caché las respuestas de servicios externos.

## <a name="architecture"></a>Arquitectura  
Servicio de administración de API utiliza una caché de datos compartidos por el arrendatario para que, a medida que escala hasta varias unidades aún obtendrá acceso a los mismos datos almacenados en caché. Sin embargo, cuando se trabaja con una implementación de múltiples regiones existen cachés independientes dentro de cada una de las regiones. Debido a esto, es importante no tratar la caché como un almacén de datos, que es la única fuente de algún dato de la información. Si lo hizo y más adelante decide aprovecharse de la implementación de múltiples regiones, los clientes con los usuarios que viajan pueden perder el acceso a los datos almacenados en caché.

## <a name="fragment-caching"></a>Caché de fragmentos
Hay algunos casos donde las respuestas que se devuelven contienen una parte de datos que son costoso determinar y aún permanece actualizada para una cantidad de tiempo razonable. Como ejemplo, considere un servicio creado por una compañía aérea que proporciona información sobre las reservas de vuelos, estado del vuelo, etcetera. Si el usuario es miembro del programa de puntos de las líneas aéreas, también tendría información relacionada con su estado actual y el kilometraje acumulado. Esta información relacionada con el usuario podría almacenarse en un sistema diferente, pero puede ser conveniente incluirlo en las respuestas devueltas sobre reservas y estado de vuelo. Esto puede hacerse mediante un proceso denominado almacenamiento en caché de fragmentos. Puede devolver la representación principal del servidor de origen mediante algún tipo de símbolo (token) para indicar donde se insertará la información relacionada con el usuario. 

Considere la siguiente respuesta JSON de una API de back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Y recursos secundarios en `/userprofile/{userid}` que parece,

     { "username" : "Bob Smith", "Status" : "Gold" }

Para determinar la información de usuario apropiado incluir, necesitamos identificar quién es el usuario final. Este mecanismo es depende de la implementación. Por ejemplo, estoy usando el `Subject` la afirmación de un `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Almacenamos esta `enduserid` valor en una variable de contexto para un uso posterior. El siguiente paso es determinar si una solicitud anterior ya recupera la información del usuario y se almacena en la caché. Para esto utilizamos la `cache-lookup-value` directiva.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Si no hay ninguna entrada en la caché que se corresponde con el valor de clave y, a continuación, n `userprofile` variable de contexto que se va a crear. Comprobamos que el éxito de la búsqueda utilizando la `choose` política de flujo de control.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Si el `userprofile` no existe la variable de contexto, a continuación, vamos a tener que hacer una solicitud HTTP a recuperarlo.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Usamos el `enduserid` para construir la dirección URL al recurso de perfil de usuario. Una vez que tenemos la respuesta, podemos extraer el texto del cuerpo de la respuesta y almacenarlo en una variable de contexto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Para evitar que tengamos que hacer esta solicitud HTTP otra vez, cuando el mismo usuario realiza otra solicitud, podemos almacenar el perfil de usuario en la caché.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Almacenamos el valor en la memoria caché utilizando la misma clave que hemos intentado recuperar con originalmente. La duración que se elija para almacenar el valor debe basarse en cómo los usuarios de cómo tolerante a errores y cambios de información suelen ser información al día. 

Es importante darse cuenta que recuperar desde la caché sigue siendo un fuera de proceso, la solicitud de red y potencialmente puede agregar decenas de milisegundos a la solicitud. Los beneficios incluyen al determinar que la información de perfil de usuario tarda mucho más que eso debido a que se necesitan en la base de las consultas o agregar información desde múltiples back-ends.

Es el paso final en el proceso Actualizar la respuesta devuelta con nuestra información de perfil de usuario.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Decidí incluir las comillas como parte del símbolo para que incluso cuando no se produce el reemplazo, la respuesta fue un JSON válido todavía. Esto fue principalmente facilitar la depuración.

Una vez que se combinan todos estos pasos juntos, el resultado final es una directiva que es similar a la siguiente.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Este enfoque de almacenamiento en caché se utiliza principalmente en los sitios web que se compone HTML en el servidor para que se puede representar como una sola página. Sin embargo, también puede ser útil en el lado de API donde los clientes no pueden hacer cliente almacenamiento en caché HTTP o es conveniente no colocar esa responsabilidad en el cliente.

Este mismo tipo de almacenamiento en caché también puede hacerse en los servidores web de back-end utilizando una Redis caching server, sin embargo, mediante el servicio de administración de la API para realizar este trabajo es útil cuando los fragmentos en caché proceden de diferentes back-ends de que las respuestas principales.

## <a name="transparent-versioning"></a>Control de versiones transparente
Es una práctica común para varias versiones de implementación diferente de una API compatibles en cualquier momento. Esta es tal vez para admitir diferentes entornos, como desarrollo, prueba, producción, etcetera, o puede ser para compatibilidad con versiones anteriores de la API para dar tiempo a los consumidores de la API migrar a las versiones más recientes. 

Un método para manejar esto en lugar de requerir a los desarrolladores del cliente cambiar las direcciones URL de `/v1/customers` a `/v2/customers` es almacenar datos del perfil del consumidor de qué versión de la API que actualmente desean utilizar y llamar a la dirección URL de back-end adecuados. Con el fin de determinar la URL correcta de back-end para llamar para un cliente en particular, es necesario consultar algunos datos de configuración. Al almacenar en caché estos datos de configuración, podemos minimizar la reducción del rendimiento de realizar esta búsqueda.

El primer paso es determinar el identificador que se utiliza para configurar la versión deseada. En este ejemplo, decidí asociar la versión a la clave de suscripción de producto. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

A continuación, hacemos una búsqueda en la caché para ver si ya hemos recuperado la versión de cliente que desee.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

A continuación, comprobamos si no encontramos en la caché.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Si no la encontramos, a continuación, nos vamos y recuperarlo.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extrae el texto de cuerpo de la respuesta de la respuesta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Para almacenarlo en la memoria caché para su uso futuro.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Y finalmente actualizar la dirección URL de back-end para seleccionar la versión del servicio deseado por el cliente.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

La directiva completo es el siguiente.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Los consumidores de API transparente controlar qué versión de back-end está teniendo acceso a los clientes sin tener que actualizar y volver a los clientes es una solución elegante que enfrenta numerosos problemas de control de versiones de API.

## <a name="tenant-isolation"></a>Aislamiento de inquilinos

En implementaciones de mayor tamaño multiempresa algunas empresas crean grupos independientes de los inquilinos en las distintas implementaciones de hardware de back-end. Esto minimiza el número de clientes que se ven afectados por un problema de hardware en el back-end. También permite nuevas versiones de software a implementarse en etapas. Lo ideal es que esta arquitectura back-end debe ser transparente para los consumidores de la API. Esto puede lograrse de manera similar al control de versiones transparente porque se basa en la misma técnica de manipulación de la dirección URL de back-end mediante el estado de configuración por clave API.  

En lugar de devolver una versión preferida de la API para cada clave de suscripción, debe devolver un identificador que un arrendatario se relaciona con el grupo de hardware asignado. Ese identificador puede utilizarse para construir la dirección URL de back-end adecuados.

## <a name="summary"></a>Resumen
La libertad para utilizar la caché de administración API de Azure para almacenar cualquier tipo de datos permite el acceso eficiente a los datos de configuración que pueden afectar a la forma en que se procesa una solicitud entrante. También puede utilizarse para almacenar fragmentos de datos que pueden aumentar las respuestas, devueltos por un API de back-end.

## <a name="next-steps"></a>Próximos pasos
Por favor, envíenos sus comentarios en el subproceso de Disqus para este tema si hay otras situaciones que han habilitado estas políticas para que usted, o si existen escenarios que le gustaría alcanzar, pero no siente no son actualmente posibles.
