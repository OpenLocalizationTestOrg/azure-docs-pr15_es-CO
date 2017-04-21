<properties
    pageTitle="Concentradores de notificación de Azure"
    description="Aprenda a utilizar las notificaciones de inserción en Azure. Ejemplos de código escritos en utilizando la API de .NET C#."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Concentradores de notificación de Azure

##<a name="overview"></a>Información general

Concentradores de notificación Azure proporcionan una infraestructura de inserción fácil de usar, multiplataforma, escala horizontal que permite enviar las notificaciones de inserción móvil desde cualquier back-end (en la nube o local) para cualquier plataforma móvil.

Con concentradores de notificación, puede enviar fácilmente las notificaciones de inserción personalizada en varias plataformas, abstraer los detalles de los sistemas de notificación de plataforma diferente (SNP). Con una sola llamada API, puede dirigirse a usuarios individuales o segmentos de audiencia completo que contiene millones de usuarios, a través de todos sus dispositivos.

Puede utilizar Notification concentradores para escenarios empresariales y para consumidores. Por ejemplo:

- Enviar notificaciones de noticias de última hora a millones con una baja latencia (concentradores de notificación potencia aplicaciones de Bing preinstaladas en todos los dispositivos de Windows y Windows Phone).
- Enviar cupones de ubicación a segmentos de usuarios.
- Enviar notificaciones de eventos a usuarios o grupos de finanzas/deportes o juegos las aplicaciones.
- Notifique a los usuarios de eventos de la empresa como nuevos mensajes y correos electrónicos y clientes potenciales de ventas.
- Enviar un-: contraseñas necesario para la autenticación con varios factores.



##<a name="what-are-push-notifications"></a>¿Qué son las notificaciones Push?

Smartphones y tabletas pueden "notificar" los usuarios cuando se ha producido un evento. Estas notificaciones pueden adoptar muchas formas.

