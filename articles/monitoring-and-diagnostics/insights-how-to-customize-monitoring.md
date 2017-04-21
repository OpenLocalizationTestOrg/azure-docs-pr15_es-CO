<properties
    pageTitle="Resumen de métricas en Microsoft Azure | Microsoft Azure"
    description="Aprenda a personalizar gráficos de supervisión en Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Resumen de métricas en Microsoft Azure

Realizar el seguimiento de todos los servicios de Azure métricas clave que permiten supervisar el estado, performance, disponibilidad y uso de los servicios. Puede ver estos indicadores en el portal de Azure, y también puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para obtener acceso mediante programación a todo el conjunto de métricas.

Para algunos servicios, puede que necesite activar diagnósticos para ver estadísticas. Para otros, tales como máquinas virtuales, obtendrá un conjunto básico de métricas, pero necesita establecer métricas de alta frecuencia permitir la completa. Vea [Habilitar monitoreo y diagnóstico](insights-how-to-use-diagnostics.md) para obtener más información.

## <a name="using-monitoring-charts"></a>Utilizar gráficos de supervisión

Puede realizar un gráfico cualquiera de las métricas de ellos durante cualquier período de tiempo que elija.

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**y, a continuación, un recurso que le interesa de supervisión.

2. La sección de **supervisión** contiene la métrica más importante para cada recurso de Azure. Por ejemplo, una aplicación web tiene **errores y solicitudes**, mientras que una máquina virtual tendría **el porcentaje de CPU** y **disco de lectura y escritura**:  ![objetivo de supervisión](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Haciendo clic en cualquier gráfico, se mostrará la hoja de **métrica** . En la hoja, además de la gráfica es una tabla que muestra las agregaciones de las métricas (como promedio, mínimo y máximo, durante el intervalo de tiempo elegido). A continuación que son las reglas de alertas para el recurso.
    ![Hoja de métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar las líneas que aparecen, haga clic en el botón **Editar** en el gráfico o el comando **Editar el gráfico** en la hoja de métrica.

5. En la hoja de modificar consulta puede hacer tres cosas:
    - Cambiar el intervalo de tiempo
    - Cambiar la apariencia entre líneas y barras
    - Elija diferentes metics ![modificar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Cambiar el intervalo de tiempo es tan fácil como seleccionar un rango diferente (por ejemplo, **Última hora**) y haciendo clic en **Guardar** en la parte inferior de la hoja. También puede elegir **personalizado**, que le permite elegir cualquier período de tiempo durante las últimas dos semanas. Por ejemplo, puede ver las dos semanas completas o, tan sólo 1 hora de ayer. Escriba en el cuadro de texto para introducir una hora diferente.
    ![Intervalo de tiempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Por debajo del rango de tiempo, chan usted elija cualquier número de métricas para mostrar en el gráfico.

8. Al hacer clic en Guardar los cambios se guardarán para ese recurso. Por ejemplo, si tiene dos máquinas virtuales y cambiar un gráfico en uno, no afectará al otro.

## <a name="creating-side-by-side-charts"></a>Creación de gráficos de side-by-side

Puede agregar tantos gráficos como desee con la personalización de gran alcance en el portal.

1. En el menú **...** en la parte superior de la hoja, haga clic en **Agregar fichas**:  
    ![Agregar menú](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. A continuación, se puede seleccionar un gráfico de la **Galería** en el lado derecho de la pantalla:  ![Galería](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si no ve la métrica que desee, puede siempre agregar una de las medidas preestablecidas y **Editar** el gráfico para mostrar la métrica que necesita.

## <a name="monitoring-usage-quotas"></a>Supervisión de uso de cuotas

Métricas de mayoría mostrar tendencias en el tiempo, pero determinados datos, como el uso de cuotas, son información de punto en el tiempo con un umbral.

También puede ver el uso de cuotas en la hoja de recursos que tengan cuotas:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Al igual que con métricas, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para obtener acceso mediante programación al conjunto completo de uso de cuotas.

## <a name="next-steps"></a>Próximos pasos

* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) siempre que una métrica cruza un umbral.
* [Habilite la supervisión y diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Recuento de la instancia la escala automáticamente](insights-how-to-scale.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Supervisar el rendimiento de aplicaciones](../application-insights/app-insights-azure-web-apps.md) si desea entender exactamente cómo está funcionando el código en la nube.
* Utilice [Las perspectivas de aplicación para aplicaciones de JavaScript y páginas web](../application-insights/app-insights-web-track-usage.md) para obtener análisis del cliente acerca de los exploradores que visitan una página web.
* [Disponibilidad de monitor y capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con los conocimientos de aplicación por lo que se puede averiguar si su página está inactivo.
