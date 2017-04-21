<properties 
    pageTitle="Configurar el panel Monitor, escala, híbrido conexiones en servicios de BizTalk y | Microsoft Azure" 
    description="Obtenga información acerca de los controles y supervisar el rendimiento en las fichas clásicas de portal de servicios de BizTalk: escritorio, Monitor, escala, configurar y conexiones híbridas. MABS, WABS" 
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
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Revisar las fichas del panel Monitor, escala, configurar y conexión híbrida

Después de crear su BizTalk Service e implementar su aplicación, puede cambiar algunas de las opciones de BizTalk Service y supervisar el rendimiento de la aplicación. 

Cuando se abre el portal Azure clásico, se colocan automáticamente en la ficha **Todos los elementos** . Para ver su BizTalk Service, seleccione su BizTalk Service en la ficha **Todos los elementos** o seleccione la ficha **Servicios de BIZTALK** ; y, a continuación, seleccione el nombre de BizTalk Service.

Esto abre una nueva ventana con las siguientes fichas. Este tema describe estas fichas.

## <a name="quick-start-quick-startquickstart"></a>Inicio rápido)![Inicio rápido][QuickStart])
Según la edición de los servicios de BizTalk, no estén disponibles todas las opciones enumeradas. 
<table border="1">
    <tr>
        <td><strong>Obtenga las herramientas</strong></td>
        <td>Descargue el SDK de BizTalk Services para instalar las plantillas de proyecto de Visual Studio en el equipo de desarrollo local. Estas plantillas crean los <strong>Servicios de BizTalk</strong> (puente) y los proyectos de Visual Studio de <strong>Artefactos de servicio de BizTalk</strong> (transformación) que se implementan en su BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalar el SDK de Azure BizTalk Services</a> enumera los pasos necesarios para empezar a trabajar.
        </td>
    </tr>
    <tr>
        <td><strong>Crear acuerdos de socios comerciales</strong></td>
        <td>Se abre el Portal de servicios de Azure BizTalk alojado en Azure donde agregar socios y crear X12, AS2 y acuerdos de EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes de mensajería de EDI en BizTalk Services Portal</a> enumera los pasos necesarios para empezar a trabajar.
        </td>
    </tr>

<tr>
        <td><strong>Más información acerca de los servicios de BizTalk</strong></td>
        <td>Visite el <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centro de aprendizaje</a> para obtener más información acerca de los servicios de BizTalk de Azure.</td>
</tr>
</table>


En la barra de tareas en la parte inferior, se puede:

<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de aplicaciones</td>
<td>Se abre el portal de servicios de BizTalk de Azure. El Portal de servicios de BizTalk es la entrada a la configuración de EDI, incluida la adición de socios y crear X12, AS2 y acuerdos de EDIFACT.
<br/><br/>
Esto es lo mismo que <strong>crear acuerdos de socio</strong> en la ficha <strong>Inicio rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes de mensajería de EDI en BizTalk Services Portal</a> proporciona más información sobre el Portal de servicios de BizTalk.</td>
</tr>

<tr>
<td><strong>Información de conexión de</strong> del Namespace de Control de acceso</td>
<td>Cuando se selecciona la información de conexión, se muestran el Namespace de Control de acceso, emisor predeterminado y clave predeterminada. Puede copiar estos valores.
<br/><br/>
También puede abrir el Portal de Control de acceso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Crear un control de acceso Namespace</a> proporciona más información sobre el Portal de Control de acceso.</td>
</tr>