En las aplicaciones de almacén de Windows y Windows Phone, la notificación puede ser en forma de un _brindis_: aparece una ventana no modal, con un sonido para indicar una nueva notificación. Otros tipos de notificación que se admiten incluyen notificaciones de _mosaico_, _raw_y _divisa_ . Para obtener más información sobre los tipos de notificaciones admitidos en los dispositivos de Windows, vea [mosaicos, insignias y las notificaciones](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

En dispositivos de Apple iOS, la inserción de igual forma comunica al usuario con un cuadro de diálogo solicitando al usuario que vea o la notificación de cierre. Haciendo clic en **vista** , se abre la aplicación que recibe el mensaje. Para obtener más información sobre iOS notificaciones, vea [notificaciones de iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

Las notificaciones de inserción ayudan a dispositivos móviles mostrar información reciente mientras aún se consumo. Las notificaciones pueden enviarse por sistemas backend para dispositivos móviles, incluso cuando no están activas las aplicaciones correspondientes en un dispositivo. Las notificaciones de inserción son un componente vital para las aplicaciones de consumidor, donde se utilizan para aumentar la utilización y contratación de la aplicación. Las notificaciones también son útiles para las empresas, cuando la información actualizada aumenta la capacidad de respuesta empleado para eventos de negocio.

Algunos ejemplos específicos de los escenarios móviles de contratación son:

1.  Actualizando un mosaico en Windows 8 o Windows Phone con información financiera actual.
2.  Un usuario con un brindis que algún elemento de trabajo se ha asignado a ese usuario en una aplicación empresarial basada en el flujo de trabajo de alerta.
3.  Mostrar una tarjeta con el número de ventas actuales es líder en una aplicación CRM (por ejemplo, Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>Funcionamiento trabajo de notificaciones de inserción

Las notificaciones de inserción se entregan a través de infraestructuras específicas de la plataforma llamadas a _Sistemas de notificación de la plataforma_ (SNP). Un PNS ofrece funciones de barebones (es decir, no hay soporte para difusión, personalización) y no tienen ninguna interfaz común. Por ejemplo, para enviar una notificación a la aplicación de un almacén de Windows, un desarrollador debe ponerse en contacto con el WNS (servicio de notificación de Windows). Para enviar una notificación a un dispositivo iOS, el mismo programador tiene que ponerse en contacto con APN (Apple Push Notification Service) y enviar el mensaje una segunda vez. Concentradores de notificación Azure ayuda proporcionando una interfaz común, junto con otras características para soportar las notificaciones de inserción en cada plataforma.

Sin embargo, en un nivel alto, todos los sistemas de notificación de plataforma siguen el mismo patrón:

1.  La aplicación cliente se pone en contacto el PNS para recuperar su _controlar_. El tipo de identificador depende del sistema. Para WNS, es un URI o "canal de notificación". Por APN, es un símbolo (token).
2.  La aplicación cliente almacena este identificador en la aplicación _back-end_ para su posterior uso. Para WNS, el back-end es normalmente un servicio de nube. Para Apple, el sistema se llama a un _proveedor_.
3.  Para enviar una notificación de inserción, el back-end de la aplicación se pone en contacto con los Pagarés utilizando el controlador para una instancia de cliente específicos de la aplicación de destino.
4.  Los Pagarés reenvía la notificación en el dispositivo especificado por el identificador.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Los desafíos de notificaciones de inserción

Mientras que estos sistemas son muy eficaces, todavía dejan mucho trabajo para el desarrollador de la aplicación con el fin de implementar los escenarios de notificación push incluso comunes, como difusión o enviar las notificaciones de inserción a usuarios segmentados.

Las notificaciones de inserción son una de las características más solicitadas en los servicios de nube para aplicaciones móviles. La razón de esto es que la infraestructura necesaria para que funcionen es bastante complejo y principalmente relacionado con la lógica de negocio principal de la aplicación. Algunos de los desafíos en la construcción de una infraestructura de inserción bajo demanda son:

- **Dependencia de la plataforma.** Para enviar notificaciones a dispositivos en diferentes plataformas, múltiples interfaces deben codificarse en el back-end. No sólo son distintos los detalles de bajo nivel, pero la presentación de la notificación (mosaico, pan tostado o divisa) es también depende de la plataforma. Estas diferencias pueden producir código de fondo compleja y difícil de mantener.

- **Escala.** Escala esta infraestructura tiene dos aspectos:
    + Las guías de Pagarés, tokens de dispositivo deben actualizarse cada vez que se inicia la aplicación. Esto conduce a una gran cantidad de tráfico (y accesos consecutivas de la base de datos) sólo para mantener actualizados los tokens de dispositivo. Cuando aumenta el número de dispositivos (posiblemente a millones), el costo de la creación y el mantenimiento de esta infraestructura es nonnegligible.

    + PNSs la mayoría no admiten difusión a varios dispositivos. Se deduce que una difusión a millones de dispositivos como resultado millones de llamadas a la PNSs. Ser capaz de escalar estas solicitudes es no trivial, porque normalmente los desarrolladores de aplicaciones desean reducir la latencia total. Por ejemplo, el último dispositivo para recibir el mensaje no debe recibir la notificación 30 minutos después de enviar las notificaciones, en cuanto a muchos casos anularía el propósito para que las notificaciones de inserción.
- **El enrutamiento.** PNSs proporcionan una manera de enviar un mensaje a un dispositivo. Sin embargo, en la mayoría de las aplicaciones las notificaciones están dirigidas a usuarios o grupos de interés (por ejemplo, todos los empleados asignados a una determinada cuenta de cliente). Por lo tanto, para poder enrutar las notificaciones a los dispositivos correctos, el back-end de la aplicación debe mantener un registro que asocia los grupos de interés con tokens de dispositivo. Esta sobrecarga agrega el tiempo total de los costos de mercado y mantenimiento de una aplicación.

##<a name="why-use-notification-hubs"></a>¿Por qué usar concentradores de notificación?

Notificación de concentradores eliminan la complejidad: no es necesario administrar los desafíos de notificaciones de inserción. En su lugar, puede utilizar un concentrador de notificación. Concentradores de notificación utilizar una infraestructura de notificación push multiplataforma completa, escala horizontal y reducen considerablemente el código específico de inserción que se ejecuta en el servidor de la aplicación. Concentradores de notificación implementan toda la funcionalidad de una infraestructura de inserción. Los dispositivos sólo están responsables de registrar controladores de Pagarés y el back-end es responsable de enviar mensajes independientes de la plataforma a usuarios o grupos de interés, como se muestra en la figura siguiente:

![][1]


Concentradores de notificación proporcionan una infraestructura de notificación de inserción listos para usar con las siguientes ventajas:

- **Varias plataformas.**
    +  Compatibilidad con todas las principales plataformas móviles. Concentradores de notificación pueden enviar notificaciones de inserción para aplicaciones de Windows Store, iOS, Android y Windows Phone.

    +  Concentradores de notificación proporcionan una interfaz común para enviar notificaciones a todas las plataformas compatibles. No se requieren protocolos específicos de la plataforma. El back-end de la aplicación puede enviar notificaciones en formatos específicos de la plataforma o independiente de la plataforma. La aplicación sólo se comunica con concentradores de notificación.

    +  Administración de controlador de dispositivo. Concentradores de notificación mantiene el registro de identificador y los comentarios de PNSs.

- **Funciona con cualquier back-end**: nube o local,. NET, PHP, Java, nodo, etcetera.

- **Escala.** Concentradores de notificación escalan a millones de dispositivos sin la necesidad de volver a diseñar o fragmento.


- **Conjunto enriquecido de los patrones de entrega**:

    - *Difusión*: permite la difusión casi simultánea a millones de dispositivos con una sola llamada API.

    - *Unidifusión y multidifusión*: insertar en etiquetas que representan a usuarios individuales, incluidos todos sus dispositivos; o un grupo más amplio; Por ejemplo, factores de forma independiente (Tablet PC y teléfono).

    - *Segmentación*: empujar a segmento complejo definido por expresiones de etiqueta (por ejemplo, dispositivos de Nueva York siguiendo los Yankees).

    Cada dispositivo, al enviar su identificador a un concentrador de notificación, puede especificar una o más _etiquetas_. Para obtener más información acerca de [las etiquetas]. Etiquetas no deben proporcionarse previamente o desechado. Las etiquetas proporcionan una manera sencilla de enviar notificaciones a usuarios o grupos de interés. Dado que las etiquetas pueden contener cualquier identificador específico de la aplicación (por ejemplo, identificadores de usuario o grupo), su uso libera el back-end de la aplicación de la carga de tener que almacenar y administrar controladores de dispositivo.

- **Personalización**: cada dispositivo puede tener una o varias plantillas para lograr la personalización y localización de cada dispositivo sin afectar al código de back-end.

- **Seguridad**: acceso secreto (SAS) o autenticación federados compartidas.

- **Telemetría enriquecido**: disponible en el portal y mediante programación.


##<a name="integration-with-app-service-mobile-apps"></a>Integración con aplicaciones móviles de servicio de la aplicación

Para facilitar una experiencia perfecta y unificar los servicios de Azure, [Aplicaciones móviles de servicio de aplicación] tiene compatibilidad integrada para las notificaciones de inserción mediante concentradores de notificación. [Aplicación servicio Mobile aplicaciones] ofrece una plataforma de desarrollo de aplicaciones móviles altamente escalables, disponibles globalmente para los desarrolladores empresariales y los integradores de sistema que aporta un amplio conjunto de capacidades a los programadores móviles.

Los desarrolladores de aplicaciones móviles pueden utilizar concentradores de notificación con el flujo de trabajo siguiente:

1. Recuperar el identificador de dispositivo PNS
2. Registrar dispositivo y [plantillas] con concentradores de notificación mediante la API de registro de SDK de cliente de Apps Mobile conveniente
    + Tenga en cuenta que aplicaciones móviles quita todas las etiquetas en los registros por motivos de seguridad. Trabajar con concentradores de notificación desde el back-end directamente para asociar etiquetas a los dispositivos.
3. Enviar notificaciones desde el back-end de la aplicación con concentradores de notificación

A continuación le presentamos algunas ventajas a los desarrolladores con esta integración:

- **SDK de cliente de aplicaciones móviles.** Estos SDK multiplataformas proporcionan API simples para el registro y hablan con el concentrador de notificación vinculado automáticamente con la aplicación móvil. Los desarrolladores no es necesario indagar a través de credenciales de notificación concentradores y trabajar con un servicio adicional.
    + Los SDK de etiquetar automáticamente el dispositivo con aplicaciones móviles de ID de usuario autenticado para habilitar la inserción en el escenario de usuario dado.
    + Los SDK utilizan automáticamente el identificador de instalación de aplicaciones móviles como GUID para registrar con concentradores de notificación, ahorrar a los desarrolladores el problema de mantener varios GUID de servicio.
    
- **Modelo de instalación.** Aplicaciones de Mobile funciona con el último modelo de inserción de notificación concentradores para representar todas las propiedades de inserción asociadas con un dispositivo en una instalación de JSON que se alinea con la inserción de Notification Services y es fácil de usar.

- **Flexibilidad.** Los desarrolladores pueden elegir siempre funcione con concentradores de notificación directamente incluso con la integración en su lugar.

- **Experiencia integrada en [el portal de Azure].** Empuje como una capacidad se representa visualmente en aplicaciones móviles y los desarrolladores pueden trabajar fácilmente con el concentrador de notificación asociados a través de aplicaciones móviles.



##<a name="next-steps"></a>Próximos pasos

Puede encontrar más información acerca de los concentradores de notificación en los siguientes temas:

+ **[Cómo los clientes utilizan concentradores de notificación]**

+ **[Guías y tutoriales de concentradores de notificación]**

+ **Tutoriales de notificación concentradores Introducción** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

.NET relevantes administradas referencias de API para las notificaciones de inserción se encuentra aquí:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Cómo los clientes utilizan concentradores de notificación]: http://azure.microsoft.com/services/notification-hubs
  [Guías y tutoriales de concentradores de notificación]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Encender]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Servicio de aplicación de aplicaciones móviles]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [plantillas]: notification-hubs-templates-cross-platform-push-messages.md
  [Portal de Azure]: https://portal.azure.com
  [etiquetas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
