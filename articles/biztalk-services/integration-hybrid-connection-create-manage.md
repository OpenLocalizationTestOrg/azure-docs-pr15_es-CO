<properties 
    pageTitle="Crear y administrar conexiones híbrido | Microsoft Azure" 
    description="Aprenda a crear una conexión híbrido, administrar la conexión e instalar al híbrido de Connection Manager. MABS, WABS" 
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
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Crear y administrar conexiones híbrido


## <a name="overview-of-the-steps"></a>Resumen de los pasos
1. Crear una conexión híbrida introduciendo el **nombre de host** o **FQDN** del recurso local en la red privada.
2. Vincular sus aplicaciones de Azure web o aplicaciones móviles Azure a la conexión híbrida.
3. Instalar al administrador de conexión híbrido en el recurso local y conectarse a la conexión híbrido específico. El portal de Azure proporciona una experiencia de un solo clic para instalar y conectar.
4. Administrar conexiones híbridas y sus claves de conexión.

Este tema muestra estos pasos. 

> [AZURE.IMPORTANT] Es posible establecer un extremo de conexión híbrido a una dirección IP. Si utiliza una dirección IP, puede o no se puede alcanzar el recurso local, dependiendo de su cliente. La conexión híbrida depende el cliente realiza una búsqueda DNS. En la mayoría de los casos, el __cliente__ es el código de aplicación. Si el cliente no realiza una búsqueda DNS (no intenta resolver la dirección IP como si fuese un nombre de dominio (x.x.x.x)), entonces no se envía tráfico a través de la conexión híbrida.
>
> Por ejemplo (en pseudocódigo), defina **10.4.5.6** como su host local:
> 
> **Funciona de la siguiente situación:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **No funciona el siguiente escenario:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Crear una conexión híbrida

Puede crearse una conexión híbrido en el portal de Azure utilicen aplicaciones Web **o** servicios de BizTalk. 

**Para crear conexiones híbrido con aplicaciones Web**, vea [Conectar aplicaciones Web Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md). También puede instalar el Administrador de conexión híbrida (MCH) desde su aplicación web, que es el método preferido. 

**Para crear conexiones híbrido en servicios de BizTalk**:

1. Iniciar sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 

    Si no tienes un BizTalk Service existentes, puede [crear un servicio de BizTalk](biztalk-provision-services.md).
3. Seleccione la ficha **Conexiones híbridas** :  
![Ficha de conexiones híbrido][HybridConnectionTab]

4. Seleccione **crear una conexión de híbridos** o seleccione el botón **Agregar** en la barra de tareas. Introduzca la siguiente información:

    Propiedad | Descripción
--- | ---
Nombre | El nombre de conexión híbrida debe ser único y no puede ser el mismo nombre que el BizTalk Service. Puede introducir cualquier nombre pero específico con su propósito. Algunos ejemplos son:<br/><br/>Nómina de*SQLServer*<br/>SupplyList*SharepointServer*<br/>Clientes*OracleServer*
Nombre de host | Escriba el nombre de host completo, sólo el nombre de host o la dirección IPv4 del recurso local. Algunos ejemplos son:<br/><br/>mySQLServer<br/>*mySQLServer*. *Dominio*. corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *yourCompany*.com<br/>10.100.10.10<br/><br/>Si utiliza la dirección IPv4, tenga en cuenta que el código de cliente o la aplicación no puede resolver la dirección IP. Vea la nota importante al principio de este tema.
Puerto | Escriba el número de puerto del recurso local. Por ejemplo, si utiliza aplicaciones de Web, escriba el puerto 80 o 443. Si utiliza SQL Server, especifique el puerto 1433.

5. Seleccione la marca de verificación para completar la instalación. 

#### <a name="additional"></a>Adicional

- Se pueden crear varias conexiones híbrido. Consulte la [Servicios de BizTalk: cuadro de las ediciones](biztalk-editions-feature-chart.md) para el número de conexiones permitidas. 
- Cada conexión híbrido se crea con un par de cadenas de conexión: aplicación claves que claves de envío y locales que ESCUCHARÁN. Cada par tiene un principal y una clave secundaria. 


## <a name="LinkWebSite"></a>Vincular su aplicación de Azure de la aplicación de servicio Web o aplicación móvil

