## <a name="about-records"></a>Acerca de los registros

Cada registro DNS tiene un nombre y un tipo. Los registros se organizan en varios tipos según los datos que contienen. El tipo más común es un registro "A", que asigna un nombre a una dirección IPv4. Otro tipo es un registro "MX", que asigna un nombre a un servidor de correo.

Azure DNS admite todos los habituales tipos de registro DNS, incluyendo A, AAAA, CNAME, MX, NS, PTR, SOA, SRV y TXT. Tenga en cuenta que:
- Conjuntos de registros SOA se crean automáticamente con cada zona, no se puede crear por separado.
- Registros de SPF deben crearse mediante el tipo de registro TXT. Para obtener más información, vea [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

En DNS de Azure, los registros se especifican mediante nombres relativos. Un "" nombre de dominio (completo FQDN) incluye el nombre de la zona, pero no un nombre "relativo". Por ejemplo, el nombre de registro relativo "www" en la zona "contoso.com" da el nombre de registro completo www.contoso.com.

## <a name="about-record-sets"></a>Acerca de los conjuntos de registros

A veces necesitará crear más de un registro DNS con un nombre y tipo dados. Por ejemplo, supongamos que está alojado el sitio web de "www.contoso.com" en dos direcciones IP diferentes. El sitio Web requiere dos diferentes registros, uno para cada dirección IP. Éste es un ejemplo de un conjunto de registros:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS administra los registros DNS utilizando conjuntos de registros. Un conjunto de registros es la colección de registros DNS en una zona que tienen el mismo nombre y son del mismo tipo. La mayoría de conjuntos de registros contienen un registro único, pero ejemplos como éste, en el que un conjunto de registros contiene más de un registro, no son infrecuentes.

Conjuntos de registros CNAME y de SOA son excepciones. Los estándares DNS no permiten varios registros con el mismo nombre para estos tipos.

El tiempo de vida o TTL, especifica cuánto tiempo cada registro se almacena en caché los clientes antes de que se vuelva a consulta. En este ejemplo, el TTL es 3600 segundos o 1 hora. El TTL se especificó para el conjunto de registros, no para cada registro, por lo que se utiliza el mismo valor para todos los registros dentro de ese conjunto de registros.

#### <a name="wildcard-record-sets"></a>Conjuntos de registros de comodín

Azure DNS admite [registros de comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Éstas son devueltas para cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más cercana de un conjunto de registros no sean comodín). Conjuntos de registros de comodín son compatibles con todos los tipos de registro excepto NS y SOA.  

Para crear un conjunto de registros de comodín, utilice el nombre de conjunto de registros "\*". O bien, utilice un nombre con la etiqueta "\*", por ejemplo,"\*.foo".

#### <a name="cname-record-sets"></a>Conjuntos de registros CNAME

Conjuntos de registros CNAME no pueden coexistir con otros conjuntos de registros con el mismo nombre. Por ejemplo, no puede crear un registro CNAME con el "www" del nombre relativo y un registro con el nombre relativo "www" al mismo tiempo. Porque el vértice de la zona (nombre = ‘@’) siempre contiene el NS y SOA que se crearon cuando se creó la zona de registro, no se puede crear un registro CNAME que se establezca en el vértice de la zona. Estas restricciones surgen de los estándares de DNS y no son limitaciones de DNS de Azure.
