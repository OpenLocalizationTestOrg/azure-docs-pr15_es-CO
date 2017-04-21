## <a name="prerequisites"></a>Requisitos previos

Antes de que podemos escribir el código de administración de CDN, necesitamos hacer cierta preparación para habilitar nuestro código interactuar con el Administrador de recursos de Azure.  Para ello, necesitará:

* Crear un grupo de recursos que contiene el perfil de CDN que se crea en este tutorial
* Configurar Active Directory para proporcionar autenticación para nuestra aplicación de Azure
* Aplicar permisos al grupo de recursos para que sólo los usuarios autorizados de nuestro inquilino Azure AD puedan interactuar con nuestro perfil CDN

### <a name="creating-the-resource-group"></a>Creando el grupo de recursos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el botón **nuevo** en la parte superior izquierda y, a continuación, **administración**y **Grupo de recursos**.
    
    ![Crear un nuevo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Llame a su grupo de recursos *CdnConsoleTutorial*.  Seleccione la suscripción y elija una ubicación cercana a usted.  Si lo desea, puede hacer clic en la casilla de verificación **fijar a Mi panel** para fijar el grupo de recursos al panel en el portal.  Esto resultará más fácil encontrarlos.  Una vez haya realizado las selecciones, haga clic en **crear**.

    ![El grupo de recursos de nombres](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Después de crea el grupo de recursos, si no anclarlo al escritorio, usted puede encontrar haciendo clic en **Examinar**, a continuación, **Grupos de recursos**.  Haga clic en el grupo de recursos para abrirlo.  Anote el **Identificador de suscripción**.  Lo necesitaremos más adelante.

    ![El grupo de recursos de nombres](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Crear la aplicación de Azure AD y aplicar permisos

Existen dos enfoques para la autenticación de la aplicación con Active Directory de Azure: usuarios individuales o a un principal de servicio. Un servicio principal es similar a una cuenta de servicio de Windows.  En lugar de conceder permisos para interactuar con los perfiles CDN de un usuario en particular, en su lugar se conceda los permisos para el servicio principal.  Entidades de servicio se utilizan generalmente para procesos automatizados, no interactivo.  Aunque este tutorial está escribiendo una aplicación de consola interactiva, nos centraremos en el enfoque principal del servicio.

Creación de una entidad de seguridad de servicio consta de varios pasos, incluida la creación de una aplicación de Azure de Active Directory.  Para ello, vamos a [seguir este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Asegúrese de seguir todos los pasos del [tutorial vinculado](../articles/resource-group-create-service-principal-portal.md).  Es *extremadamente importante* completarla exactamente como se describe.  Asegúrese de que tenga en cuenta el **inquilino ID**, **nombre de dominio del inquilino** (habitualmente un *. onmicrosoft.com* dominio a menos que haya especificado un dominio personalizado), **ID de cliente**y la **clave de autenticación de cliente**, como necesitaremos más adelante.  Tenga mucho cuidado proteger su **ID de cliente** y la **clave de autenticación de cliente**, como estas credenciales pueden ser utilizadas por cualquier persona para ejecutar operaciones como el principal de servicio. 
>   
> Cuando llegue al paso denominado [aplicación multiempresa de configurar](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), seleccione **No**.
> 
> Cuando llegue al paso [asignar aplicaciones a función](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilice el grupo de recursos que creamos anteriormente, *CdnConsoleTutorial*, pero en lugar de la función de **lector** , asignar el rol de **Colaborador de perfil CDN** .  Después de asignar el rol de **Colaborador de perfil CDN** de la aplicación en el grupo de recursos, vuelva a este tutorial. 

Una vez que haya creado su servicio principal y la función de **Colaborador de perfil CDN** , el blade de **usuarios** para el grupo de recursos debe ser similar a esto.

![Módulo de usuarios](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Autenticación de usuario interactivo

Si, en lugar de una entidad de servicio, prefiere que autenticación de usuario interactivo, el proceso es muy similar de un principal de servicio.  De hecho, debe seguir el mismo procedimiento, pero hacer unos pequeños cambios.

> [AZURE.IMPORTANT] Siga estos pasos únicamente si opta por utilizar la autenticación de usuario individuales en lugar de un servicio principal.

1. Al crear la aplicación, en lugar de la **Aplicación Web**, elija la **aplicación nativa**. 
    
    ![Aplicación nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. En la siguiente página, se le pedirá para un **identificador URI redirigido**.  El URI no puede validar, pero recuerde lo que ha escrito.  Necesitará más adelante. 

3. No hay ninguna necesidad de crear una **clave de autenticación de cliente**.

4. En lugar de asignar a un principal de servicio a la función de **Colaborador de perfil CDN** , vamos a asignar usuarios individuales o grupos.  En este ejemplo, puede ver que he asignado al rol **Colaborador de perfil CDN** *CDN demostración de usuario* .  
    
    ![Acceso de usuario individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

