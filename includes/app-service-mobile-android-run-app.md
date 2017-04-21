
1. Visite el [Portal de Azure]. Haga clic en **Examinar todos los** > **Aplicaciones móviles** > el back-end que acaba de crear. En la configuración de la aplicación móvil, haga clic en **Inicio rápido** > **Android)**. **Configurar la aplicación de cliente**, haga clic en **Descargar**. Esto descarga un proyecto Android completo para una aplicación previamente configurado para conectarse a su servidor. 

2. Abra el proyecto mediante **Studio Android**, utilizando **el proyecto de importación (ADT de Eclipse, Gradle, etc.)**. Asegúrese de que realice esta selección de importación para evitar los errores JDK.

3. Presione el botón **ejecutar 'app'** para generar el proyecto e iniciar la aplicación en el simulador de Android.

4. En la aplicación, escriba texto significativo, como _completar el tutorial_ y, a continuación, haga clic en el botón 'Agregar'. Esto envía una solicitud POST para el back-end Azure que implementó anteriormente. El back-end inserta los datos de la solicitud está en la tabla TodoItem SQL y devuelve información sobre los elementos recién almacenados a la aplicación móvil. La aplicación móvil muestra estos datos en la lista. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portal de Azure]: https://portal.azure.com/
