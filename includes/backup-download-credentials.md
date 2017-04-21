## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Utilizar las credenciales de bóveda para autenticarse con el servicio de copia de seguridad de Azure

El servidor local (el cliente de Windows o servidor de Windows Server o Data Protection Manager) necesita autenticarse con un depósito de copia de seguridad antes de que puede copiar los datos en Azure. La autenticación se logra utilizando "bóveda credenciales". El concepto de credenciales de depósito es similar al concepto de un archivo de "configuración de publicación" que se utiliza en PowerShell de Azure.

### <a name="what-is-the-vault-credential-file"></a>¿Qué es el archivo de credenciales de la caja fuerte?

El archivo de credenciales de depósito es un certificado generado por el portal para cada depósito de copia de seguridad. El portal, a continuación, carga la clave pública para el servicio de Control de acceso (ACS). La clave privada del certificado estará disponible para el usuario como parte del flujo de trabajo que se proporciona como entrada del flujo de trabajo de registro de la máquina. Esto autentica el equipo para enviar los datos de backup a un depósito identificado en el servicio de copia de seguridad de Azure.

La credencial de depósito sólo se utiliza durante el flujo de trabajo del registro. Es responsabilidad del usuario asegurarse de que el archivo de credenciales de la caja fuerte no está en peligro. Si cae en manos de cualquier usuario malintencionado, el archivo de credenciales de bóveda puede utilizarse para registrar otros equipos con el mismo depósito. Sin embargo, como los datos de backup se cifran mediante una frase de contraseña que pertenece al cliente, los datos de copia de seguridad existentes no pueden estar en peligro. Para mitigar este problema, se establecen las credenciales de bóveda caducará en 48 horas. Puede descargar cualquier número de veces: las credenciales de la caja fuerte de un depósito de copia de seguridad, pero sólo el último archivo de credenciales de bóveda es aplicable durante el flujo de trabajo del registro.

### <a name="download-the-vault-credential-file"></a>Descargue el archivo de credenciales de depósito

El archivo de credenciales de depósito se descarga a través de un canal seguro desde el portal de Azure. El servicio de copia de seguridad de Azure no tiene constancia de la clave privada del certificado y la clave privada no se conserva en el portal o el servicio. Utilice los pasos siguientes para descargar el archivo de credenciales de la caja fuerte a un equipo local.

1.  Iniciar sesión en el [Portal de administración](https://manage.windowsazure.com/)
2.  Haga clic en **Servicios de recuperación** en el panel de navegación de la izquierda y seleccione el depósito de copia de seguridad que ha creado. Haga clic en el icono de nube para llegar a la vista de inicio rápido de la caja fuerte de copia de seguridad.

    ![Vista rápida](./media/backup-download-credentials/quickview.png)

3.  En la página de inicio rápido, haga clic en **credenciales de descarga vault**. El portal genera el archivo de credenciales de la caja fuerte, que está disponible para su descarga.

    ![Descargar](./media/backup-download-credentials/downloadvc.png)

4.  El portal genera una credencial de depósito mediante una combinación de nombre de depósito y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de la caja fuerte en la carpeta de descargas de la cuenta local o seleccione Guardar como en el menú Guardar para especificar una ubicación para las credenciales de la caja fuerte.

### <a name="note"></a>Nota
- Asegúrese de que las credenciales de la caja fuerte se guarda en una ubicación que puede tener acceso desde su equipo. Si se almacena en un recurso compartido de archivo/SMB, compruebe los permisos de acceso.
- El archivo de credenciales de depósito sólo se utiliza durante el flujo de trabajo del registro.
- El archivo de credenciales de depósito caduca después de 48 horas y puede descargarse desde el portal.
- Hacer referencia a la copia de seguridad de Azure [FAQ](../articles/backup/backup-azure-backup-faq.md) para cualquier duda sobre el flujo de trabajo.
