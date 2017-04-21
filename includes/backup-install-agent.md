## <a name="download-install-and-register-the-azure-backup-agent"></a>Descargar, instalar y registrar al agente de copia de seguridad de Azure

Después de crear la caja fuerte de la copia de seguridad de Azure, debe instalarse un agente en cada uno de los equipos de Windows (Windows Server, el cliente de Windows, servidor de System Center Data Protection Manager o la máquina del servidor de copia de seguridad de Azure) que permite hacer copia de seguridad de datos y aplicaciones de Azure.

1. Iniciar sesión en el [Portal de administración](https://manage.windowsazure.com/)

2. **Servicios de recuperación**, haga clic en Seleccione el depósito de copia de seguridad que se desea registrar con un servidor. Aparece la página de inicio rápido para ese depósito de copia de seguridad.

    ![Inicio rápido](./media/backup-install-agent/quickstart.png)

3. En la página de inicio rápido, haga clic en la opción **de Windows Server o System Center Data Protection Manager o el cliente de Windows** de **Descarga del agente**. Haga clic en **Guardar** para copiarlo en el equipo local.

    ![Guardar el agente](./media/backup-install-agent/agent.png)

4. Una vez que el agente está instalado, haga doble clic en MARSAgentInstaller.exe para iniciar la instalación del agente de copia de seguridad de Azure. Elija la carpeta de instalación y la carpeta temporal requerido para el agente. La ubicación de caché especificada debe tener espacio libre que es al menos el 5% de los datos de backup.

5.  Si utiliza un servidor proxy para conectarse a internet, en la pantalla de **Configuración del servidor Proxy** , escriba los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, introduzca los detalles del nombre y la contraseña de usuario en esta pantalla.

6.  El agente de copia de seguridad de Azure instala.NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.

7.  Una vez que el agente está instalado, haga clic en el botón **Continuar al registro** para continuar con el flujo de trabajo.

    ![Registrar](./media/backup-install-agent/register.png)

8. En la pantalla de credenciales vault, busque y seleccione el archivo de credenciales de depósito que se descargó anteriormente.

    ![Credenciales de depósito](./media/backup-install-agent/vc.png)

    El archivo de credenciales de depósito sólo es válido para 48 horas (después de descargarlo desde el portal). Si se produce cualquier error en esta pantalla (por ejemplo "archivo de credenciales de bóveda proporcionado ha caducado"), iniciar una sesión en el portal de Azure y descarga de nuevo el archivo de credenciales de la caja fuerte.

    Asegúrese de que el archivo de credenciales de depósito está disponible en una ubicación que se puede acceder mediante la aplicación de instalación. Si encuentra errores relacionados de acceso, copie el archivo de credenciales de bóveda en una ubicación temporal en este equipo y vuelva a intentar la operación.

    Si se produce un error de credenciales de caja fuerte no válido (por ejemplo, "credenciales de la caja fuerte no válido proporcionadas") el archivo está dañado o no dispone de las últimas credenciales asociadas con el servicio de recuperación. Vuelva a intentar la operación tras descargar un nuevo archivo de credenciales de depósito desde el portal. Este error suele aparecer si el usuario hace clic en la opción **credenciales de bóveda de descarga** en el portal de Azure, en una sucesión rápida. En este caso, sólo el segundo archivo de credenciales de bóveda es válido.

9. En la pantalla de **configuración de cifrado** , puede generar una frase de contraseña o proporcione una frase de contraseña (mínimo de 16 caracteres). Recuerde guardar la contraseña en un lugar seguro.

    ![Cifrado](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Si la contraseña está perdida u olvidada; Microsoft no puede ayudar a recuperar los datos de backup. El usuario posee la contraseña de cifrado y Microsoft no tiene visibilidad en la frase de contraseña utilizada por el usuario final. Guarde el archivo en una ubicación segura como se requiere, durante una operación de recuperación.

10. Una vez que hace clic en el botón **Finalizar** , el equipo se ha registrado correctamente en el depósito y ahora está listo para empezar a copias de seguridad Microsoft Azure.

11. Cuando utiliza copia de seguridad de Microsoft Azure independiente puede modificar la configuración que especificó durante el flujo de trabajo de registro haciendo clic en la opción de **Cambiar las propiedades** en el complemento mmc de copias de seguridad de Azure.

    ![Cambiar propiedades](./media/backup-install-agent/change.png)

    O bien, al utilizar Data Protection Manager, puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Configurar** seleccionando **en línea** en la ficha **administración** .

    ![Configurar copia de seguridad de Azure](./media/backup-install-agent/configure.png)
