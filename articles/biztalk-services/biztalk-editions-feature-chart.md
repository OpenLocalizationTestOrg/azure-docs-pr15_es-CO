<properties
    pageTitle="Obtenga información acerca de las características de las ediciones de BizTalk Services | Microsoft Azure"
    description="Comparar las funciones de las ediciones de BizTalk Services: gratuita, desarrollador, básico, estándar y Premium. MABS, WABS."
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
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>BizTalk Services: Gráfico de las ediciones

Los servicios de BizTalk Azure ofrece varias ediciones. Use este artículo para determinar qué edición es la adecuada para sus necesidades de negocio y escenario.


## <a name="compare-the-editions"></a>Compare las ediciones

**Libre (vista previa)**

Puede crear y administrar conexiones híbrido. Una conexión híbrido es una manera fácil de conectar a un sitio Web de Azure a un sistema local, como SQL Server.

**Developer**

Incluye conexiones híbridas, EAI y EDI procesamiento de mensaje con un portal de administración de socios comerciales de fácil de usar y soporte para esquemas EDI comunes y enriquecido EDI sobre X12 y AS2. Puede crear escenarios EAI conectar servicios en la nube con los protocolos HTTP/S, resto, FTP, SFTP y WCF para leer y escribir mensajes.  Utilizar conectividad a los sistemas LOB local con adaptadores listos para el uso de SAP, Oracle eBusiness, bases de datos Oracle, Siebel y SQL Server. Utilice un entorno centrado en developer con herramientas de Visual Studio para la implementación y desarrollo fácil. Se limita a propósitos de desarrollo y prueba únicamente con ningún acuerdo de nivel de servicio (SLA).

**Básico**

Incluye la mayor parte de las funciones para desarrolladores con aumentos en conexiones conexiones híbridas, puentes EAI, acuerdos de EDI y Pack de adaptador de BizTalk. También ofrece alta disponibilidad y la opción de ampliarlas con un contrato de nivel de servicio (SLA).

**Estándar**

Incluye todas las capacidades básicas con aumentos en las conexiones de conexiones híbridas, puentes EAI, acuerdos de EDI y Pack de adaptador de BizTalk. También ofrece alta disponibilidad y la opción de ampliarlas con un contrato de nivel de servicio (SLA).

**Premium**

Incluye todas las funciones estándares con aumentos en las conexiones de conexiones híbridas, puentes EAI, acuerdos de EDI y Pack de adaptador de BizTalk. También incluye archiving, alta disponibilidad y la opción de ampliarlas con un contrato de nivel de servicio (SLA).


## <a name="editions-chart"></a>Gráfico de ediciones
En la tabla siguiente se enumera las diferencias.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Libre (vista previa)</th>
        <th>Developer</th>
        <th>Básico</th>
        <th>Estándar</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Precio de salida</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Servicios de BizTalk Azure precios</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de precios de Azure</a></td>
