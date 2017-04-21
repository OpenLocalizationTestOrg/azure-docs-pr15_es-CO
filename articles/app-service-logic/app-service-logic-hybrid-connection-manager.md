<properties 
    pageTitle="Mediante el Administrador de conexión híbrida | Microsoft Azure" 
    description="Instalar y configurar el híbrido de Connection Manager y conectar a los conectores locales en aplicaciones de lógica" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Conectar a los conectores locales mediante el Administrador de conexión híbrida

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema de lógica apps 2014-12-01-vista previa. Lógica Apps de la disponibilidad general (GA) utiliza una puerta de enlace para la conectividad local. Más información acerca de la nueva [puerta de enlace](app-service-logic-gateway-connection.md) y de [Lógica de aplicaciones GA](https://azure.microsoft.com/documentation/services/logic-apps/).

Para utilizar un sistema local, lógica de aplicaciones utiliza híbrido de Connection Manager. Algunos conectores pueden conectarse a un sistema local, como SQL Server, SAP, SharePoint y así sucesivamente. 

El Administrador de conexión híbrida (MCH) es un clic-installer, una vez que se instala en un servidor IIS dentro de la red detrás del servidor de seguridad. Con una retransmisión de Bus de servicios de Azure, MCH autentica el sistema local con el conector en Azure. 

> [AZURE.NOTE] Híbrido de Connection Manager sólo es necesario si se está conectando a un recurso local detrás del servidor de seguridad. Si no se conecta a un sistema local, entonces no es necesario el Administrador de conexión híbrida.

Para empezar, necesita:

- Azure Service Bus retransmisión namespace SAS cadena de conexión. Ver [Tarifas de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/) para determinar qué nivel incluye retransmisiones.
- Inicio de sesión en información del sistema, incluyendo el nombre de usuario y la contraseña local. Por ejemplo, si se conecta a una de SQL Server local, necesita la cuenta de inicio de sesión de SQL Server y la contraseña.
- Información del servidor, incluido el puerto número y nombre de servidor local. Por ejemplo, si se conecta a una de SQL Server local, necesita el nombre de SQL Server y el número de puerto TCP.

## <a name="get-the-service-bus-connection-string"></a>Obtener la cadena de conexión de Bus de servicio

En el portal de Azure, copiar la cadena de conexión de SAS de raíz de Bus de servicio. Esta cadena de conexión conecta el conector de Azure en el sistema local. 

1. En el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), seleccione el espacio de nombres del Bus de servicio y seleccione **Información de conexión**:

    ![][SB_ConnectInfo]

2. Copie la cadena de conexión de SAS:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Instalar al administrador de conexión híbrida

1. En el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), seleccione el conector que ha creado. Para abrirlo, seleccione **Examinar**, seleccione **API aplicaciones**y, a continuación, seleccione el conector o API App. 
<br/><br/>
En **Conexión híbrido**, el programa de instalación está **incompleta**:
<br/>
![][2] 

2. Seleccione **conexión híbrida**. Se muestra la cadena de conexión de Bus de servicio especificada previamente.
3. Copie la **cadena de configuración principal**:
<br/>
![][PrimaryConfigString]

4. En **Locales híbrido de Connection Manager**, puede descargar el Administrador de conexión de híbridos o instalarlo directamente desde el portal. 
<br/><br/>
Para instalar directamente desde el portal, vaya a su servidor IIS local, vaya al portal y seleccione **descargar y configurar**.
<br/><br/>
Para descargar el híbrido de Connection Manager, vaya a su servidor IIS local y vaya a la **aplicación de ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). La instalación se inicia automáticamente, por lo que puede ejecutar.

5. En la ventana de **Configuración de agente de escucha** , escriba la **Cadena de configuración principales** que se pegó anteriormente (en el paso 3) y seleccione **instalar**.

Cuando la configuración está completa, la muestra siguiente:
<br/>
![][3] 

Ahora cuando vaya al conector nuevo, el estado de conexión híbrido es **conectado**. Tendrá que cerrar el conector y vuelva a abrirlo:
<br/>
![][4] 

> [AZURE.NOTE] Para pasar a la cadena de conexión secundaria, volver a ejecutar la instalación de conexión híbrida y escriba la **Cadena de configuración secundaria**.


## <a name="tcp-ports-and-security"></a>Los puertos TCP y seguridad

Cuando se crea una conexión híbrido, se crea un sitio Web en el servidor IIS local local. Puede ser el servidor IIS en una DMZ. Los requisitos de puerto TCP en el servidor IIS incluyen:

Puerto TCP | ¿Por qué
--- | ---
 | No hay puertos TCP entrantes son necesarios.
9350 - 9354 | Estos puertos se utilizan para la transmisión de datos. El Administrador de retransmisión del Bus de servicio sondeos 9350 para determinar si hay conectividad TCP del puerto. Si está disponible, se supone que el puerto 9352 también está disponible. Tráfico de datos pase por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se utiliza el puerto 9352 para el tráfico de datos, puerto 5671 se utiliza como el canal de control. <br/><br/>Permitir conexiones salientes a este puerto. 
80, 443 | Si no se puede utilizar puertos 9352 y 5671, *entonces* los puertos 80 y 443 son los puertos de reserva utilizados para la transmisión de datos y el canal de control.<br/><br/>Permitir conexiones salientes a estos puertos.
Puerto del sistema de prem | En el sistema local, abra el puerto utilizado por el sistema. Por ejemplo, SQL Server utiliza normalmente el puerto 1433. Abra este puerto TCP.

