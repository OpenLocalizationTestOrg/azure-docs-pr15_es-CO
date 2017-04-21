<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Preguntas más frecuentes: aloja la zona ARPA en DNS de Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>¿Puedo alojar zonas ARPA para Mis bloques IP asignada por el ISP en Azure DNS?
Sí. Alojan las zonas ARPA para sus propios rangos IP en DNS de Azure es totalmente compatible.

Simplemente [crear la zona en el DNS de Azure](dns-getstarted-create-dnszone.md)y luego trabajar con su ISP para [delegar la zona](dns-domain-delegation.md).  A continuación, puede administrar los registros PTR para cada búsqueda inversa en la misma forma que otros tipos de registros.

También puede [importar una zona de búsqueda inversa existente mediante la CLI de Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>¿Cuál es mi costo de zona ARPA de hospedaje?
Aloja la zona ARPA para la IP asignada por el ISP bloque en Azure DNS se cargará a [las tasas de Azure DNS estándar](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>¿Se puede alojar zonas ARPA para direcciones IPv4 e IPv6 en DNS de Azure?
Sí.