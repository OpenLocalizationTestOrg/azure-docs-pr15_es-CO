## <a name="what-is-reverse-dns"></a>¿Qué es DNS inversa?

Registros DNS convencionales permiten una asignación de un nombre DNS (por ejemplo, "www.contoso.com") a una dirección IP (por ejemplo, 64.4.6.100).  DNS inversa permite la traducción de una dirección IP (64.4.6.100) por un nombre ('www.contoso.com').

Se utilizan registros DNS inversos en una variedad de situaciones. Por ejemplo, los registros de DNS inverso son ampliamente utilizados en la lucha contra el correo basura al comprobar el remitente de un mensaje de correo electrónico.  El servidor de correo receptor recuperará el registro DNS inverso de la dirección IP del servidor envío y comprobar si ese host está autorizado a enviar correo electrónico desde el dominio de origen. (Tenga en cuenta sin embargo que [Azure Compute servicios no admiten el envío de correo electrónico a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Funciona de forma inversa DNS

Registros de DNS inverso se alojan en las zonas DNS especiales, conocidas como zonas 'ARPA'.  Estas zonas forman una jerarquía DNS independiente en paralelo con la jerarquía normal de alojar dominios como 'contoso.com'.

Por ejemplo, el registro DNS 'www.contoso.com' se implementa mediante un registro DNS 'A' con el nombre "www" en la zona contoso.com.  Este registro señala a la dirección IP correspondiente, en este caso 64.4.6.100.  La búsqueda inversa se implementa por separado, con un registro de 'PTR' llamado '100' en la zona de '6.4.64.in-addr.arpa' (Observe que las direcciones IP se invierten en zonas ARPA).  Este registro PTR, si se ha configurado correctamente, señala el nombre www.contoso.com.

Cuando una organización se le asigna un bloque de direcciones IP, adquieren el derecho de administrar la zona ARPA correspondiente. Las zonas ARPA correspondientes a los bloques de direcciones IP utilizados por Azure son alojadas y administradas por Microsoft. Su ISP puede alojar la zona ARPA para sus propias direcciones IP automáticamente o puede permitir que alojan la zona ARPA en un servicio DNS de su elección, como DNS de Azure.

>[AZURE.NOTE] Búsquedas DNS directas y las búsquedas DNS inversas se implementan en jerarquías DNS independientes y en paralelo. La búsqueda inversa de 'www.contoso.com' **no** alojado en la zona de 'contoso.com', en su lugar se aloja en la zona ARPA para el bloque de direcciones IP correspondiente.

Para obtener más información acerca de DNS inversa, consulte [Búsqueda de DNS inversa](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Compatibilidad con Azure DNS inversa

Azure admite dos escenarios independientes relativas a DNS inverso:

1. Aloja la zona ARPA correspondiente a su bloque de direcciones IP.
2. Lo que le permite configurar el registro DNS inverso para la dirección IP asignada al servicio de Azure.

Para admitir el DNS anterior, Azure puede utilizarse para alojar las zonas ARPA y gestionan los registros PTR para cada búsqueda inversa de DNS.  El proceso de creación de la zona ARPA, configurar la delegación y configurar los registros PTR es el mismo que para las zonas DNS normales.  Las únicas diferencias son que la delegación debe configurarse a través de su ISP en lugar de tu entidad de registro DNS, y se debe utilizar el tipo de registro PTR.

Para admitir este último, Azure permite configurar la búsqueda inversa de las direcciones IP asignadas a su servicio.  Esta búsqueda inversa se configura de Azure como un registro PTR en la zona correspondiente de ARPA.  Estas zonas ARPA, correspondientes a todos los rangos IP utilizados por Azure, alojadas por Microsoft. **El resto de este artículo describe este escenario en detalle.**
