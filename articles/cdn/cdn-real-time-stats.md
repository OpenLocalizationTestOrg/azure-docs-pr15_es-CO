<properties
    pageTitle="Real-Time-Stats en Azure CDN | Microsoft Azure"
    description="Estadísticas en tiempo real proporciona datos en tiempo real acerca del rendimiento de Azure CDN al entregar contenido a los clientes."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estadísticas en tiempo real de Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general

Este documento explica las estadísticas en tiempo real de Microsoft Azure CDN.  Esta funcionalidad proporciona datos en tiempo real, como el ancho de banda, Estados de memoria caché y conexiones simultáneas a su perfil CDN al entregar contenido a los clientes. Esto permite la supervisión continua del estado de su servicio en cualquier momento, incluidos los eventos de puesta en marcha.

Los gráficos siguientes están disponibles:

* [Ancho de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados de la memoria caché](#cache-statuses)
* [Conexiones](#connections)


## <a name="accessing-real-time-stats"></a>Acceso a estadísticas en tiempo real

1. En el [Portal de Azure](https://portal.azure.com), vaya a su perfil CDN.

    ![Hoja de perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)

2. En el módulo perfil CDN, haga clic en el botón **Administrar** .

    ![Hoja de perfil CDN botón Administrar](./media/cdn-real-time-stats/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

3. Mantenga el mouse sobre la ficha de **análisis** y luego desplace el puntero sobre la barra flotante de **Estadísticas en tiempo real** .  Haga clic en el **objeto grande de HTTP**.

    ![Portal de administración de CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)

    Se muestran los gráficos de estadísticas en tiempo real.
    
Cada uno de los gráficos muestra estadísticas en tiempo real para el intervalo de tiempo seleccionado, a partir de cuando se carga la página.  Los gráficos se actualizan automáticamente cada pocos segundos.  El botón **Actualizar gráfico** , si está presente, borrará el gráfico, después del cual sólo se mostrarán los datos seleccionados.

## <a name="bandwidth"></a>Ancho de banda

![Gráfico de ancho de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

El gráfico de **ancho de banda** muestra la cantidad de ancho de banda utilizado para la plataforma actual durante el período de tiempo seleccionado. La parte sombreada del gráfico indica el uso de ancho de banda. La cantidad exacta del ancho de banda se utiliza actualmente se muestra directamente debajo de la línea de gráfico.

## <a name="status-codes"></a>Códigos de estado

![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

El gráfico de **Códigos de estado** indica la frecuencia con ciertos códigos de respuesta HTTP se están produciendo en el intervalo de tiempo seleccionado.

> [AZURE.TIP]  Para obtener una descripción de cada opción de código de estado HTTP, vea [Códigos de estado HTTP de Azure CDN](https://msdn.microsoft.com/library/mt759238.aspx).

Se muestra una lista de códigos de estado HTTP directamente encima del gráfico. Esta lista indica cada código de estado que se puede incluir en el gráfico de líneas y el número de veces por segundo para ese código de estado actual. De forma predeterminada, se muestra una línea para cada uno de estos códigos de estado en el gráfico. Sin embargo, puede elegir supervisar sólo los códigos de estado que tienen un significado especial para su configuración de CDN. Para ello, comprobar los códigos de estado deseado y desactive todas las demás opciones, haga clic en **Actualizar el gráfico**. 

Puede ocultar temporalmente los datos registrados para un código de estado en particular.  En la leyenda directamente debajo del gráfico, haga clic en el código de estado que desee ocultar. El código de estado se ocultarán inmediatamente desde el gráfico. Vuelva a hacer clic en ese código de estado hará que esa opción mostrarlo de nuevo.

## <a name="cache-statuses"></a>Estados de la memoria caché

![Gráfico de Estados de memoria caché](./media/cdn-real-time-stats/cdn-cache-status.png)

El gráfico de **Estados de caché** indica ¿con qué frecuencia se producen ciertos tipos de Estados de la memoria caché durante el período de tiempo seleccionado. 

> [AZURE.TIP]  Para obtener una descripción de cada opción de código de estado de caché, consulte los [Códigos de estado de caché de Azure CDN](https://msdn.microsoft.com/library/mt759237.aspx).

Una lista de códigos de estado de la memoria caché se muestra directamente sobre el gráfico. Esta lista indica cada código de estado que se puede incluir en el gráfico de líneas y el número de veces por segundo para ese código de estado actual. De forma predeterminada, se muestra una línea para cada uno de estos códigos de estado en el gráfico. Sin embargo, puede elegir supervisar sólo los códigos de estado que tienen un significado especial para su configuración de CDN. Para ello, comprobar los códigos de estado deseado y desactive todas las demás opciones, haga clic en **Actualizar el gráfico**. 

Puede ocultar temporalmente los datos registrados para un código de estado en particular.  En la leyenda directamente debajo del gráfico, haga clic en el código de estado que desee ocultar. El código de estado se ocultarán inmediatamente desde el gráfico. Vuelva a hacer clic en ese código de estado hará que esa opción mostrarlo de nuevo.

## <a name="connections"></a>Conexiones

![Gráfico de conexiones](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica cuántas conexiones se han establecido para los servidores de borde. Cada solicitud de un activo que pasa a través de nuestros resultados CDN en una conexión.

## <a name="next-steps"></a>Próximos pasos

- Obtener notificaciones con [alertas en tiempo real en Azure CDN](cdn-real-time-alerts.md)
- Profundizar con [informes avanzados de HTTP](cdn-advanced-http-reports.md)
- Analizar [patrones de uso](cdn-analyze-usage-patterns.md)