[Detrás de un Firewall con Bus de servicios de hospedaje](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Solución de problemas

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Solución de problemas en instalaciones

1. En el servidor IIS, confirme el rol web IIS está instalado y se inician todos los servicios IIS.
2. En el servidor IIS, confirme el híbrido de Connection Manager está instalado y ejecutándose:
 - En el Administrador de IIS (inetmgr), el sitio Web de ***MicrosoftAzureBizTalkHybridListener*** debe aparecer y estar en ejecución. 
 - Este sitio Web utiliza el ***HybridListenerAppPool*** que se ejecuta como la cuenta de usuario locales integradas de *NetworkService* . También se debe iniciar este AppPool.
3. En el servidor IIS, confirme que el conector está instalado y ejecutándose: 
 - Se crea un sitio Web para el conector. Por ejemplo, si ha creado un conector SQL, hay un sitio Web de ***MicrosoftSqlConnector_nnn*** . En el Administrador de IIS (inetmgr), confirme que este sitio Web se enumeran y se inicia. 
 - Este sitio Web utiliza su propio grupo de aplicaciones de IIS denominado ***HybridAppPoolnnn***. Este AppPool se ejecuta como la cuenta de usuario locales integradas de *NetworkService* . Deben ser iniciado este sitio Web y AppPool. 
 - Busque el conector local. Por ejemplo, si el sitio Web de conector utiliza el puerto 6569, vaya a http://localhost:6569. Un documento predeterminado no está configurado para un `HTTP Error 403.14 - Forbidden error` se espera.
4. En el servidor de seguridad, confirme están abiertos los puertos TCP que se muestran en este tema.
5. Mire el sistema de origen o de destino:
 - Algunos sistemas locales requieren archivos de dependencia adicional. Por ejemplo, si se conecta a SAP local, algunos archivos adicionales de SAP deben instalarse en el servidor IIS.
 - Compruebe la conectividad con el sistema con la cuenta de inicio de sesión. Por ejemplo, el puerto TCP utilizado por el sistema debe ser abierto, como el puerto 1433 para SQL Server. La cuenta de inicio de sesión especificada en el portal de Azure debe tener acceso al sistema.
6. En el servidor IIS, compruebe los registros de sucesos para buscar errores. 
7. Limpieza y volver a instalar el Administrador de conexión híbrida: 
 - En IIS, elimine manualmente el sitio Web de conector y su grupo de aplicaciones. 
 - Vuelva a ejecutar el Administrador de conexión híbrida y confirme que entra la correcta **Principal cadena de configuración** para el conector.



### <a name="in-the-azure-classic-portal"></a>En el portal Azure clásico

1. Compruebe que el espacio de nombres del Bus de servicio tiene un estado **activo** .
2. Al crear el conector, escriba la cadena de conexión de servicio Bus SAS. No escriba la cadena de conexión de ACS.


## <a name="faq"></a>Preguntas más frecuentes

**Pregunta**: hay dos administradores de conexión híbrida. ¿Cuál es la diferencia? 

**Respuesta**: la tecnología de [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) que se utiliza principalmente (anteriormente en sitios Web) de aplicaciones Web y aplicaciones móviles (servicios móviles anteriormente) para conectarse a local no hay. Este administrador de conexiones híbrido es su propio [programa de instalación](../biztalk-services/integration-hybrid-connection-create-manage.md) y utilice un servicio de BizTalk Azure (segundo plano). Admite únicamente los protocolos TCP y HTTP.

Con conectores de servicio de la aplicación de Azure, también tenemos un híbrido de Connection Manager.  El Administrador de conexión de este híbrido realiza *no* uso un Azure BizTalk Service (segundo plano) y admite más de los protocolos TCP y HTTP. Consulte la [lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md).

Ambos utilizan Bus de servicios de Azure para conectarse al sistema local.

**¿Pregunta**: cuando creo un App API personalizada, puedo utilizar el Administrador de conexión de la aplicación servicio híbrido para conectar a local? 

**Respuesta**: no en el sentido tradicional. Puede utilizar un conector integrado, configurar el Administrador de conexión de la aplicación servicio híbrido para conectarse al sistema local. A continuación, utilizar este conector con tu App API personalizada, posiblemente mediante una lógica de la aplicación. Actualmente, no se puede desarrollar o crear su propias API App híbrida (por ejemplo, el conector SQL o el conector de archivos).

Si la API personalizada utiliza un puerto TCP o HTTP, puede utilizar [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y su híbrido de Connection Manager. En este escenario, se utiliza un servicio de BizTalk de Azure. Puede ayudarle a [conectarse a local de SQL Server desde una aplicación web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) .  


## <a name="read-more"></a>Leer más

[Supervisar las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
