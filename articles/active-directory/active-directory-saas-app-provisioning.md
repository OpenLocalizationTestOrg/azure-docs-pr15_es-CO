<properties
    pageTitle="Automatizada en Azure AD de aprovisionamiento de usuarios de la aplicación de SaaS | Microsoft Azure"
    description="Una introducción a cómo puede utilizar AD Azure suministrar automáticamente, desaprovisionamiento y actualizar continuamente las cuentas de usuario en varias aplicaciones de SaaS de terceros."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar el usuario de aprovisionamiento y baja para las aplicaciones SaaS con Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>¿Qué es la provisión de usuarios automatizada para las aplicaciones SaaS?

Azure (Azure AD) de Active Directory le permite automatizar la creación, mantenimiento y eliminación de identidades de usuario en aplicaciones de nube ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow y mucho más.

**A continuación se muestran algunos ejemplos de lo que le permite hacer:**

- Automáticamente crear cuentas nuevas en las aplicaciones SaaS derecho para gente nueva cuando se unan a su equipo.
- Desactivar automáticamente las cuentas de la aplicaciones de SaaS cuando inevitablemente la gente deja el equipo.
- Asegúrese de que las identidades de las aplicaciones SaaS se mantienen actualizadas según los cambios en el directorio.
- Proporcionar objetos de no usuario, tales como grupos de aplicaciones de SaaS que les dan soporte.

**Provisioning automatizado usuario también incluye la funcionalidad siguiente:**

- La capacidad de hacer coincidir las identidades existentes entre AD Azure y aplicaciones de SaaS.
- Opciones de personalización para Ayuda AD Azure ajustan las configuraciones de las aplicaciones de SaaS que utiliza actualmente su organización actuales.
- Alertas de correo electrónico opcional para errores de configuración.
- Informes y registros de actividades para ayudar con la supervisión y solución de problemas.

##<a name="why-use-automated-provisioning"></a>¿Por qué utilizar Provisioning automatizado?

Incluyen algunas motivaciones comunes para el uso de esta característica:

- Para evitar los costos, las ineficiencias y errores humanos asociados con los procesos de creación manual.
- Para proteger su organización quitando instantáneamente las identidades de los usuarios de aplicaciones clave de SaaS cuando abandonan la organización.
- Para importar fácilmente una cantidad masiva de usuarios en una determinada aplicación SaaS.
- Para disfrutar de la comodidad de que su solución de aprovisionamiento se ejecute fuera de las mismas directivas de acceso de la aplicación que ha definido para Azure AD Single Sign-On.

##<a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Con qué frecuencia escribir AD Azure cambios de directorio para la aplicación de SaaS?**

Azure AD comprueba si hay cambios cada cinco a diez minutos. Si la aplicación SaaS devuelve varios errores (como en el caso de las credenciales de administrador no válido), AD Azure disminuya gradualmente su frecuencia a hasta una vez al día hasta que se corrigen los errores.

**¿Cuánto se tarda en suministrar Mis usuarios?**

Cambios incrementales se realizan casi al instante, pero si intenta proporcionar la mayor parte de su directorio, luego depende el número de usuarios y grupos que tiene. Directorios pequeños tardan sólo unos minutos, medianas directorios pueden tardar varios minutos y directorios muy grande pueden tardar varias horas.

**¿Cómo se puede seguir el progreso del trabajo de aprovisionamiento actual?**

Puede revisar el informe de Provisioning de cuentas en la sección informes de su directorio. Otra opción es visitar la solapa escritorio para la aplicación de SaaS que son provisioning a y busque en la sección "Estado de integración" cerca de la parte inferior de la página.

**¿Cómo sé si los usuarios no podrán obtener aprovisionado correctamente?**

Al final de la configuración de aprovisionamiento Asistente allí es una opción para suscribirse a las notificaciones de correo electrónico para errores de provisioning. También puede comprobar el informe de errores de Provisioning para ver los usuarios que no se ha podido hacer provisioning y por qué.

**¿Puede AD Azure escribir los cambios de la aplicación de SaaS en el directorio?**

