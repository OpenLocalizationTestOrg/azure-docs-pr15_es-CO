Los clientes de Azure pueden desbloquear 25.000 correos electrónicos gratuitos cada mes. Estos 25.000 mensajes mensuales gratuitos le dará acceso a informes y análisis y [todas las API][] (Web, SMTP, eventos, análisis y más). Para obtener información acerca de los servicios adicionales proporcionados por SendGrid, consulte la página de [Características SendGrid][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para suscribirse a una cuenta de SendGrid

1. Inicie sesión en el [Portal de administración de Azure][].

2. En el panel inferior del portal de administración, haga clic en **nuevo**.

    ![nuevo comando barra][command-bar-new]

3. Haga clic en el **mercado**.

    ![almacén de sendgrid][sendgrid-store]

4. En el cuadro de diálogo **Elija una aplicación y servicio** , seleccione **SendGrid** y haga clic en la flecha derecha.

5. En el cuadro de diálogo **Personalizar la aplicación y servicio** seleccione el plan de **SendGrid** en que desea registrarse.

6. Escriba un nombre para identificar el servicio de **SendGrid** en la configuración de Azure, o utilice el valor predeterminado de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Nombres deben estar entre 1 y 100 caracteres de longitud y contienen sólo caracteres alfanuméricos, guiones, puntos y caracteres de subrayado. El nombre debe ser único en la lista de elementos de almacenamiento de Azure suscritos.

    ![almacén-pantalla-2][store-screen-2]

7. Elija un valor para la región; Por ejemplo, oeste de Estados Unidos.

8. Haga clic en la flecha derecha.

9. En la ficha **Revisar compra** , revise el plan y la información de precios así las condiciones legales. Si acepta los términos, haga clic en la marca de verificación. Después de hacer clic en la marca de verificación, la cuenta SendGrid comenzará el [proceso de provisioning de SendGrid].

    ![almacén-pantalla-3][store-screen-3]

10. Después de confirmar tu compra se redirigen al panel complementos y verá el mensaje de **SendGrid de compra**.

    ![mensaje compras sendgrid][sendgrid-purchasing-message]

    Su cuenta de SendGrid se aprovisiona inmediatamente y verá el mensaje **comprado correctamente el complemento SendGrid**. Ahora se crean su cuenta y las credenciales. Está listo para enviar mensajes de correo electrónico en este momento. 

    Para modificar el plan de suscripción o consulte la SendGrid configuración de contacto, haga clic en el nombre de su servicio de SendGrid para abrir el tablero de mandos de mercado SendGrid. 

    ![sendgrid-agregar-en-panel][sendgrid-add-on-dashboard]

    Para enviar un correo electrónico a SendGrid, debe proporcionar las credenciales de su cuenta (nombre de usuario y contraseña).

### <a name="to-find-your-sendgrid-credentials"></a>Para buscar las credenciales SendGrid ###

1. Haga clic en **información de conexión**.

    ![sendgrid-conexión-botón de información][sendgrid-connection-info-button]

2. En el cuadro de diálogo *información de conexión* , copie la **contraseña** y el nombre de usuario para utilizarla más adelante en este tutorial.

    ![información de conexión de sendgrid][sendgrid-connection-info]

    Para configurar el correo electrónico entregabilidad, haga clic en el botón **Administrar** . Esta forma se dirige a su Panel de Control SendGrid. 

    ![sendgrid-panel de control][sendgrid-control-panel]

    Para obtener más información acerca de cómo empezar con SendGrid, consulte la [Introducción SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Características SendGrid]: http://sendgrid.com/features
[Portal de administración de Azure]: https://manage.windowsazure.com
[SendGrid Introducción]: http://sendgrid.com/docs
[Proceso de Provisioning de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todas las API]: https://sendgrid.com/docs/API_Reference/index.html