</tr>
<tr>
<td><strong>Configuración mínima por defecto</strong></td>
<td>1 unidad libre</td>
<td>Unidad de 1 desarrollo</td>
<td>1 unidad básica</td>
<td>1 unidad estándar</td>
<td>Unidad de 1 prima</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Sí, en incrementos de 1 unidad básica</td>
<td>Sí, en incrementos de 1 unidad estándar</td>
<td>Sí, en incrementos de 1 unidad de prima</td>
</tr>
<tr>
<td><strong>Máximo permitido de escala horizontal</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Hasta 8 unidades</td>
<td>Hasta 8 unidades</td>
<td>Hasta 8 unidades</td>
</tr>
<tr>
<td><strong>Puentes de EAI por unidad</strong></td>
<td>No incluye</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Se incluyen acuerdos de TPM</td>
<td>No incluye</td>
<td>Incluido. acuerdos de 10 por unidad.</td>
<td>Incluido. 50 acuerdos por unidad.</td>
<td>Incluido. acuerdos de 250 por unidad.</td>
<td>Incluido. acuerdos de 1000 por unidad.</td>
</tr>
<tr>
<td><strong>Híbrido de conexiones por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferencia de datos de conexión híbrida (GB) por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexiones de servicio de adaptador de BizTalk para los sistemas LOB local</strong></td>
<td>No incluye</td>
<td>1 conexión</td>
<td>2 conexiones</td>
<td>5 conexiones</td>
<td>25 conexiones</td>
</tr>
<tr>
<td align="left"><strong>Sistemas y protocolos admitidos:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus de servicios (SB)</li>
<li>Azure Blob</li>
<li>API del resto</li>
</ul>
</td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Alta disponibilidad</strong>
<br/><br/>
Acuerdo de nivel de servicio (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Precios de Azure BizTalk Services</a>.
</td>
<td>No incluye</td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Backup y restore</strong></td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Seguimiento</strong></td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Archiving</strong><br/><br/>
Incluye el no repudio de recepción (NRR) y descargar los mensajes con seguimiento</td>
<td>No incluye</td>
<td>Incluye</td>
<td>No incluye</td>
<td>No incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
<tr>
<td><strong>Uso de transformaciones, incluyendo XSLT personalizado</strong></td>
<td>No incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
<td>Incluye</td>
</tr>
</table>

> [AZURE.NOTE] Para resistencia contra fallas de hardware, alta disponibilidad implica tener varias máquinas virtuales dentro de una sola unidad de BizTalk.


## <a name="faqs"></a>Preguntas más frecuentes

#### <a name="what-is-a-biztalk-unit"></a>¿Qué es una unidad de BizTalk?
Una "unidad" es el nivel atómico de una implementación de servicios de BizTalk de Azure. Cada edición viene con una unidad que tiene la memoria y la capacidad de cálculo diferentes. Por ejemplo, una unidad básica tiene más compute de desarrollador, estándar tiene más compute de básico y así sucesivamente. Al escalar un BizTalk Service, escala en términos de unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>¿Cuál es la diferencia entre los servicios de BizTalk y BizTalk VM de Azure?
BizTalk Services proporciona una verdadera arquitectura de plataforma-as-a-Service (PaaS) para crear soluciones de integración en la nube. Con el modelo de PaaS que centrarse completamente en la aplicación lógica y salen todos de la administración de la infraestructura a Microsoft, incluyendo:

- No hay necesidad de administrar o aplicar parches a las máquinas virtuales.
- Microsoft asegura la disponibilidad.
- Controlar la escala demanda simplemente solicitando más o menos capacidad a través del portal de Azure.

BizTalk Server en las máquinas virtuales Azure proporciona una arquitectura de infraestructura-as-a-Service (IaaS). Crear máquinas virtuales y configurar exactamente igual que el entorno local, lo que permite ejecutar las aplicaciones existentes en la nube sin cambios de código. IaaS, va siendo responsable de configurar las máquinas virtuales, administrar las máquinas virtuales (por ejemplo, instalar software y parches del sistema operativo) y arquitectura de la aplicación para alta disponibilidad.

Si está buscando en nuevas soluciones de integración que minimizan el esfuerzo de administración de infraestructura, utilice servicios de BizTalk. Si se desean para migrar rápidamente las soluciones existentes de BizTalk o buscando un entorno bajo demanda desarrollar y probar aplicaciones de BizTalk Server, utilice BizTalk Server en una máquina Virtual de Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>¿Cuál es la diferencia entre el servicio de adaptador de BizTalk y conexiones híbridas?
El servicio de adaptador de BizTalk utiliza un servicio de BizTalk de Azure. El servicio de adaptador de BizTalk utiliza el Pack de adaptador de BizTalk para conectar a un sistema de línea de negocio (LOB) local. Una conexión híbrida proporciona una forma fácil y cómoda de conectar aplicaciones de Azure, al igual que la característica de aplicaciones Web en el servicio de aplicación de Azure y Azure Servicios para dispositivos móviles, a un recurso local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>¿Qué significa "Híbrido conexión transferencia de datos (GB) por unidad"? ¿Es por minuto, hora, día, semana y mes? ¿Qué ocurre cuando se alcanza el límite?

El costo de conexión híbrida por unidad depende de la edición de los servicios de BizTalk. En pocas palabras, los costos dependen de la cantidad de datos transferir. Por ejemplo, la transferencia de datos de 10 GB diariamente cuesta menos que transferir diariamente 100 GB. Utilice la [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/?scenario=full) para los servicios de BizTalk para determinar los costes específicos. Normalmente, los límites se aplican diariamente. Si superan el límite, cualquier cobertura se cargará a la tasa de 1 $ por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Cuando creo un acuerdo de servicios de BizTalk, ¿por qué el número de puentes sube por dos en lugar de sólo uno?

Cada acuerdo consta de dos puentes diferentes: un puente de comunicación del lado de envío y un puente de comunicación del lado de recepción.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>¿Qué ocurre cuando me tocó el límite de cuota en el número de puentes o acuerdos?

No es posible implementar los nuevos puentes o crear los nuevos acuerdos. Para implementar más, debe ampliar a más unidades de servicio de BizTalk o actualizar a una edición superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>¿Cómo migro desde un nivel de servicios de BizTalk a otro?

La edición gratuita no puede ser migrado o 'escalar' a otro nivel y no puede ser el backup y restore a otro nivel. Si necesita otro nivel, crear un BizTalk Service nuevo usando el nuevo nivel. Los artefactos que se crea utilizando la edición gratuita, incluidas las conexiones híbrido, necesitan volver a crear en el nuevo BizTalk Service. 

Para las ediciones restantes, utilice la copia de seguridad y restauración para migrar los artefactos desde un nivel a otro. Por ejemplo, los artefactos en el nivel estándar de copia de seguridad y, a continuación, restaurarlas al nivel Premium. [Servicios de BizTalk: Backup y Restore](biztalk-backup-restore.md) describe las rutas de acceso de migración compatibles y enumera qué artefactos se copian. Tenga en cuenta que las conexiones híbrido no se copia. Después de realizar copias de seguridad y restaurar a un nuevo nivel, a continuación, volver a crear las conexiones híbrido.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>¿Es el servicio de adaptador de BizTalk, incluido en el servicio? ¿Cómo obtengo el software?

Sí, el servicio de adaptador de BizTalk con el adaptador de BizTalk Pack se incluyen con el SDK de servicios de BizTalk de Azure [Descargar](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Próximos pasos

Para crear servicios de BizTalk de Azure en el portal de Azure, vaya a [Servicios de BizTalk: Provisioning mediante el portal de Azure](biztalk-provision-services.md). Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionales
- [Servicios de BizTalk: Provisioning mediante el portal de Azure](biztalk-provision-services.md)<br/>
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](biztalk-service-state-chart.md)<br/>
- [Los servicios de BizTalk: Fichas de panel, el Monitor y la escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Los servicios de BizTalk: Backup y restore](biztalk-backup-restore.md)<br/>
- [Servicios de BizTalk: límite](biztalk-throttling-thresholds.md)<br/>
- [Servicios de BizTalk: Nombre del emisor y la clave de emisor](biztalk-issuer-name-issuer-key.md)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
