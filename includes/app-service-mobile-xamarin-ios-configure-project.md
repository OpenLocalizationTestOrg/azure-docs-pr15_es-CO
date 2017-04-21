####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Configurar el proyecto de iOS en Xamarin Studio

1. En Xamarin.Studio, abra **Info.plist**y actualizar el **Identificador de paquete** con el identificador de paquete que creó anteriormente con el nuevo ID de la aplicación.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Desplácese hacia abajo hasta el **Fondo modos** y Active la casilla de **Habilitar modos de fondo** y el cuadro **notificaciones remotas** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Haga doble clic en el proyecto en el Panel de la solución para abrir las **Opciones del proyecto**.

4.  Elija la **firma de paquete de iOS** en **Generar**y seleccionar la correspondiente **identidad** y **perfil de Provisioning** que había establecido para este proyecto. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Esto garantiza que el proyecto utiliza el nuevo perfil para la firma de código. Para el dispositivo oficial de Xamarin provisioning documentación, vea [Aprovisionamiento de dispositivos de Xamarin].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Configurar el proyecto de iOS en Visual Studio

1. En Visual Studio, haga clic en el proyecto y, a continuación, haga clic en **Propiedades**.

2. En las páginas de propiedades, haga clic en la ficha **aplicación de iOS** y actualizar el **identificador** , el identificador que creó anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. En la ficha **firma de paquete de iOS** , seleccione el correspondiente **identidad** y **perfil de Provisioning** que había establecido para este proyecto. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Esto garantiza que el proyecto utiliza el nuevo perfil para la firma de código. Para el dispositivo oficial de Xamarin provisioning documentación, vea [Aprovisionamiento de dispositivos de Xamarin].

4. Haga doble clic en Info.plist para abrirla y, a continuación, habilitar **RemoteNotifications** en modos de fondo. 



[Dispositivo de Xamarin Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/