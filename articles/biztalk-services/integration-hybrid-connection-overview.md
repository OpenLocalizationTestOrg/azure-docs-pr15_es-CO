<properties
    pageTitle="Introducción a las conexiones híbrido | Microsoft Azure"
    description="Obtenga información sobre conexiones híbridas, seguridad, puertos TCP y las configuraciones admitidas. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Introducción a las conexiones híbrido
Introducción a las conexiones híbrido, enumera las configuraciones admitidas y enumera los puertos TCP necesarios.


## <a name="what-is-a-hybrid-connection"></a>¿Qué es un híbrido de conexión

Conexiones híbridas son una característica de los servicios de BizTalk de Azure. Conexiones híbridas proporcionan una forma fácil y cómoda de conectar las características de aplicaciones Web en Azure App servicio (anteriormente, sitios Web) y aplicaciones móviles en servicio de la aplicación de Azure (anteriormente Servicios de Mobile) a los recursos locales detrás del firewall.

![Conexiones híbridas][HCImage]

Los beneficios de las conexiones híbrido incluyen:

- Aplicaciones Web y aplicaciones móviles pueden tener acceso a datos locales y servicios existentes segura.
- Varias aplicaciones Web o aplicaciones móviles pueden compartir una conexión híbrido para tener acceso a un recurso local.
- Puertos TCP mínima son necesarios para tener acceso a la red.
- Aplicaciones que utilizan conexiones híbridas acceso a sólo el recurso local específico que se publica a través de la conexión híbrida.
- Puede conectarse a cualquier recurso local que utiliza un puerto TCP estático, como SQL Server, MySQL, APIs de Web HTTP y mayoría de los servicios Web personalizados.

    > [AZURE.NOTE] Actualmente no se admiten servicios basados en TCP que utilizan puertos dinámicos (como FTP de modo pasivo o modo pasivo extendido). LDAP también es incompatible. LDAP utiliza un puerto TCP estático, pero también podría utilizar UDP. Como resultado, no se admite.

- Puede utilizarse con todos los marcos de trabajo compatibles con aplicaciones de Web (. NET, PHP, Java, Python, Node.js) y aplicaciones de Mobile (Node.js,. NET).
- Aplicaciones Web y aplicaciones móviles pueden tener acceso a recursos locales exactamente del mismo modo como si se encuentra la Web o la aplicación Mobile de su red local. Por ejemplo, también pueden utilizarse la misma conexión cadena utilizada locales en Azure.


Conexiones híbridas también proporcionan a los administradores de empresa con control y visibilidad sobre el acceso a aplicaciones híbridas, incluidos los recursos corporativos:

- Configuración de directiva de grupo, los administradores pueden permitir conexiones híbrido en la red y también designar recursos que sean accesibles para aplicaciones híbridas.
- Registros de sucesos y auditoría de la red corporativa proporcionan visibilidad de los recursos haciendo conexiones híbridas.


## <a name="example-scenarios"></a>Escenarios de ejemplo

Conexiones híbridas admiten las siguientes combinaciones de framework y aplicaciones:

- Acceso de .NET framework para SQL Server
- Acceso de .NET framework a los servicios HTTP/HTTPS con WebClient
- Acceso PHP a SQL Server, MySQL
- Acceso de Java a SQL Server, MySQL y Oracle
- Acceso de Java a los servicios HTTP/HTTPS

Al utilizar conexiones híbridas para tener acceso a los locales de SQL Server, considere lo siguiente:

- SQL Express denominada instancias debe configurarse para utilizar puertos estáticos. De forma predeterminada, SQL Express instancias con nombre usar puertos dinámicos.
- SQL Express predeterminado instancias utiliza un puerto estático, pero debe estar habilitado TCP. De forma predeterminada, TCP no está habilitado.
- Al utilizar organización por clústeres o grupos de disponibilidad, el `MultiSubnetFailover=true` modo no es compatible actualmente.
- La `ApplicationIntent=ReadOnly` no se admite actualmente.
- Autenticación de SQL puede ser necesaria que el método de autorización end-to-end compatible con la aplicación de Azure y el servidor SQL local.


## <a name="security-and-ports"></a>Seguridad y puertos

Conexiones híbridas utilizan la autorización de la firma de acceso compartido (SAS) para proteger las conexiones de las aplicaciones de Azure y en instalaciones híbridas Connection Manager a la conexión híbrida. Se crean claves de conexión independiente para la aplicación y el Administrador de conexión local híbrido. Estas claves de conexión pueden ser pasadas y revocadas de forma independiente.

Proporcionan conexiones híbrido para la distribución segura y sin problemas de las claves para las aplicaciones y el Administrador de conexión local híbrido.

Consulte [crear y administrar conexiones híbrido](integration-hybrid-connection-create-manage.md).

*Autorización de la aplicación es independiente de la conexión híbrida*. Puede utilizarse cualquier método de autorización adecuada. El método de autorización depende de los métodos de autorización de end-to-end compatibles a través de la nube de Azure y los componentes locales. Por ejemplo, la aplicación de Azure tiene acceso a una de SQL Server local. En este escenario, autorización de SQL puede ser el método de autorización es compatible end-to-end.

#### <a name="tcp-ports"></a>Puertos TCP
Conexiones híbridas requieren conectividad TCP o HTTP saliente de la red privada. No es necesario abrir ningún puerto de servidor de seguridad o cambiar la configuración de red perimetral para permitir cualquier conectividad entrante en la red.

Conexiones híbridas utiliza los siguientes puertos TCP:

Puerto | Por qué necesita
--- | ---
9350 - 9354 | Estos puertos se utilizan para la transmisión de datos. El Administrador de retransmisión del Bus de servicio sondeos 9350 para determinar si hay conectividad TCP del puerto. Si está disponible, se supone que el puerto 9352 también está disponible. Tráfico de datos pase por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se utiliza el puerto 9352 para el tráfico de datos, puerto 5671 se utiliza como el canal de control. <br/><br/>Permitir conexiones salientes a este puerto.
80, 443 | Estos puertos se utilizan para algunas peticiones de datos a Azure. Además, si los puertos 9352 y 5671 no son utilizables, *entonces* los puertos 80 y 443 son los puertos de reserva utilizados para la transmisión de datos y el canal de control.<br/><br/>Permitir conexiones salientes a estos puertos. <br/><br/>**Nota** No se recomienda para utilizar éstos como los puertos de reserva en lugar de los demás puertos TCP. El HTTP/WebSocket se utiliza como el protocolo en lugar de TCP nativo para los canales de datos. Podría resultar en un rendimiento menor.



## <a name="next-steps"></a>Próximos pasos

[Crear y administrar conexiones híbrido](integration-hybrid-connection-create-manage.md)<br/>
[Conectar aplicaciones Web Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Conectarse a local de SQL Server desde una aplicación web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Vea también

[REST API para administrar los servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[Servicios de BizTalk: cuadro de las ediciones](biztalk-editions-feature-chart.md)<br/>
[Crear un BizTalk Service mediante el portal de Azure](biztalk-provision-services.md)<br/>
[Los servicios de BizTalk: Fichas de panel, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
