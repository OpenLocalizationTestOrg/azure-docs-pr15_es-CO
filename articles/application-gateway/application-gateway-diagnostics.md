<properties 
   pageTitle="Supervisar los registros de acceso y rendimiento y métricas de puerta de enlace de aplicación | Microsoft Azure"
   description="Aprenda a habilitar y administrar registros de acceso y rendimiento de puerta de enlace de aplicación"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Registro de diagnósticos y la métrica de puerta de enlace de aplicación

Azure proporciona la capacidad para supervisar recursos con el registro y métricas

[**Registro**](#enable-logging-with-powershell) : permite el registro de rendimiento, acceso y otros registros que se guarden o se consume desde un recurso para fines de vigilancia.

[**Métrica**](#metrics) - Application gateway actualmente tiene una métrica. Esta métrica mide el rendimiento de la puerta de enlace de aplicación en Bytes por segundo.

Puede utilizar diferentes tipos de registros en Azure para administrar y solucionar problemas de puertas de enlace de aplicación. Algunos de estos registros pueden obtenerse a través del portal, y todos los registros se pueden extraer de un almacenamiento Azure blob y ver en distintas herramientas, como [Análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md), Excel y PowerBI. Puede aprender más sobre los diferentes tipos de registros de la siguiente lista:

- **Registros de auditoría:** Puede utilizar [Los registros de auditoría de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente denominados registros operativos) para ver todas las operaciones de sometimiento a su suscripción de Azure y su estado. Registros de auditoría están habilitados de forma predeterminada y se pueden ver en el portal de Azure de vista previa.
- **Acceder a los logs:** Puede utilizar este registro para ver el patrón de acceso de puerta de enlace de aplicación y analizar importante información de IP del llamador, URL solicitada, la latencia de respuesta, devolver código de bytes de entrada y salida. Registro de acceso se recopila cada 300 segundos. Este registro contiene un registro por cada instancia de puerta de enlace de aplicaciones. La instancia de puerta de enlace de la aplicación puede identificarse por propiedad 'instanceId'.
- **Registros de rendimiento:** Puede utilizar este registro para ver el rendimiento de las instancias de la puerta de enlace de aplicaciones. Este registro captura información de rendimiento sobre la base de cada instancia incluida el número total de peticiones servida, rendimiento en bytes, número total de peticiones servidas, recuento de solicitudes con error, instancia de back-end y sin ellos. Registro de rendimiento se recopila cada 60 segundos.
- **Registros del firewall:** Puede utilizar este registro para ver las solicitudes que se registran a través del modo de detección o prevención de una puerta de enlace de la aplicación que está configurado con el servidor de aplicaciones web.

>[AZURE.WARNING] Registros sólo están disponibles para recursos implementados en el modelo de implementación del Administrador de recursos. No puede utilizar registros de recursos en el modelo de implementación clásica. Para comprender mejor de los dos modelos, haga referencia al artículo de [distribución clásico y Administrador de recursos del conocimiento](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Habilitar el registro con PowerShell

El registro de auditoría se habilita automáticamente para cada recurso del Administrador de recursos. Debe habilitar acceso y registro para iniciar la recopilación de los datos disponibles a través de los registros de rendimiento. Para habilitar el registro, consulte los siguientes pasos: 

1. Nota El identificador del recurso de la cuenta de almacenamiento, donde se almacenan los datos de registro. Esto sería del formulario: /subscriptions/\<subscriptionId\>/resourceGroups/\<nombre de grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nombre de la cuenta de almacenamiento\>. Puede utilizarse cualquier cuenta de almacenamiento en su suscripción. Puede utilizar el portal de vista previa para buscar esta información.

    ![Obtener una vista previa de portal - diagnósticos de puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics1.png)

2. Nota es el identificador de recurso del gateway de la aplicación para que el registro esté habilitada. Esto sería del formulario: /subscriptions/\<subscriptionId\>/resourceGroups/\<nombre de grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nombre de puerta de enlace de la aplicación\>. Puede utilizar el portal de vista previa para buscar esta información.

    ![Obtener una vista previa de portal - diagnósticos de puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilitar el registro de diagnóstico mediante el siguiente cmdlet de powershell:

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] logs de auditoría no requieren una cuenta de almacenamiento independiente. El uso de almacenamiento de información para el registro de rendimiento y acceso incurre en gastos de servicio.

## <a name="enable-logging-with-azure-portal"></a>Habilitar el registro con el portal de Azure

### <a name="step-1"></a>Paso 1

Desplácese hasta el recurso en el portal de Azure. Haga clic en **registros de diagnóstico**. Si es la primera vez que configura diagnósticos el módulo es similar a la siguiente imagen:

Puerta de enlace de aplicaciones, 3 registros están disponibles.

- Registro de acceso
- Registro de rendimiento
- Registro de Firewall

Haga clic en **Activar diagnósticos** para iniciar la recopilación de datos.

![hoja de configuración de diagnósticos][1]

### <a name="step-2"></a>Paso 2

En la hoja de **configuración de diagnóstico** , la configuración de cómo se establecen los registros de diagnóstico. En este ejemplo, el análisis de registro se utiliza para almacenar los registros. Haga clic en **Configurar** en el **Análisis del registro** para configurar el área de trabajo. Concentradores de evento y una cuenta de almacenamiento pueden utilizarse para guardar también los registros de diagnósticos.

![módulo de diagnóstico][2]

### <a name="step-3"></a>Paso 3

Elija un área de OMS existente o crear uno nuevo. En este ejemplo se utiliza uno existente.

![áreas de trabajo de OMS][3]

### <a name="step-4"></a>Paso 4

Cuando haya terminado, confirme la configuración y haga clic en **Guardar** para guardar la configuración.

![Confirmar selección][4]

## <a name="audit-log"></a>Registro de auditoría

Azure genera este registro (conocido como "registro operacional") de forma predeterminada.  Los registros se conservan durante 90 días en el almacén de registros de sucesos de Azure. Más información sobre estos registros, lea el artículo [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="access-log"></a>Registro de acceso

Este registro sólo se genera si se ha habilitado en una función de puerta de enlace de aplicación como se detalla en los pasos anteriores. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Cada acceso de puerta de enlace de aplicación se registra en formato JSON, tal como se muestra en el ejemplo siguiente:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Registro de rendimiento

Este registro sólo se genera si se ha habilitado en una función de puerta de enlace de aplicación como se detalla en los pasos anteriores. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se registran los datos siguientes:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Registro de Firewall

Este registro sólo se genera si se ha habilitado en una puerta de enlace cada aplicación como se detalla en los pasos anteriores. Este registro también requiere dicho servidor de seguridad de la aplicación web esté configurado en una puerta de enlace de la aplicación. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se registran los datos siguientes:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Ver y analizar el registro de auditoría

Puede ver y analizar los datos de registro de auditoría mediante cualquiera de los métodos siguientes:

- **Herramientas de azure:** Recuperar información de los registros de auditoría a través de PowerShell de Azure, la Command Line Interface (CLI) de Azure, la API de REST de Azure o el portal de Azure de vista previa.  Instrucciones paso a paso para cada método se detallan en el artículo [con el Administrador de recursos de operaciones de auditoría](../resource-group-audit.md) .
- **De alimentación BI:** Si ya no tienes una cuenta de [BI de alimentación](https://powerbi.microsoft.com/pricing) , puede probarlo de forma gratuita. Mediante los [registros de auditoría de Azure content pack para poder BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar sus datos con paneles preconfigurados que puede utilizar como-es o personalizar.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Ver y analizar el registro de acceso, rendimiento y firewall

Azure [Registro Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) puede recopilar el contador de registro de sucesos de archivos y desde su cuenta de almacenamiento Blob e incluye visualizaciones y potentes capacidades de búsqueda para analizar los registros.

También puede conectarse a su cuenta de almacenamiento de información y recuperar las entradas de registro JSON para registros de acceso y rendimiento. Una vez descargados los archivos JSON, puede convertirlos a archivos CSV y ver en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con los conceptos básicos de cambiar los valores de constantes y variables de C# y Visual Studio, puede utilizar las [Herramientas de registro convertidor](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponible en Github.

## <a name="metrics"></a>Métricas

Métrica es una característica de determinados recursos Azure, donde puede ver los contadores de rendimiento en el portal. Para puerta de enlace de aplicaciones, una métrica está disponible en el momento de escribir este artículo. Esta métrica es el rendimiento y puede verse en el portal. Desplácese a una puerta de enlace de la aplicación y haga clic en **medidas**.  Seleccione el rendimiento en la sección de **estadísticas disponibles** para ver los valores. En la siguiente imagen, puede ver un ejemplo con los filtros que pueden utilizarse para mostrar los datos en intervalos de tiempo distintos.

Para ver una lista de actual admite métricas, visite [métricas compatibles con el Monitor de Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![vista de métrica][5]

## <a name="alert-rules"></a>Reglas de alerta

Las reglas de alertas se pueden iniciar en función de métricas en un recurso. Esto significa que para la puerta de enlace de aplicaciones, una alerta puede llamar a un webhook o un administrador de correo electrónico si el rendimiento de la puerta de enlace de la aplicación es por encima, por debajo o en un umbral para un período de tiempo especificado.

En el ejemplo siguiente, se le guiará a través de la creación de una regla de alerta que envía un correo electrónico a un administrador después de que se ha conseguido infringir un umbral de rendimiento.

### <a name="step-1"></a>Paso 1

Haga clic en **Agregar alerta métrica** para iniciar. Este módulo también puede ir desde la hoja de métricas.

![módulo de reglas de alerta][6]

### <a name="step-2"></a>Paso 2

En la hoja de **Agregar regla** , complete el nombre, la condición y notificará las secciones y haga clic en **Aceptar** cuando haya terminado.

El selector de **condición** permite 4 valores, **mayor**, **igual o mayor que**, **menor que**o **menor o igual a**.

El selector de **período** , se permite para la selección de un período de 5 minutos a 6 horas.

Mediante la selección de **los lectores, colaboradores y los dueños del correo electrónico** el correo electrónico puede ser dinámico basándose en los usuarios que tienen acceso a ese recurso. De lo contrario puede proporcionarse una lista separada por comas de los usuarios en el cuadro de texto **email(s) Administrador adicionales** .

![Agregar hoja de regla][7]

Si se infringe el umbral, un correo electrónico llega similar a la que se muestra en la siguiente imagen:

![correo electrónico infringido el umbral][8]

Una vez que una alerta de métrica se ha creado y proporciona una visión general de todas las reglas de alerta, se muestra una lista de alertas.

![vista de la regla de alerta][9]

Para obtener más información acerca de las notificaciones de alertas, visite [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Para conocer más acerca de webhooks y cómo se puede utilizar con las alertas, visite [configurar un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Próximos pasos

- Visualizar el contador y registros de sucesos con el [Análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md) 
- [Ver los registros de auditoría de Azure con alimentación BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) de blog.
- [Ver y analizar los registros de auditoría de Azure en BI de alimentación y más](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) de blog.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png