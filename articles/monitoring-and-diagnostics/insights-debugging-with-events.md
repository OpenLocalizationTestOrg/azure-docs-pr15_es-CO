<properties
    pageTitle="Ver eventos y registros de auditoría"
    description="Aprenda a ver todos los eventos que se producen en su suscripción de Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Ver eventos y registros de auditoría

Todas las operaciones realizadas en los recursos de Azure se auditarán totalmente por el Administrador de recursos de Azure, desde la creación y eliminaciones a la concesión o denegación de acceso. Puede examinar estos registros en el portal de Azure, y también puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para tener acceso a todo el conjunto de eventos mediante programación.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Examinar los eventos que afectan a su suscripción de Azure

1. Iniciar sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar** y seleccione los **registros de auditoría**.  
    ![Examinar el concentrador](./media/insights-debugging-with-events/Insights_Browse.png)
3. Esto abrirá una placa que muestra todos los eventos que han afectado a cualquiera de sus suscripciones para los últimos 7 días. En la parte superior es un gráfico que muestre los datos por nivel y bajo ésta es la lista completa de registros:  ![todos los eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Sólo puede ver los eventos más recientes 500 de una suscripción determinada en el portal de Azure.

4. Puede hacer clic en cualquier entrada de registro para ver los eventos que lo componen. Por ejemplo, al implementar algo en un grupo de recursos, muchos recursos diferentes pueden ser creados o modificados. Para cada entrada puede ver:
    * El **nivel** del evento - por ejemplo, podría ser simplemente algo para realizar el seguimiento (**informativo**), o cuando algo ha ido mal que necesita saber sobre (**Error**).
    * El **estado** - el estado final generalmente será **realizada con éxito** o con **errores**, pero también puede ser **aceptado** para operaciones de larga duración.
    * *Cuando* el evento se ha producido.
    * *Quién* realizó la operación, si hay alguien. No todas las operaciones realizadas por usuarios, algunos se realizan servicios de back-end para que no tuvieran un **llamador**.
    * El **Identificador de correlación** del evento - éste es el identificador único para este conjunto de operaciones.

5. Desde allí puede ir a la hoja de detalles para ver los detalles del evento.

    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Para los eventos de **error** , esta página incluye normalmente un **subestado** y una sección de **Propiedades** que incluyen detalles útiles con fines de depuración.

## <a name="filter-to-specific-logs"></a>Filtrar registros específicos

Para ver los eventos que se aplican a una entidad concreta o de un tipo específico, puede filtrar la hoja de registros de auditoría haciendo clic en el comando de **filtro** . También se pueden utilizar la hoja de filtro para cambiar el **intervalo de tiempo** de la hoja de registros de auditoría.

Cuando hace clic en este comando, se abrirá una nueva hoja:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Hay cuatro tipos de filtros:

1. Por suscripción
2. Un **grupo de recursos**
3. Un **tipo de recurso**
4. Por un determinado **recurso** - para esto deberá más allá en la completa *Identificador de recurso* del recurso que le interese

Además, también puede filtrar eventos por quién realizó el evento o, por el nivel del evento.

Una vez haya terminado de elegir lo que desea ver, haga clic en el botón **Actualizar** en la parte inferior de la hoja.

## <a name="monitor-events-impacting-specific-resources"></a>Supervisar sucesos de afectar recursos específicos

1. Haga clic en **Examinar** para buscar el recurso que le interese. También puede ver todos los registros de para todo un **grupo de recursos**.
2. En la hoja de recursos, desplácese hacia abajo hasta que encuentre el mosaico de **eventos** .  
    ![Mosaico de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Haga clic en esta ficha para ver los eventos filtrados a sólo con el recurso seleccionado. Puede utilizar el comando de **filtro** para cambiar el intervalo de tiempo o aplicar filtros más específicos.

## <a name="next-steps"></a>Próximos pasos

* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) siempre que ocurre un evento.
* [Métricas de servicio de monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Estado del servicio de seguimiento](insights-service-health.md) para averiguar cuándo Azure ha experimentado interrupciones de degradación o de servicio de rendimiento.  
