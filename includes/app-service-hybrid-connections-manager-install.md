
1. En la hoja de **conexiones híbridas** , haga clic en la conexión híbrido que acaba de crear, haga clic en **El programa de instalación de agente de escucha**.
    
    ![Haga clic en el programa de instalación de agente de escucha](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. Se abre la hoja de **Propiedades de conexión de híbrido** . En **Locales híbrido de Connection Manager**, elija **descargar y configurar manualmente**, guardar el paquete descargado de HybridConnectionManager.msi y copiar la cadena de conexión de puerta de enlace.
    
    ![Haga clic aquí para instalar](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. Desde un símbolo del sistema de administrador, escriba el siguiente comando para iniciar el programa de instalación:

        start HybridConnectionManager.msi
 
7. Después de ejecuta el programa de instalación, haga clic en **ahora no**, entonces vaya a la carpeta %ProgramFiles%\Microsoft\HybridConnectionManager, ejecute HCMConfigWizard.exe y haga clic en **Sí** en el cuadro de diálogo **Control de cuentas de usuario** .
        
7. Pegar la cadena de conexión híbrida que copió anteriormente y haga clic en **Aceptar**. 
    
    ![Instalar](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Cuando haya finalizado, haga clic en **Cerrar**.
    
    ![Haga clic en Cerrar](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    En la hoja de **conexiones híbridas** , la columna **estado** muestra ahora **conectado**. 
    
    ![Estado conectado](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)