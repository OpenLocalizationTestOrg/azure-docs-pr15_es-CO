<properties
    pageTitle="Cómo proteger un API Web de back-end con Azure Active Directory y administración de la API"
    description="Aprenda a proteger API Web back-end con Azure Active Directory y administración de la API." 
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

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Cómo proteger un API Web de back-end con Azure Active Directory y administración de la API

En el siguiente vídeo muestra cómo construir un API Web de back-end y proteger mediante el protocolo OAuth 2.0 con Azure Active Directory y administración de la API.  Este artículo proporciona una introducción e información adicional de los pasos en el vídeo. Este vídeo de 24 minutos muestra cómo para:

-   Generar un API Web de back-end y protéjala con DAA - comenzando en 1:30
-   Importar la API de administración de API, comenzando a las 7:10
-   Configurar el portal para desarrolladores para llamar a la API - empezando a las 9:09
-   Configurar una aplicación de escritorio para llamar a la API - comenzando a las 18:08
-   Configurar una directiva de validación de JWT para autorizar previamente las solicitudes - empezando en 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Crear un directorio de AD de Azure

Para proteger su API Web respaldado con Azure Active Directory primero debe tener un un arrendatario DAA. En este vídeo se usa un arrendatario denominado **APIMDemo** . Para crear un arrendatario DAA, iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) y haga clic en **nuevo**->**Servicios de la aplicación**->**Active Directory**->**directorio**->**Crear personalizado**. 

![Active Directory Azure][api-management-create-aad-menu]

En este ejemplo se crea un directorio llamado **APIMDemo** con un dominio predeterminado denominado **DemoAPIM.onmicrosoft.com**. Este directorio se usa en todo el vídeo.

