<properties 
    pageTitle="Cómo autorizar a cuentas de desarrollador mediante Active Directory de Azure en Azure API administración" 
    description="Obtenga información sobre cómo autorizar a los usuarios mediante Active Directory de Azure en la API de administración." 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Cómo autorizar a cuentas de desarrollador mediante Active Directory de Azure en Azure API administración


## <a name="overview"></a>Información general
Esta guía muestra cómo habilitar el acceso al portal de desarrolladores para todos los usuarios en uno o más directorios activos de Azure. Esta guía también muestra cómo administrar grupos de usuarios de Active Directory de Azure agregando grupos externos que contienen los usuarios de un Active Directory de Azure.

>Para completar los pasos descritos en esta guía debe tener un Azure de Active Directory en el que se va a crear una aplicación.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Cómo autorizar a cuentas de desarrollador mediante Active Directory de Azure

Para empezar, haga clic en **Administrar** en el Portal de Azure clásico para el servicio de administración de la API. Esto abre el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia del servicio de administración de API][] en el tutorial para [comenzar con la administración de la API de Azure][] .

Haga clic en **seguridad** en el menú de **Gestión de la API** de la izquierda y haga clic en **Identidades externas**.

![Identidades externas][api-management-security-external-identities]

Haga clic en **Azure Active Directory**. Tome nota de la **Dirección URL de redireccionamiento** y cambia a Azure Active Directory en el Portal clásico de Azure.

![Identidades externas][api-management-security-aad-new]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Active Directory de Azure y elija **Agregar una aplicación que está desarrollando mi organización**.

![Agregar nueva aplicación de Azure Active Directory][api-management-new-aad-application-menu]

Escriba un nombre para la aplicación, seleccione **aplicación Web o API de Web**y haga clic en el botón siguiente.

![Nueva aplicación de Azure Active Directory][api-management-new-aad-application-1]

**Dirección URL de inicio de sesión**, especifique la dirección URL de sesión de su portal para desarrolladores. En este ejemplo, la **dirección URL de inicio de sesión** es `https://aad03.portal.current.int-azure-api.net/signin`. 

Para la **Dirección URL de ID de la aplicación**, especifique el dominio predeterminado o un dominio personalizado para Active Directory de Azure y anexar una cadena única. En este ejemplo se utiliza el dominio predeterminado de **https://contoso5api.onmicrosoft.com** con el sufijo de **/api/API** especificado.

![Nuevas propiedades de aplicación de Active Directory de Azure][api-management-new-aad-application-2]

Haga clic en el botón de verificación para guardar y crear la nueva aplicación y cambie a la ficha **Configurar** para configurar la nueva aplicación.

![Crear nueva aplicación de Azure Active Directory][api-management-new-aad-app-created]

Si varios directorios activos de Azure se va a utilizar para esta aplicación, haga clic en **Sí** para la **aplicación es multiempresa**. El valor predeterminado es **No**.

![La aplicación es multiempresa][api-management-aad-app-multi-tenant]

Copie la **Dirección URL de redirección** de la sección de **Azure Active Directory** de la ficha de **Identidades externas** en el portal de publisher y pegarlo en el cuadro de texto **Dirección URL de respuesta** . 

![Dirección URL de respuesta][api-management-aad-reply-url]

Desplácese hasta la parte inferior de la ficha configurar, seleccione la lista desplegable **Permisos de la aplicación** y comprobar los **datos de directorio de lectura**.

![Permisos de aplicación][api-management-aad-app-permissions]

Seleccione la lista desplegable **Permisos de delegado** y comprobar **Habilitar inicio de sesión y leer perfiles de los usuarios**.

![Permisos delegados][api-management-aad-delegated-permissions]

>Para obtener más información acerca de la aplicación y los permisos delegados, vea [obtener acceso a la API de gráfico][].

Copiar el **Id de cliente** en el Portapapeles.

![Id. de cliente][api-management-aad-app-client-id]

Volver al portal de publisher y pegar en el **Identificador de cliente** copiado desde la configuración de la aplicación de Active Directory de Azure.

![Id. de cliente][api-management-client-id]

Volver a la configuración de Active Directory de Azure y haga clic en **Seleccione duración de** lista desplegable en la sección de **claves** y especificar un intervalo. En este ejemplo se utiliza **1 año** .

![Clave][api-management-aad-key-before-save]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave. Copie la clave en el Portapapeles.

>Tome nota de esta clave. Una vez que cierre la ventana de configuración de Active Directory de Azure, la clave no se puede mostrar de nuevo.

![Clave][api-management-aad-key-after-save]

Volver al portal de publisher y pegue la clave en el cuadro de texto **Secreto del cliente** .

![Secreto del cliente][api-management-client-secret]

