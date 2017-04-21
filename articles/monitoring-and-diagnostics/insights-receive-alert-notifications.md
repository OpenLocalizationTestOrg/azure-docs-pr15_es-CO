<properties
    pageTitle="Recibir notificaciones de alerta para los servicios de Azure | Microsoft Azure"
    description="Notificará cuando se cumplen las condiciones de las reglas de alertas."
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

# <a name="receive-alert-notifications"></a>Recibir notificaciones de alerta

Puede recibir una alerta basada en métricas de supervisión para o eventos en los servicios de Azure.

Para una regla de alerta en un valor de métrica, cuando el valor de una métrica específica cruza un umbral asignado, la regla de alerta se activa y puede enviar una notificación. Para una regla de alerta de eventos, una regla puede enviar una notificación en *cada* evento, o, cuando se producen un número determinado de eventos.

Cuando se crea una regla de alerta, puede seleccionar opciones para enviar una notificación por correo electrónico al administrador del servicio y administradores de compañeros o a otro administrador puede especificar. Cuando se activa la regla, y cuando se resuelve una condición de alerta, se envía un correo electrónico de notificación.

Puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar y obtener información acerca de las reglas de alertas mediante programación.

## <a name="create-an-alert-rule"></a>Crear una regla de alerta

1. En el [portal](https://portal.azure.com/), haga clic en **Examinar**y, a continuación, un recurso que le interesa de supervisión.

2. Haga clic en la ficha **reglas de alertas** en el objetivo de **las operaciones** .

3. Haga clic en el comando **Agregar alerta** .

    ![Agregar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Puede nombrar la regla de alerta y elija una descripción que se mostrará en la notificación por correo electrónico.

5. Al seleccionar **métricas** elegirá una condición y el valor de umbral para la métrica. Esto es el período de tiempo que Azure se utiliza para supervisar y trama de la actividad de alerta.

    ![Condición y umbral](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. También puede elegir **eventos**y recibir una notificación cuando se produce un determinado evento.

    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Por último, puede elegir enviar notificación por correo electrónico a los administradores responsables.

Después de hacer clic en **Guardar**, dentro de unos minutos se le informará cuando la métrica que elija supera el umbral.

## <a name="managing-your-alert-rules"></a>Administrar las reglas de alertas

Una vez haya creado una regla de alerta, puede ver una vista previa de la alerta de umbral en comparación con la métrica del día anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Por supuesto, puede editar esta regla de alerta y **Habilitar** o **Deshabilitar** si desea temporalmente dejar de recibir notificaciones acerca de él.

## <a name="next-steps"></a>Próximos pasos

* [Configurar webhooks en sus avisos](insights-webhooks-alerts.md) a notificaciones de ruta para diversos canales
* [Métricas de servicio de monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Habilite la supervisión y diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Disponibilidad de monitor y capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con los conocimientos de aplicación por lo que se puede averiguar si su página está inactivo.
* [Supervisar el rendimiento de aplicaciones](../application-insights/app-insights-azure-web-apps.md) si desea entender exactamente cómo está funcionando el código en la nube.
* [Ver eventos y registros de auditoría](insights-debugging-with-events.md) para saber todo lo que ha sucedido en el servicio.
* [Estado del servicio de seguimiento](insights-service-health.md) para averiguar cuándo Azure ha experimentado interrupciones de degradación o de servicio de rendimiento.