![Active Directory Azure][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Crear un servicio Web API protegido mediante Active Directory de Azure

En este paso, API Web back-end se crea utilizando Visual Studio 2013. Este paso del vídeo se inicia a la 1:30. Para crear la API Web back-end proyecto en Visual Studio, haga clic en **archivo**->**nueva**->**proyecto**y elija la **Aplicación Web de ASP.NET** de la lista de plantillas **Web** . En este vídeo, el proyecto se denomina **APIMAADDemo**. Haga clic en **Aceptar** para crear el proyecto. 

![Visual Studio][api-management-new-web-app]

Haga clic en **API de Web** , **Seleccione una plantilla de lista** para crear un proyecto Web API. Para configurar la autenticación de directorio de Azure, haga clic en **Autenticación de cambio**.

![Nuevo proyecto][api-management-new-project]

Haga clic en **Cuentas de la organización**y especificar el **dominio** de los inquilinos de DAA. En este ejemplo, el dominio es **DemoAPIM.onmicrosoft.com**. El dominio del directorio puede obtenerse desde la ficha **dominios** del directorio.

![Dominios][api-management-aad-domains]

Configurar las opciones deseadas en el cuadro de diálogo **Cambiar autenticación** y haga clic en **Aceptar**.

![Cambio de la autenticación][api-management-change-authentication]

Al hacer clic en **Aceptar** Visual Studio intentará registrar la aplicación con el directorio de AD de Azure y se le pedirá que inicie sesión de Visual Studio. Iniciar sesión con una cuenta administrativa para el directorio.

![Iniciar sesión en Visual Studio][api-management-sign-in-vidual-studio]

Para configurar este proyecto como una comprobación de Azure Web API el cuadro para el **Host en la nube** y, a continuación, haga clic en **Aceptar**.

![Nuevo proyecto][api-management-new-project-cloud]

Le pedirá que inicie sesión en Azure y, a continuación, puede configurar la aplicación Web.

![Configurar][api-management-configure-web-app]

En este ejemplo se especifica un nuevo **plan de servicio de la aplicación** denominada **APIMAADDemo** .

Haga clic en **Aceptar** para configurar la aplicación Web y crear el proyecto.

## <a name="add-the-code-to-the-web-api-project"></a>Agregue el código al proyecto Web API

El siguiente paso en el vídeo, agrega el código al proyecto Web API. Este paso se inicia a las 4:35.

La API de Web en este ejemplo implementa un servicio de calculadora básica mediante un modelo y un controlador. Para agregar el modelo para el servicio, haga **los modelos** en el **Explorador de soluciones** y seleccione **Agregar** **clase**. Nombre de la clase `CalcInput` y haga clic en **Agregar**.

Agregue el siguiente `using` instrucción en la parte superior de la `CalcInput.cs` archivo.

    using Newtonsoft.Json;

 Reemplace la clase generada por el código siguiente.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Haga de **controladores** en el **Explorador de soluciones** y elija **Agregar**->**controlador**. Elija el **Controlador de Web API 2 - vacío** y haga clic en **Agregar**. Escriba **CalcController** para el nombre del controlador y haga clic en **Agregar**.

![Agregar controlador][api-management-add-controller]

Agregue el siguiente `using` instrucción en la parte superior de la `CalcController.cs` archivo.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Reemplace la clase controlador generado con el código siguiente. Este código implementa la `Add`, `Subtract`, `Multiply`, y `Divide` las operaciones de la API de calculadora básica.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Presione **F6** para generar y comprobar la solución.

## <a name="publish-the-project-to-azure"></a>Publique el proyecto en Azure

En este paso el Visual Studio el proyecto se publica en Azure. Este paso del vídeo se inicia a las 5:45.

Para publicar el proyecto en Azure, haga clic en el proyecto **APIMAADDemo** en Visual Studio y elija **Publicar**. Mantenga la configuración predeterminada en el cuadro de diálogo **Publicar en Web** y haga clic en **Publicar**.

![Publicar Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Conceder permisos a la aplicación de servicio de AD Azure back-end

Una nueva aplicación para el servicio de back-end es creada en el directorio de AD Azure como parte del proceso de configuración y publicación del proyecto Web API. En este paso del vídeo, comenzando a las 6:13, los permisos se conceden al API Web de back-end.

![Aplicación][api-management-aad-backend-app]

Haga clic en el nombre de la aplicación para configurar los permisos necesarios. Vaya a la ficha **Configurar** y desplácese hacia abajo hasta la sección **permisos a otras aplicaciones** . Haga clic en los **Permisos de aplicaciones** desplegable al lado de **Active Directory**de **Windows Azure** , la casilla de verificación para **leer datos de directorio**y haga clic en **Guardar**.

![Agregar permisos][api-management-aad-add-permissions]

>[AZURE.NOTE] Si no aparece, en permisos a otras aplicaciones, **Active Directory** de **Windows Azure** , haga clic en **Agregar aplicación** y agregarlo en la lista.

Cuando se configura una aplicación de Azure AD para el portal de administración de API de desarrollador, tome nota del **Identificador URI de la aplicación** para su uso en un paso posterior.

![Identificador URI de la aplicación][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar la API Web administración de API

Las API se configuran desde el portal de publisher de API que se tiene acceso a través del Portal clásico de Azure. Para llegar al portal de publisher, haga clic en **Administrar** en el Portal de Azure clásico para el servicio de administración de la API. Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia del servicio de administración de API][] en el tutorial [administrar su primera API][] .

![Portal de Publisher][api-management-management-console]

Las operaciones pueden ser [agregado manualmente a la API](api-management-howto-add-operations.md)o se pueden importar. En este vídeo, se importan las operaciones en formato Pavoneo a partir de 6:40.

Cree un archivo denominado `calcapi.json` con el siguiente contenido y guardarlo en el equipo. Asegúrese de que el `host` puntos de atributo a la API Web de back-end. En este ejemplo `"host": "apimaaddemo.azurewebsites.net"` se utiliza.

{"swagger": "2.0", "info": {"title": "Calculadora", "description": "Aritmética sobre HTTP!", "version": "1.0"}, "host": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "esquemas": ["http"], "rutas": {"/ add? un = {a} & b = {b}": {"get": {"description": "Responde con una suma de dos números.", "operationId": "Agregar dos enteros", "parámetros": [{"nombre": "a", "en": "consulta", "description": "primer operando. El valor predeterminado es <code>51</code>. ","required": true,"default":"51","enum": ["51"]}, {"nombre":"b","en":"consulta","description":"segundo operando. El valor predeterminado es <code>49</code>. ","required": true,"default":"49","enum": ["49"]}],"respuestas": {}}}," / sub?a = {a} & b = {b} ": {"get": {"description":"Responde con una diferencia entre dos números.","operationId":"Restar dos números enteros","parámetros": [{"nombre":"a","en":"consulta","description":"primer operando. El valor predeterminado es <code>100</code>. ","required": true,"default":"100","enum": ["100"]}, {"nombre":"b","en":"consulta","description":"segundo operando. El valor predeterminado es <code>50</code>. ","required": true,"default":"50","enum": ["50"]}],"respuestas": {}}}," / div?a = {a} & b = {b} ": {"get": {"description":"Responde con un cociente de dos números.","operationId":"Dividir dos enteros","parámetros": [{"nombre":"a","en":"consulta","description":"primer operando. El valor predeterminado es <code>100</code>. ","required": true,"default":"100","enum": ["100"]}, {"nombre":"b","en":"consulta","description":"segundo operando. El valor predeterminado es <code>20</code>. ","required": true,"default":"20","enum": ["20"]}],"respuestas": {}}}," / mul?a = {a} & b = {b} ": {"get": {"description":"Responde con un producto de dos números.","operationId":"Multiplicar dos enteros","parámetros": [{"nombre":"a","en":"consulta","description":"primer operando. El valor predeterminado es <code>20</code>. ","required": true,"default":"20","enum": ["20"]}, {"nombre":"b","en":"consulta","description":"segundo operando. El valor predeterminado es <code>5</code>. ","required": true,"default":"5","enum": ["5"]}],"respuestas": {}}}}}

Para importar la API de la Calculadora, haga clic en **API** desde el menú de la **API de administración** de la izquierda y, a continuación, haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Realice los pasos siguientes para configurar la API de la calculadora.

1. Haga clic en **desde archivo**, busque el `calculator.json` archivo que ha guardado y haga clic en el botón de opción **Swagger** .
2. En el cuadro de texto **sufijo de dirección URL de API de Web** , escriba **calc** .
3. Haga clic en el cuadro de **productos (opcionales)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Agregar nueva API][api-management-import-new-api]

Una vez importada la API, se muestra la página de resumen de la API en el portal de publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Llamar a la API sin éxito desde el portal del programador

En este momento, la API ha sido importada en la API de administración, pero no aún pueden llamarse correctamente desde el portal del programador porque el servicio back-end está protegido con autenticación de Active Directory de Azure. Esto se demuestra en el vídeo a partir de 7:40 mediante los pasos siguientes.

Haga clic en el **portal para desarrolladores** desde la parte superior derecha del portal de publisher.

![Portal para desarrolladores][api-management-developer-portal-menu]

**API** y haga clic en **Calculadora** API.

![Portal para desarrolladores][api-management-dev-portal-apis]

Haga clic en **Pruébelo**.

![Pruébelo][api-management-dev-portal-try-it]

Haga clic en **Enviar** y observe el estado de la respuesta de **401 no autorizado**.

![Enviar][api-management-dev-portal-send-401]

La solicitud está autorizada porque el servidor de la API está protegido por Azure Active Directory. Antes de llamar correctamente a la API el desarrollador portal debe configurarse para autorizar a los programadores que utilicen OAuth 2.0. Este proceso se describe en las secciones siguientes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrar el portal para desarrolladores como una aplicación de DAA

El primer paso para configurar el portal para desarrolladores para autorizar a los programadores que utilicen OAuth 2.0 es registrar el portal para desarrolladores como una aplicación de DAA. Esto se demuestra a partir a las 8:27 del vídeo.

Desplácese hasta el inquilino de Azure AD desde el primer paso de este vídeo, en este ejemplo, **APIMDemo** y vaya a la ficha **aplicaciones** .

![Nueva aplicación][api-management-aad-new-application-devportal]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Active Directory de Azure y elija **Agregar una aplicación que está desarrollando mi organización**.

![Nueva aplicación][api-management-new-aad-application-menu]

Elija la **aplicación Web o API de Web**, escriba un nombre y haga clic en la flecha siguiente. En este ejemplo se utiliza **APIMDeveloperPortal** .

![Nueva aplicación][api-management-aad-new-application-devportal-1]

Para la **dirección URL de inicio de sesión** , escriba la dirección URL de su servicio de administración de API y anexar `/signin`. En este ejemplo se utiliza **https://contoso5.portal.azure-api.net/signin **.

Para la **Dirección URL de la aplicación Id.** escriba la dirección URL de su servicio de administración de API y anexar algunos caracteres únicos. Pueden ser cualquier carácter deseado y en este ejemplo se utiliza **https://contoso5.portal.azure-api.net/dp** . Cuando se configuran las **Propiedades de la aplicación** que desee, haga clic en la casilla de verificación para crear la aplicación.

![Nueva aplicación][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar un servidor de autorización API administración OAuth 2.0

El siguiente paso es configurar un servidor de autorización de OAuth 2.0 en la API de administración. Este paso se demuestra en el vídeo empezando a las 9:43.

Haga clic en **seguridad** en el menú de gestión de la API de la izquierda, haga clic en **OAuth 2.0**y, a continuación, haga clic en servidor de **autorización de agregar** .

![Agregar servidor de autorización][api-management-add-authorization-server]

En los campos **nombre** y **Descripción** , escriba un nombre y una descripción opcional. Estos campos se utilizan para identificar el servidor de autorización OAuth 2.0 dentro de la instancia de servicio de administración de la API. En este ejemplo se utiliza la **demostración del servidor de autorización** . Más tarde cuando se especifica un servidor OAuth 2.0 que se utilizará para la autenticación de una API, seleccionará este nombre.

Para la **dirección URL de la página de registro de cliente** , escriba un valor de marcador de posición, como `http://localhost`.  Señala la **dirección URL de la página de registro de cliente** a la página que los usuarios pueden utilizar para crear y configurar sus propias cuentas para proveedores OAuth 2.0 que admiten la administración de usuarios de cuentas. En este ejemplo no los usuarios crear y configurar sus propias cuentas de modo que se utilice un marcador de posición.

![Agregar servidor de autorización][api-management-add-authorization-server-1]

A continuación, especifique la **dirección URL del extremo de autorización** y **dirección URL del extremo de Token**.

![Servidor de autorización][api-management-add-authorization-server-1a]

Estos valores se pueden recuperar desde la página de **Los extremos de la aplicación** de la aplicación de DAA que creó para el portal del programador. Para el acceso a los extremos vaya a la ficha **Configurar** de la aplicación de DAA y haga clic en **los extremos de la vista**.

![Aplicación][api-management-aad-devportal-application]

![Extremos de vista][api-management-aad-view-endpoints]

Copie el **extremo de autorización OAuth 2.0** y pegarlo en el cuadro de texto **dirección URL de extremo de autorización** .

![Agregar servidor de autorización][api-management-add-authorization-server-2]

Copie el **extremo token OAuth 2.0** y pegarlo en el cuadro de texto **dirección URL de extremo de símbolo (token)** .

![Agregar servidor de autorización][api-management-add-authorization-server-2a]

Además de pegar en el extremo del símbolo (token), agregue un parámetro body adicional denominado **recursos** y para el uso del valor del **Identificador URI de la aplicación** de la aplicación DAA para el servicio de back-end que se creó cuando se publicó el proyecto de Visual Studio.

![Identificador URI de la aplicación][api-management-aad-sso-uri]

A continuación, especifique las credenciales del cliente. Estas son las credenciales para el recurso que desee tener acceso, en este caso, el servicio de back-end.

![Credenciales del cliente][api-management-client-credentials]

Para obtener el **Identificador de cliente**, vaya a la ficha **Configurar** de la aplicación de DAA para el servicio de back-end y copie el **Id de cliente**.

Para obtener el clic **Secreto a cliente** **Seleccione duración** desplegable en la sección de **claves** y especificar un intervalo. En este ejemplo se utiliza 1 año.

![Id. de cliente][api-management-aad-client-id]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave. 

>[AZURE.IMPORTANT] Tome nota de esta clave. Una vez que cierre la ventana de configuración de Active Directory de Azure, la clave no se puede mostrar de nuevo.

Copiar la clave en el Portapapeles, vuelva al portal de publisher, pegue la clave en el cuadro de texto **Secreto de cliente** y haga clic en **Guardar**.

![Agregar servidor de autorización][api-management-add-authorization-server-3]

Inmediatamente después de las credenciales del cliente es una concesión de autorización de código. Copie este código de autorización y vuelva a la aplicación de portal de desarrolladores de Azure AD Configurar página y pegar la autorización conceder en el campo de **Dirección URL de respuesta** y haga clic de nuevo en **Guardar** .

![Dirección URL de respuesta][api-management-aad-reply-url]

El siguiente paso es configurar los permisos para el portal del programador aplicación DAA. Haga clic en **Permisos de la aplicación** y la casilla de verificación para **leer datos de directorio**. Haga clic en **Guardar** para guardar los cambios y, a continuación, haga clic en **Agregar aplicación**.

![Agregar permisos][api-management-add-devportal-permissions]

Haga clic en el icono de búsqueda, escriba **APIM** en Inicio con el cuadro, seleccione **APIMAADDemo**y haga clic en la casilla de verificación para guardar.

![Agregar permisos][api-management-aad-add-app-permissions]

Haga clic en **Delegar permisos** para **APIMAADDemo** la casilla de verificación para **APIMAADDemo de acceso**y haga clic en **Guardar**. Esto permite que el desarrollador de la aplicación de portal de acceso al servicio back-end.

![Agregar permisos][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Habilitar la autorización de usuario OAuth 2.0 de la API de calculadora

Ahora que el servidor de OAuth 2.0 está configurado, puede especificar en la configuración de seguridad de la API. Este paso se demuestra en el vídeo empezando a las 14:30.

Haga clic en **API** en el menú de la izquierda y haga clic en **Calculadora** para ver y configurar sus opciones.

![Calculadora de API][api-management-calc-api]

Vaya a la ficha **seguridad** , active la casilla de verificación **OAuth 2.0** , seleccione el servidor de autorización deseada en la lista desplegable **servidor de autorización** y haga clic en **Guardar**.

![Calculadora de API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Llamar correctamente a la API de la Calculadora desde el portal del programador

Ahora que se configura la autorización OAuth 2.0 en la API, pueden llamarse con éxito sus operaciones en el centro de desarrollo. Este paso se demuestra en el vídeo empezando a las 15:00.

Volver a la operación de **Agregar dos enteros** del servicio Calculadora en el portal del programador y haga clic en **Pruébelo**. Tenga en cuenta el nuevo elemento en la sección **autorización** correspondiente al servidor de autorización que acaba de agregar.

![Calculadora de API][api-management-calc-authorization-server]

Seleccione el **código de autorización** de la lista desplegable de autorización y escriba las credenciales de la cuenta para utilizarla. Si ya están firmadas con la cuenta que puede que no se le.

![Calculadora de API][api-management-devportal-authorization-code]

Haga clic en **Enviar** y observe el **estado de la respuesta** de **200 OK** y los resultados de la operación en el contenido de la respuesta.

![Calculadora de API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar una aplicación de escritorio para llamar a la API

El siguiente procedimiento en el vídeo comienza a las 16:30 y configura una aplicación de escritorio simple para llamar a la API. El primer paso es registrar la aplicación de escritorio en Azure AD y dar acceso al directorio y al servicio back-end. A las 18:25 hay una demostración de la aplicación de escritorio llamada a una operación en la calculadora de API.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar una directiva de validación de JWT para autorizar previamente las solicitudes

El procedimiento final en el vídeo comienza a las 20:48 y muestra cómo utilizar la directiva [JWT validar](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para autorizar previamente las solicitudes al validar los tokens de acceso de cada solicitud entrante. Si la solicitud no está validada por la directiva de JWT validar, la solicitud se bloquea por la API de administración y no se pasa a lo largo del back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Otra demostración de cómo configurar y utilizar esta directiva [177 de nube cubrir episodio: más funciones de administración de la API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) y avance rápido a 13:50. Avance rápido para ver las directivas configuradas en el editor de directivas de 15:00 a 18:50, para una demostración de la llamada a una operación desde el portal para desarrolladores con y sin el símbolo (token) de autorización necesaria.

## <a name="next-steps"></a>Próximos pasos
-   Vea más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) acerca de la API de administración.
-   Para que otras formas de proteger el servicio back-end, vea [autenticación de certificado mutuo](api-management-howto-mutual-certificates.md) y [Conectar a través de VPN o ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Cree una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance
[Administrar su primera API]: api-management-get-started.md