**Los inquilinos permitido** especifica los directorios que tienen acceso a las API de la instancia del servicio de administración de la API. Especifique los dominios de las instancias de Azure Active Directory al que desea conceder acceso. Varios dominios puede separar con comas, espacios o saltos de línea.

![Inquilinos permitidos][api-management-client-allowed-tenants]

Varios dominios pueden especificarse en la sección de **Los inquilinos permitido** . Antes de que cualquier usuario puede iniciar sesión desde un dominio diferente del dominio original donde se registró la aplicación, un administrador global del dominio diferente debe conceder permiso para la aplicación de acceso a los datos de directorio. Para conceder permiso, un administrador global debe iniciar sesión en la aplicación y haga clic en **Aceptar**. En el ejemplo siguiente `miaoaad.onmicrosoft.com` se ha agregado a **Los inquilinos permitido** y global Administrador de ese dominio está iniciando por primera vez.

![Permisos][api-management-permissions-form]

>Si un administrador no global intenta iniciar sesión antes de que los permisos se conceden por un administrador global, se produce un error en el intento de inicio de sesión y aparece una pantalla de error.

Una vez especificada la configuración deseada, haga clic en **Guardar**.

![Guardar][api-management-client-allowed-tenants-save]

Una vez que se guardan los cambios, los usuarios de Active Directory especificado de Azure pueden iniciar sesión en el portal del programador siguiendo los pasos de [iniciar sesión en el portal para desarrolladores con una cuenta de Active Directory de Azure][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Cómo agregar un grupo de directorio activo de Azure externo

Después de habilitar el acceso de los usuarios de un Active Directory de Azure, puede agregar grupos de Active Directory de Azure en la API de administración para administrar más fácilmente la asociación de los desarrolladores en el grupo con los productos que desee.

> Para configurar un grupo de Active Directory de Azure externo, Azure Active Directory debe configurarse primero en la ficha de identidades siguiendo el procedimiento descrito en la sección anterior. 

Los grupos de Active Directory de Azure externos se agregan desde la ficha **visibilidad** del producto para el que desea conceder acceso al grupo. Haga clic en **productos**y, a continuación, haga clic en el nombre del producto deseado.

![Configurar el producto][api-management-configure-product]

Cambie a la ficha **visibilidad** y haga clic en **Agregar grupos de Active Directory de Azure**.

![Agregar grupos][api-management-add-groups]

Seleccione el **Inquilino de Azure Active Directory** desde la lista desplegable y, a continuación, escriba el nombre del grupo deseado en los **grupos** que deben agregarse cuadro de texto.

![Seleccionar grupo][api-management-select-group]

Este nombre de grupo puede encontrarse en la lista de **grupos** de Active Directory Azure, tal como se muestra en el ejemplo siguiente.

![Lista de grupos de Active Directory Azure][api-management-aad-groups-list]

Haga clic en **Agregar** para validar el nombre del grupo y agregue el grupo. En este ejemplo, los **Desarrolladores de Contoso 5** se agrega el grupo externo. 

![Grupo agregado][api-management-aad-group-added]

Haga clic en **Guardar** para guardar la nueva selección de grupo.

Una vez que se ha configurado un grupo de Active Directory de Azure Azure desde un producto, está disponible para activarla en la ficha de **visibilidad** para los demás productos de la instancia del servicio de administración de la API.

Para revisar y configurar las propiedades de los grupos externos una vez que se hayan agregado, haga clic en el nombre del grupo de la ficha **grupos** .

![Administrar grupos][api-management-groups]

Desde aquí se pueden editar el **nombre** y la **Descripción** del grupo.

![Editar grupo][api-management-edit-group]

Los usuarios de Active Directory configurado de Azure pueden iniciar sesión en el portal para desarrolladores y ver y suscribirse a los grupos para los que tienen visibilidad siguiendo las instrucciones en la sección siguiente.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Cómo iniciar sesión en el portal para desarrolladores con una cuenta de Active Directory de Azure

Para iniciar sesión en el portal para desarrolladores con una cuenta de Azure Active Directory configurada en las secciones anteriores, abra una nueva ventana del explorador con la **dirección URL de inicio de sesión** desde la configuración de la aplicación de Active Directory y haga clic en **Active Directory de Azure**.

![Portal para desarrolladores][api-management-dev-portal-signin]

Escriba las credenciales de uno de los usuarios en el Active Directory de Azure y haga clic en **iniciar sesión**.

![Inicia sesión][api-management-aad-signin]

Se le pedirá con un formulario de registro, si se requiere información adicional. Complete el formulario de registro y haga clic en **suscribirse**.

![Registro][api-management-complete-registration]

Ahora, el usuario se registra en el portal del programador para la instancia del servicio de administración de API.

![Registro completado][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Comenzar con la administración de la API de Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Cree una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Acceso a la API de gráfico]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Inicie sesión en el portal para desarrolladores con una cuenta de Active Directory de Azure]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