Para la mayoría de las aplicaciones SaaS, provisioning es sólo saliente, lo que significa que los usuarios se escriben desde el directorio de la aplicación y cambios de la aplicación no se volverán a escribir en el directorio. Para el [día laborable](https://msdn.microsoft.com/library/azure/dn762434.aspx), sin embargo, provisioning es sólo de entrada, lo que significa que se importan los usuarios en el directorio desde el día laborable y, asimismo, cambios en el directorio no se escribieron atrás en día laborable.

**¿Cómo puedo enviar comentarios al equipo de ingeniería?**

Póngase en contacto con nosotros a través del [área de votaciones de Azure de Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>¿Cómo trabajo Provisioning automatizado?

AD Azure Aprovisiona los usuarios a aplicaciones de SaaS conectándose al provisioning extremos de proveedor de la aplicación. Estos extremos permiten AD Azure crear, actualizar y quitar usuarios mediante programación. A continuación es una breve introducción de los diferentes pasos que toma AD Azure para automatizar el provisioning.

1. Cuando se habilita el aprovisionamiento de una aplicación por primera vez, se realizan las siguientes acciones:
 - Azure AD intentará asociar todos los usuarios existentes en la aplicación de SaaS con sus identidades correspondientes en el directorio. Cuando se asocia a un usuario, *no* se habilita automáticamente para el inicio de sesión único. A fin de que un usuario tenga acceso a la aplicación, deben explícitamente asignarse a la aplicación en Azure AD, ya sea directamente o a través de la pertenencia al grupo.
 - Si ya ha especificado qué usuarios deben asignarse a la aplicación, y si se produce un error en Azure AD buscar cuentas existentes de los usuarios, AD Azure proporcionará nuevas cuentas para ellos en la aplicación.
2. Una vez que se ha completado la sincronización inicial como se describe anteriormente, AD Azure comprobará cada 10 minutos para que los cambios siguientes:
 - Si se han asignado nuevos usuarios a la aplicación (ya sea directamente o a través de la pertenencia a un grupo), a continuación, estarán aprovisionar una nueva cuenta en la aplicación de SaaS.
 - Si se ha quitado el acceso de un usuario, su cuenta en la aplicación de SaaS se marcará como deshabilitada (los usuarios son totalmente nunca eliminados, que protege contra la pérdida de datos en caso de un error de configuración).
 - Si un usuario se ha asignado recientemente a la aplicación y ya tenía una cuenta en la aplicación de SaaS, dicha cuenta se marcará como habilitada y ciertas propiedades de usuario pueden actualizarse si están obsoletos en comparación con el directorio.
 - Si se ha cambiado la información del usuario (por ejemplo, número de teléfono, ubicación de la oficina, etcetera) en el directorio, también se actualizará dicha información en la aplicación de SaaS.

Para obtener más información sobre cómo se asignan los atributos entre AD Azure y su aplicación SaaS, vea el artículo sobre [Asignaciones de atributos de personalización](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Lista de aplicaciones que admitan distribución automática del usuario

Haga clic en una aplicación para ver un tutorial sobre cómo configurar el provisioning automatizado para él:

- [Cuadro de](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Concurren](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Lista desplegable para el negocio](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Fuerza de ventas](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Recinto de la fuerza de ventas](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Día laborable](http://go.microsoft.com/fwlink/?LinkId=690250) (aprovisionamiento entrante)

En orden para una aplicación para apoyar el provisioning automatizado usuario, en primer lugar debe proporcionar los extremos necesarios que permiten programas externos automatizar la creación, mantenimiento y eliminación de usuarios. Por lo tanto, no todas las aplicaciones de SaaS son compatibles con esta característica. El equipo de ingeniería de Azure AD será capaz de crear un conector de aprovisionamiento a las aplicaciones para las aplicaciones que lo admiten, y este trabajo se asignan prioridades según las necesidades de los clientes actuales y potenciales.

Para ponerse en contacto con el equipo de ingeniería de Azure AD para solicitar soporte técnico aprovisionamiento de aplicaciones adicionales, envíe un mensaje a través del [área de votaciones de Azure de Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos para la administración de la aplicación en Azure Active Directory](active-directory-apps-index.md)
- [Personalizar las asignaciones de atributo para el aprovisionamiento de usuario](active-directory-saas-customizing-attribute-mappings.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Filtros para el aprovisionamiento de usuarios de ámbito](active-directory-saas-scoping-filters.md)
- [Uso de SCIM para permitir la provisión automática de usuarios y grupos de Active Directory de Azure para aplicaciones](active-directory-scim-provisioning.md)
- [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)
