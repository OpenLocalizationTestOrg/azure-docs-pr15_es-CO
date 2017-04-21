<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Preguntas más frecuentes: DNS inversa para la dirección IP asignada de Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>¿Cuánto invertir costo de registros DNS?
¡Son gratis!  No hay ningún costo adicional para registros de DNS inverso o consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>¿Mis registros DNS inversas resolverá desde internet?
Sí. Una vez establecida la propiedad inversa de DNS para el servicio de nube, Azure administra todas las delegaciones DNS y las zonas DNS necesarias para asegurarse de que se resuelve el registro de DNS inverso para todos los usuarios de internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>¿Se creará un registro DNS inverso predeterminado para los servicios de nube?
No. DNS inversa es una característica opcional. Si decide no configurar ellos, no se crean registros DNS inversos predeterminado.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>¿Qué es el formato para el nombre de dominio completo (FQDN)?
FQDN se especifica en orden directo y debe terminarse con un punto (por ejemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>¿Qué ocurre si fallan las comprobaciones de validación para el DNS inverso he especificado?
Donde se comprueba la validación de DNS inversa fallan, se producirá un error en la operación de administración del servicio. Por favor, corrija el valor DNS inverso según sea necesario y vuelva a intentar.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>¿Puedo administrar DNS inversa para mi sitio Web de Azure?
DNS inversa no se admite para los sitios Web de Azure. DNS inversa es compatible con funciones de Azure PaaS y máquinas virtuales de IaaS.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>¿Puedo configurar varios registros DNS inversos para mi servicio de nube?
No. Azure es compatible con un solo registro DNS inverso para cada servicio de nube de Azure. Sin embargo, cada servicio de nube Azure puede tener su propio registro DNS inverso.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>¿Puedo enviar mensajes de correo electrónico a dominios externos de Mis servicios de Azure Compute?
No. [Servicios de azure Compute no admiten el envío mensajes de correo electrónico a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
