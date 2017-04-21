
1. En Explorador de soluciones de Visual Studio, haga clic en el proyecto de aplicación Windows Store, haga clic en **almacén** > **Asociar la aplicación con el almacén...**.

    ![Asociar la aplicación con el almacén de Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. En el asistente, haga clic en **siguiente**, identifícate con tu cuenta de Microsoft, escriba un nombre para su aplicación en **reserva un nuevo nombre de la aplicación**y haga clic en **reservar**.

3. Después de crea correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **siguiente**y, a continuación, haga clic en **asociar**. Esto agrega la información de registro del almacén de Windows necesaria al manifiesto de aplicación.

7. Repita los pasos 1 y 3 para el proyecto de aplicación de Windows Phone Store utilizando el mismo registro que creó anteriormente para la aplicación del almacén de Windows.  

7. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview), iniciar sesión con tu cuenta de Microsoft, haga clic en el nuevo registro de la aplicación en **Mis aplicaciones**, expanda **Servicios de** > **notificaciones de inserción**.

8. En la página de **notificaciones de inserción** , haga clic en **el sitio de Servicios Live** en **Windows Push Notification Services (WNS) y aplicaciones móviles de Microsoft Azure**y tome nota de los valores del **SID del paquete** y el valor *actual* en **Secreto de la aplicación**. 

    ![Configuración de la aplicación en el centro de desarrollo](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] El secreto de la aplicación y el paquete de SID son las credenciales de seguridad importante. No compartir estos valores con cualquier persona o distribuirlos con su aplicación.
