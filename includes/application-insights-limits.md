Hay algunos límites en el número de eventos por cada aplicación y métricas (es decir, cada clave de instrumentación). 

Los límites dependen el [nivel de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

**Recurso** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
Puntos de datos de la sesión<sup>1, 2</sup> por mes | ilimitado | 
Puntos de datos total por mes por solicitud, evento, dependencia, seguimiento, excepción y vista de página | 5 millones | 50 millones<sup>3</sup>
Velocidad de datos de [registro y seguimiento](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | dp 500/s
Velocidad de datos de [excepción](../articles/application-insights/app-insights-asp-net-exceptions.md) | dp 50/s | dp 50/s
Velocidad total de datos de solicitud, evento, dependencia y telemetría de la vista de página | 200 dp/s | dp 500/s
Retención de datos sin procesar para [búsqueda](../articles/application-insights/app-insights-diagnostic-search.md) y [análisis](../articles/application-insights/app-insights-analytics.md) | 7 días
Retención de datos agregados para [el Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md) | 90 días
Recuento de nombres de [propiedad](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre de propiedad | 150 | 
Longitud del valor de propiedad | 8192 | 
Longitud del mensaje Trace y excepciones | 10000 |
Recuento de nombres de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre de métrica |  150 | 
[Pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> un punto de datos es un valor de métrica individual o con propiedades adjuntas y las medidas.

<sup>2</sup> una sesión de punto de datos registra el inicio o el final de una sesión y registra la identidad del usuario.

<sup>3</sup> pueden adquirir capacidad adicional más allá de los 50 millones.
 
[Sobre los precios y las cuotas en las perspectivas de aplicación](../articles/application-insights/app-insights-pricing.md)