<tr>
<td><strong>Sincronizar las teclas</strong> en la cuenta de almacenamiento</td>
<td>Cuando se crea una cuenta de almacenamiento, se crean automáticamente una clave principal y clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. Su BizTalk Service utiliza automáticamente la clave principal. <strong>Claves de sincronización</strong> permiten a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir el BizTalk Service.
<br/><br/>
Por ejemplo, desea que el BizTalk Service para utilizar una clave principal nueva para la cuenta de almacenamiento. Para hacer esto:
<br/><br/>
<ol>
<li>Seleccione su BizTalk Service y <strong>Las claves de la sincronización</strong>. Seleccione la clave secundaria. Al hacerlo, se inicia el BizTalk Service utilizando la clave secundaria.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave principal. Recuerde que su BizTalk Service utiliza la clave secundaria.</li>
<li>Seleccione su BizTalk Service y <strong>Las claves de la sincronización</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que se hayan regenerado.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se denomina "claves dinámicas". El propósito es permitir a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir el BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Cuando se selecciona eliminar su BizTalk Service y se quitan todos los elementos implementados en él.</td>
</tr>
</table>


## <a name="dashboard"></a>Tablero de mandos
Según la edición de los servicios de BizTalk, no estén disponibles todas las opciones enumeradas. 

Cuando se selecciona el nombre de BizTalk Service, se muestra la ficha del tablero de mandos. En el tablero de mandos, puede:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Resumen de uso: Muestra el número de conexiones híbrido usadas
También muestra el uso de datos en GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico de métrica: Muestra una lista fija de métricas de performance
Estas métricas proporcionan valores en tiempo real con respecto a la salud de la BizTalk Service. También puede elegir el intervalo de tiempo de **intervalo** de las estadísticas que se muestran en el gráfico y de los valores **relativos** o **absolutos** . 

