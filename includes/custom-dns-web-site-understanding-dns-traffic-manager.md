El sistema de nombres de dominio (DNS) se utiliza para encontrar cosas en internet. Por ejemplo, cuando escribe una dirección en su explorador, o haga clic en un vínculo en una página web, utiliza DNS para traducir el dominio en una dirección IP. La dirección IP es algo así como una dirección, pero no es muy humano descriptivo. Por ejemplo, es mucho más fácil recordar un nombre DNS como **contoso.com** lo es recordar una dirección IP, como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en *registros*. Los registros asocian un determinado *nombre*, como **contoso.com**, con otro nombre DNS o una dirección IP. Cuando una aplicación, como un explorador web, busca un nombre en DNS, busca el registro y se utiliza lo señala a la dirección. Si el valor al que señala es una dirección IP, el explorador utilizará ese valor. Si señala a otro nombre DNS, la aplicación debe realizar resolución otra vez. En última instancia, la resolución de nombres terminará en una dirección IP.

Cuando se crea un sitio Web de Azure, se asigna automáticamente un nombre DNS para el sitio. Este nombre tiene el formato de ** &lt;yoursitename&gt;. azurewebsites.net**. Cuando se agrega el sitio Web como un extremo de Azure Traffic Manager, su sitio Web es accesible a través de la ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** dominio.

> [AZURE.NOTE] Cuando su sitio Web está configurado como un extremo Traffic Manager, utilice la **. trafficmanager.net** de direcciones al crear registros DNS.

> Sólo puede usar los registros CNAME con Traffic Manager

También hay varios tipos de registros, cada uno con sus propias funciones y limitaciones, pero para los sitios Web configurado para como extremos Traffic Manager, nos sólo ocupa aproximadamente uno; Registros *CNAME* .

###<a name="cname-or-alias-record"></a>Registro CNAME o Alias

Un registro CNAME asigna un nombre DNS *específico* , por ejemplo, **mail.contoso.com** o **www.contoso.com**, a otro nombre de dominio (canónico). En el caso de los sitios Web de Azure usando Traffic Manager, el nombre de dominio canónico es el ** &lt;myapp >. trafficmanager.net** nombre de dominio de su perfil de Traffic Manager. Una vez creado, el registro CNAME crea un alias para la ** &lt;myapp >. trafficmanager.net** nombre de dominio. La entrada CNAME resolverá con la dirección IP de su ** &lt;myapp >. trafficmanager.net** nombre de dominio automáticamente, por lo que si cambia la dirección IP del sitio Web, no tiene que realizar ninguna acción.

Una vez que el tráfico llega a Traffic Manager, a continuación, enruta el tráfico a tu sitio Web, con el que esté configurado para el método de equilibrio de carga. Esto es completamente transparente para los visitantes de su sitio Web. Sólo verá el nombre de dominio personalizado en su explorador.

> [AZURE.NOTE] Algunos registradores de dominio sólo permiten asignar subdominios al utilizar un registro CNAME, como **www.contoso.com**y no nombres de raíz, como **contoso.com**. Para obtener más información sobre registros CNAME, consulte la documentación proporcionada por el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia en registro CNAME</a>o el documento de <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - implementación y especificación</a> .
