
1. Visite el [Portal de Azure]. Haga clic en **Examinar todos los** > **Aplicaciones móviles** > el back-end que acaba de crear. En la configuración de la aplicación móvil, haga clic en **Inicio rápido** > **Cordova**. En **Configurar la aplicación de cliente**, seleccione **crear una nueva aplicación**, haga clic en **Descargar**. Esto descarga un proyecto completo de Córdoba para una aplicación previamente configurado para conectarse a su servidor.

2. Desempaquetar el archivo ZIP descargado en un directorio del disco duro, desplácese hasta el archivo de solución (.sln) y abrirlo con Visual Studio.

5. En Visual Studio, desde la lista desplegable situada junto a la flecha de inicio permite elegir la plataforma de solución (Android, iOS o Windows), seleccione un dispositivo de implementación específicas o emulador haciendo clic en la lista desplegable en la flecha verde. Tenga en cuenta que puede utilizar la plataforma Android por defecto y el emulador de rizo. Tutoriales más avanzados requerirá que seleccione un dispositivo compatible o un emulador. 

6. Presione F5 o haga clic en la flecha verde para construir y y ejecutar su aplicación de Córdoba. Si ve un cuadro de diálogo de seguridad en el emulador solicitante el acceso a la red, acéptelo.   

7. Después el se inicia la aplicación en el dispositivo o el emulador, escriba texto significativo en **Escriba el texto nuevo**, como _completar el tutorial_ y, a continuación, haga clic en el botón **Agregar** .  
Esto envía una solicitud POST para el back-end Azure que implementó anteriormente. El back-end inserta los datos de la solicitud está en la tabla TodoItem en la base de datos SQL y devuelve información sobre los elementos recién almacenados a la aplicación móvil. La aplicación móvil muestra estos datos en la lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Repita los tres pasos anteriores para cada plataforma de dispositivo que desea admitir.

[Portal de Azure]: https://portal.azure.com/