Para vincular un Web App o App Mobile en Azure App servicio a una conexión existente de híbrido, seleccione **usar una conexión de híbridos existente** en la hoja de conexiones híbridas. Consulte [acceso a recursos mediante conexiones híbrido en Azure App servicio local](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Instalar el híbrido Connection Manager local

Después de crear una conexión híbrido, instale al administrador de conexión de híbrido en el recurso local. Puede descargarse desde sus aplicaciones de Azure web o desde el BizTalk Service. Pasos de servicios de BizTalk: 

1. Iniciar sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 
3. Seleccione la ficha **Conexiones híbridas** :  
![Ficha de conexiones híbrido][HybridConnectionTab]
4. En la barra de tareas, seleccione **Instalación local**:  
![Instalación local][HCOnPremSetup]
5. Seleccione **instalar y configurar** para ejecutar o descargar el híbrido de Connection Manager en el sistema local. 
6. Seleccione la marca de verificación para iniciar la instalación. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicional
- Híbrido de Connection Manager puede instalarse en los sistemas operativos siguientes:

    - Windows Server 2008 R2 (.NET Framework 4.5 + y Windows Management Framework 4.0 + requerido)
    - Windows Server 2012 (Windows Management Framework 4.0 + requerido)
    - R2 de Windows Server 2012


- Después de instalar al administrador de conexión híbrida, ocurre lo siguiente: 

    - La conexión híbrida alojados en Azure se configura automáticamente para utilizar la cadena de conexión de aplicación principal. 
    - El recurso local se configura automáticamente para utilizar la cadena de conexión local principal.

- Híbrido de Connection Manager debe utilizar una cadena de conexión local válido para la autorización. Las aplicaciones de Azure Web o aplicaciones móviles debe utilizar una cadena de conexión de la aplicación válido para la autorización.
- Puede escalar conexiones híbridas instalando otra instancia del Administrador de conexión híbrido en otro servidor. Configurar el agente de escucha local para utilizar la misma dirección como primer escucha local. En esta situación, el tráfico es (round robin) distribuido aleatoriamente entre los agentes de escucha activa local. 


## <a name="ManageHybridConnection"></a>Administrar conexiones híbrido
Para administrar las conexiones híbrido, puede:

- Utilice el portal de Azure y vaya a su BizTalk Service. 
- Use [las API del resto](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copia o volver a generar las cadenas de conexión híbrida

1. Iniciar sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y, a continuación, seleccione su BizTalk Service. 
3. Seleccione la ficha **Conexiones híbridas** :  
![Ficha de conexiones híbrido][HybridConnectionTab]
4. Seleccione la conexión híbrida. En la barra de tareas, seleccione **Administrar conexión**:  
![Administrar opciones][HCManageConnection]

    **Administrar conexión** enumera las cadenas de conexión de la aplicación y locales. Puede copiar las cadenas de conexión o volver a generar la clave de acceso utilizada en la cadena de conexión. 

    **Si selecciona Regenerar (Regenerate)**, la tecla de acceso compartido utilizado dentro de la cadena de conexión se cambia. Siga este procedimiento:
    - En el portal Azure clásico, seleccione **Teclas de sincronización** en la aplicación de Azure.
    - Vuelva a ejecutar el **Programa de instalación local**. Cuando vuelva a ejecutar el programa de instalación local, el recurso local se configura automáticamente para utilizar la cadena de conexión principal actualizada.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Utilizar Directiva de grupo para controlar los recursos locales usados por una conexión híbrida

1. Descargue las [plantillas administrativas híbrido de Connection Manager](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraer los archivos.
3. En el equipo en el que se modifica la directiva de grupo, haga lo siguiente:  

    - Copia el. Archivos ADMX en la carpeta *%WINROOT%\PolicyDefinitions* .
    - Copia el. Archivos ADML a la carpeta *%WINROOT%\PolicyDefinitions\en-us* .

Una vez copiado, puede utilizar el Editor de directiva de grupo para cambiar la directiva.




## <a name="next"></a>Siguiente

[Conectar aplicaciones Web Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Conectarse a local de SQL Server desde aplicaciones Web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Introducción a las conexiones híbrido](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Vea también

[REST API para administrar los servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Los servicios de BizTalk: Gráfico de las ediciones](biztalk-editions-feature-chart.md)  
[Crear un BizTalk Service mediante Azure portal clásico](biztalk-provision-services.md)  
[Los servicios de BizTalk: Fichas de panel, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
