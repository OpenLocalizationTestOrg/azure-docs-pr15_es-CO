<properties
    pageTitle="Empezar a trabajar con los ejes del evento en C# | Microsoft Azure"
    description="Siga este tutorial para comenzar a utilizar Azure evento concentradores con C# y utilizando la EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Empezar a trabajar con los ejes del evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Concentradores de eventos es un servicio que procesa grandes cantidades de datos de eventos (telemetría) de las aplicaciones y los dispositivos conectados. Después de recopilar datos en concentradores de evento, puede almacenar los datos mediante un clúster de almacenamiento o transformarlos mediante un proveedor de análisis en tiempo real. Esta capacidad de recopilación y procesamiento de eventos a gran escala es un componente clave de arquitecturas de aplicaciones modernas, incluyendo Internet de las cosas (IoT).

Este tutorial muestra cómo utilizar el portal Azure clásico para crear un evento de concentrador. También muestra cómo recopilar mensajes a un concentrador de eventos mediante una aplicación de consola escrita en C# y cómo recuperarlos en paralelo utilizando la biblioteca C# [Procesador anfitrión del evento][] .

Para completar este tutorial, necesitará lo siguiente:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Una cuenta activa de Azure. Si no tiene uno, puede crear una cuenta gratuita en sólo un par de minutos. Para obtener más información, vea [Versión de prueba gratuita de Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora está listo para ejecutar las aplicaciones.

1. Desde Visual Studio, abra el proyecto de **receptor** que creó anteriormente.

2. (Ratón) en la solución de **receptor** , a continuación, haga clic en **Agregar**y, a continuación, haga clic en **Proyecto existente**.
 
3. Busque el archivo Sender.csproj existente, a continuación, haga doble clic en él para agregarlo a la solución.
 
4. De nuevo, haga clic derecho en la solución del **receptor** y, a continuación, haga clic en **Propiedades**. Se muestra la página de propiedades del **receptor** .

5. Haga clic en **Proyecto de inicio**, haga clic en el botón **proyectos de inicio múltiples** . Establezca el cuadro de **acción** para proyectos el **receptor** y el **emisor** para **Iniciar**.

    ![][19]

6. Haga clic en **dependencias del proyecto**. En el cuadro de **proyectos** , haga clic en **remitente**. En el cuadro **depende** , asegúrese de que está activado el **receptor** .

    ![][20]

7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades** .

1.  Presione F5 para ejecutar el proyecto **receptor** desde dentro de Visual Studio y espere a los receptores para todas las particiones de inicio.

    ![][21]

2.  El proyecto de **remitente** se ejecutará automáticamente. Presione **ENTRAR** en la ventana de la consola y ver los eventos que aparecen en la ventana del receptor.

    ![][22]

Presione **CTRL+c** en la ventana del **remitente** para finalizar la aplicación remitente y presione **ENTRAR** en la ventana del receptor para cerrar dicha aplicación.

## <a name="next-steps"></a>Próximos pasos

Ahora que ha creado una aplicación operativa que crea un concentrador de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

- Una completa [aplicación de ejemplo que utiliza el evento concentradores][].
- El ejemplo de [escalado horizontal del procesamiento de eventos con eventos concentradores][] .
- [Resumen de eventos concentradores][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Host del procesador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Resumen de eventos concentradores]: event-hubs-overview.md
[aplicación de ejemplo que utiliza el evento concentradores]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalado horizontal del procesamiento de eventos con concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
