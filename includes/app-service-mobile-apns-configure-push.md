

1. En el equipo Mac, iniciar **Acceso a llaves**. Abrir **Mis certificados** en la **categoría** en la barra izquierda de navigationn. Busque el certificado SSL que descargó en la sección anterior y revelar su contenido. Seleccione sólo el certificado (no active la clave privada) y se [exporta](https://support.apple.com/kb/PH20122?locale=en_US).

2. En el [portal de Azure](https://portal.azure.com/), haga clic en **Examinar todos los** > **Los servicios de la aplicación** > la aplicación Mobile de back-end. En **configuración**, en **Push de servicio de la aplicación**, haga clic en haga clic en el nombre del concentrador de notificación. Vaya a **Apple Push Notification Services** > **cargar el certificado**. Cargar el archivo. p12, selección del **modo** de correcto (dependiendo de si el cliente SSL certificado anterior es producción o espacio aislado). Guarde los cambios.

¡El servicio está configurado ahora para trabajar con notificaciones push en iOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
