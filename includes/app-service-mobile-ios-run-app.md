
1. En el equipo Mac, visite el [Portal de Azure]. Haga clic en **Examinar todos los** > **Aplicaciones móviles** > el back-end que acaba de crear. En la configuración de la aplicación móvil, haga clic en **Inicio rápido** > **iOS (objetivo-C)**. Si lo prefiere, Swift, haga clic en **Inicio rápido** > **iOS (Swift)** en su lugar. **Descargue y ejecute el proyecto de iOS**, haga clic en **Descargar**. Esto descarga un proyecto Xcode completo para una aplicación previamente configurado para conectarse a su servidor. Abra el proyecto con Xcode.

2. Presione el botón **Ejecutar** para generar el proyecto e iniciar la aplicación en el simulador de iOS.

3. En la aplicación, escriba el texto significativo, como _completar el tutorial_ y, a continuación, haga clic en el signo más (**+**) icono. Esto envía una solicitud POST para el back-end Azure que implementó anteriormente. El back-end inserta los datos de la solicitud está en la tabla TodoItem SQL y devuelve información sobre los elementos recién almacenados a la aplicación móvil. La aplicación móvil muestra estos datos en la lista. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal de Azure]: https://portal.azure.com/