Para obtener una descripción de estas métricas de rendimiento, vaya a [Estadísticas disponibles](#Metrics) en este tema.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Vistazo rápido: Muestra las propiedades de BizTalk Service

<table border="1">

<tr>
<td><strong>Actualizar las credenciales de la base de datos de seguimiento</strong></td>
<td>Cambia el nombre de usuario y la contraseña que utiliza para iniciar sesión en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Actualizar el certificado SSL</strong></td>
<td>Puede actualizar el BizTalk Service para utilizar un certificado SSL diferente. Un certificado SSL autofirmado se crea automáticamente al <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">crear el servicio de BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Descargar certificado</strong></td>
<td>Puede descargar el certificado SSL utilizado su BizTalk Service a un equipo local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Muestra el estado actual de su BizTalk Service. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Servicios de BizTalk: gráfico de estado de servicio</a>. </td>
</tr>
<tr>
<td><strong>Dirección URL del servicio</strong></td>
<td>La dirección URL para el servicio de BizTalk. Este es el mismo, como la <strong>Dirección URL del dominio</strong> especificado cuando se crea el BizTalk Service.</td>
</tr>
<tr>
<td><strong>Dirección pública IP Virtual (VIP)</strong></td>
<td>La dirección IP asignada a su BizTalk Service. Se utiliza para todas las terminales de entrada y es la dirección de origen para el tráfico saliente. Esta dirección IP pertenece a su BizTalk Service siempre que se crea. Si elimina el BizTalk Service, la dirección IP se asigna a otro BizTalk Service.</td>
</tr>
<tr>
<td><strong>Namespace de ACS</strong></td>
<td>Se autentica con el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Edición</strong></td>
<td>Listas de la edición especificado cuando se crea el BizTalk Service.</td>
</tr>
<tr>
<td><strong>Ubicación</strong></td>
<td>Muestra la región geográfica que aloja su BizTalk Service.</td>
</tr>
<tr>
<td><strong>Creado</strong></td>
<td>Muestra la fecha y hora de que creación del BizTalk Service.</td>
</tr>
<tr>
<td><strong>Base de datos de seguimiento</strong></td>
<td>El nombre de la base de datos de SQL Azure que almacena las tablas de seguimiento utilizadas por su BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explican</a> proporciona detalles sobre la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Monitoreo de Archiving de almacenamiento de información</strong></td>
<td>El nombre de la cuenta de almacenamiento de Azure que almacena el resultado de supervisión de su BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explican</a> proporciona detalles sobre la cuenta de almacenamiento.</td>
</tr>
<tr>
<td><strong>Nombre de la suscripción</strong></td>
<td>Enumera la suscripción que aloja su BizTalk Service. La suscripción controla el acceso al portal clásico de Azure.</td>
</tr>
<tr>
<td><strong>Id. de suscripción</strong></td>
<td>Cuando se crea una suscripción, automáticamente se genera un identificador de suscripción. Cuando se utiliza la API del resto, tendrá que introducir el ID de suscripción.</td>
</tr>
</table>

[Servicios de BizTalk: Provisioning portal clásico de Azure usando](http://go.microsoft.com/fwlink/p/?LinkID=302280) enumera los pasos necesarios para crear un BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Administrar la información de conexión, las claves de la sincronización y eliminar en la barra de tareas:

<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de aplicaciones</td>
<td>Se abre el Portal de servicios de BizTalk de Azure. El Portal de servicios de BizTalk es la entrada a la configuración de EDI, incluida la adición de socios y crear X12, AS2 y acuerdos de EDIFACT.
<br/><br/>
Esto es lo mismo que <strong>crear acuerdos de socio</strong> en la ficha <strong>Inicio rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes de mensajería de EDI en BizTalk Services Portal</a> proporciona más información sobre el Portal de servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>Información de conexión de</strong> del Namespace de Control de acceso</td>
<td>Muestra el Namespace de Control de acceso, emisor predeterminado y valores de clave predeterminado; que se puede copiar.
<br/><br/>
También puede abrir el Portal de Control de acceso. Este Portal de Control de acceso es el mismo que con la opción de Active Directory en el panel de navegación de la izquierda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administrar su Namespace de ACS</a> proporciona más información sobre el Portal de Control de acceso.</td>
</tr>
<tr>
<td><strong>Sincronizar las teclas</strong> en la cuenta de almacenamiento</td>
<td>Cuando se crea una cuenta de almacenamiento, se crean automáticamente una clave principal y clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. Su BizTalk Service utiliza automáticamente la clave principal. <strong>Claves de sincronización</strong> permiten a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir el BizTalk Service.
<br/><br/>
Por ejemplo, desea que el BizTalk Service para utilizar una clave principal nueva para la cuenta de almacenamiento. Para hacer esto:
<br/><br/>
<ol>
<li>Seleccione su BizTalk Service y <strong>Las claves de la sincronización</strong>. Seleccione la clave secundaria. Al hacerlo, se inicia el BizTalk Service utilizando la clave secundaria.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave principal. Recuerde que su BizTalk Service utiliza la clave secundaria.</li>
<li>Seleccione su BizTalk Service y <strong>Las claves de la sincronización</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que se hayan regenerado.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se denomina "claves dinámicas". El propósito es permitir a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir el BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Su BizTalk Service y todos los elementos implementados a él se quitan.</td>
</tr>
</table>


## <a name="monitor"></a>Monitor
No se aplica a la edición gratuita.

Cuando se selecciona el nombre de BizTalk Service, la ficha Monitor está disponible y muestra lo siguiente:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Gráfico de métrica: Muestra las métricas de rendimiento seleccionado
Estas métricas proporcionan valores en tiempo real con respecto a la salud de la BizTalk Service. Elige qué mediciones de rendimiento se muestran. Un máximo de seis métricas de rendimiento se puede proyectar simultáneamente. 

También puede elegir los valores **relativos** o **absolutos** y el intervalo de tiempo de **intervalo** de las estadísticas que se muestran. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para quitar o mostrar las métricas en el gráfico:
1. Seleccione la ficha **Monitor** .
2. En la barra de tareas, seleccione **Agregar métricas** :  
![Seleccione Agregar métricas][AddMetrics]
3. Comprobar las métricas de rendimiento que desee mostrar.
4. Seleccione la marca de verificación para volver a la ficha **Monitor** .
5. Seleccione el círculo situado junto a la métrica para mostrar el valor de esa métrica en el gráfico.  

    Por ejemplo, la métrica de **Uso de la CPU** está deshabilitada; el resultado no se muestra en el gráfico:  
![Métrica de uso de la CPU está atenuado][GrayedMetric]  

    Seleccione el sombreado círculo para habilitar la métrica de **Uso de la CPU** mostrar su resultado en el gráfico:  
![Métrica de uso de la CPU está habilitado][EnabledMetric]

6. Para quitar una métrica de la gráfica de la pantalla y la lista, seleccione **Eliminar métrica** en la barra de tareas. Para agregar la parte métrica posterior a la lista, seleccione **Agregar métricas** en la barra de tareas, comprobar la métrica y seleccione la marca de verificación para volver a la ficha **Monitor** . Seleccione el sombreado círculo para habilitar la métrica.

## <a name="Metrics"></a>Estadísticas disponibles
Están disponibles las siguientes contadores de rendimiento y métricas:

<table border="1">

<tr>
<td><strong>Latencia de RountdTrip</strong></td>
<td>Muestra el tiempo promedio necesario en milisegundos (ms) para procesar un mensaje desde el momento en que se recibió el mensaje hasta que el mensaje se procesa completamente por la BizTalk Service a través de todos los puentes. Se cuentan sólo los mensajes que se ha procesado correctamente.<br/><br/>
Cuando se producen los eventos siguientes, se crea una marca de tiempo:
<ul>
<li>Mensaje entra en la puerta de enlace</li>
<li>Los mensajes se enrutan al destino</li>
<li>Se recibe respuesta del destino</li>
<li>Respuesta de confirmación de destino enviada a la puerta de enlace</li>
</ul>
<br/>
Esta métrica muestra el resultado del cálculo siguiente:
<br/><br/>
[Destino acuse respuesta enviada a la puerta de enlace] - [mensaje entra en la puerta de enlace]</td>
</tr>
<tr>
<td><strong>Errores en el código fuente</strong></td>
<td>Muestra el número total de mensajes de error por la BizTalk Service cuando extraen mensajes de los extremos de origen.</td>
</tr>
<tr>
<td><strong>Uso de la CPU</strong></td>
<td>Muestra el promedio % tiempo de procesador de todas las instancias de la función.</td>
</tr>
<tr>
<td><strong>Latencia de procesamiento</strong></td>
<td>Muestra el tiempo promedio necesario en milisegundos (ms) para procesar un mensaje por la BizTalk Service a través de todos los puentes, con exclusión del tiempo dedicado a los destinos. Se cuentan sólo los mensajes que se ha procesado correctamente.<br/><br/>
Cuando cada uno de los siguientes eventos se producen, se crea una marca de tiempo:

<ul>
<li>Mensaje entra en la puerta de enlace</li>
<li>Los mensajes se enrutan al destino</li>
<li>Se recibe respuesta del destino</li>
<li>Respuesta de confirmación de destino enviada a la puerta de enlace</li>
</ul>
<br/>Esta métrica muestra el resultado del cálculo siguiente:<br/><br/>
[Destino acuse respuesta enviada a la puerta de enlace] - [mensaje entra en la puerta de enlace] - [se recibe respuesta de destino] + [mensaje se enruta al destino]</td>
</tr>
<tr>
<td><strong>Errores en el proceso</strong></td>
<td>Muestra el número total de mensajes de error durante el procesamiento de la BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes enviados</strong></td>
<td>Muestra el número total de mensajes enviados por la BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando llega a un mensaje enviado desde una canalización de la ruta de destino. Esta métrica no indica que un mensaje se ha procesado correctamente.<br/><br/>
En un escenario de solicitud y respuesta, la métrica se incrementa cuando la ruta de destino envía un acuse de recibo a la canalización.</td>
</tr>
<tr>
<td><strong>Mensajes recibidos</strong></td>
<td>Muestra el número total de mensajes recibidos por la BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando se recibe un nuevo mensaje de la canalización.</td>
</tr>
<tr>
<td><strong>Mensajes en proceso</strong></td>
<td>Muestra el número total de mensajes que está procesando actualmente el BizTalk Service dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes procesados</strong></td>
<td>Muestra el número total de mensajes procesados correctamente por la BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje se recibió correctamente la canalización y se enrutan correctamente al destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
En la ficha escala, puede agregar o restar el número de unidades utilizadas por su BizTalk Service. De forma predeterminada, hay una unidad configurada. Pueden agregarse unidades adicionales para escalar su BizTalk Service. Al aumentar la escala, aumenta el rendimiento. La cantidad de recursos también aumenta, incluyendo puentes de implementada, acuerdos, conexiones de unidad de negocio y capacidad de procesamiento. Por ejemplo, aumentar la escala de 1 unidad a 2 unidades. En esta situación, puede implementar el doble del número de puentes, doble los acuerdos, doble las conexiones de unidad de negocio y doble de la potencia de procesamiento.

Algunas ediciones de BizTalk no ofrecen una opción de escala. En esta situación, se permite una sola unidad. Para determinar cuántas unidades se puede escalar su edición, consulte [Servicios de BizTalk: cuadro de las ediciones](biztalk-editions-feature-chart.md).

Aumentar el número de unidades puede afectar precios. Si aumenta las unidades, seleccionando **Guardar** muestra un mensaje que indica si se ve afectada la facturación. A continuación, elija continuar. Al aumentar el número de unidades, el estado de BizTalk Service cambia de activo a actualizar. En el estado de la actualización, su BizTalk Service continúa ejecutándose.

[Servicios de BizTalk: cuadro de las ediciones](biztalk-editions-feature-chart.md) define una "unidad".


## <a name="configure"></a>Configurar
No se aplica a conexiones híbridas.

El estado de copia de seguridad se establece en ninguno o automático. Cuando se establece en ninguno, se crea automáticamente ninguna copia de seguridad. Cuando se establece en automático, configurar la ubicación de copia de seguridad, la frecuencia de la copia de seguridad y durante cuánto tiempo deben mantener los archivos de copia de seguridad. 

[Servicios de BizTalk: Backup y Restore](biztalk-backup-restore.md) proporciona los detalles. 


## <a name="HybridConnections"></a>Conexiones híbridas
Conexiones híbridas conectan una aplicación de Azure, como aplicaciones Web o aplicaciones móviles en servicio de la aplicación de Azure, a un recurso local que utiliza un puerto TCP estático, como SQL Server, MySQL, APIs de Web HTTP y mayoría de los servicios Web personalizados. Conexiones híbridas se administran en los servicios de BizTalk en el portal Azure clásico.

Para crear conexiones híbridas en servicio de la aplicación de Azure, consulte [acceso a recursos mediante conexiones híbrido en Azure App servicio local](../app-service-web/web-sites-hybrid-connection-get-started.md).

Para crear o administrar conexiones híbrido en Azure BizTalk Services, vea [Conexiones híbridas](integration-hybrid-connection-overview.md).



## <a name="next"></a>Siguiente
Ahora que está familiarizado con las diferentes fichas, puede obtener más información acerca de las características de los servicios de BizTalk de Azure:

- [Servicios de BizTalk: límite](biztalk-throttling-thresholds.md)  
- [Servicios de BizTalk: Nombre del emisor y la clave de emisor](biztalk-issuer-name-issuer-key.md)  
- [Los servicios de BizTalk: Backup y Restore](biztalk-backup-restore.md)

## <a name="see-also"></a>Vea también
- [Conexiones híbridas](integration-hybrid-connection-overview.md)  
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](biztalk-editions-feature-chart.md)  
- [Servicios de BizTalk: Provisioning portal clásico de Azure usando](biztalk-provision-services.md)  
- [Servicios de BizTalk: Gráfico de estado de servicio de BizTalk](biztalk-service-state-chart.md)  
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
