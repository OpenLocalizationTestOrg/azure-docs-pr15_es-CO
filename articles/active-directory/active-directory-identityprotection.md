<properties
    pageTitle="Protección de la identidad de Active Directory Azure | Microsoft Azure"
    description="Conozca cómo la protección de la identidad de AD de Azure permite limitar la capacidad de un atacante para aprovechar un dispositivo o identidad en peligro y proteger una identidad o un dispositivo que se sospecha o sabe que poner en peligro."
    services="active-directory"
    keywords="protección de la identidad de directorio activo Azure, descubrimiento de app de nube, administración de aplicaciones, seguridad, riesgos, nivel de riesgo, vulnerabilidad, directiva de seguridad"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Protección de la identidad de Azure de Active Directory 

Protección de identidad de directorio activo Azure es una característica de la edición Premium de AD de Azure P2 que proporciona una vista consolidada en eventos de riesgo y las posibles vulnerabilidades que afectan a las identidades de su organización. Microsoft ha Proteger identidades basados en cloud para más de una década, y con la protección de la identidad de AD de Azure, Microsoft está realizando estos mismos sistemas de protección disponibles para los clientes empresariales. Protección de identidad aprovecha las capacidades de detección de anomalías existentes Azure del anuncio (disponibles a través de informes de actividad anómala de Azure AD) e introduce a nuevos tipos de eventos de riesgo que pueden detectar anomalías en tiempo real.



##<a name="getting-started"></a>Introducción

La mayoría de las infracciones de seguridad tienen lugar cuando los atacantes tener acceso a un entorno por robo de identidad de un usuario. Los atacantes han vuelto cada vez más eficaces en el aprovechamiento de las infracciones de terceros y el uso de ataques de phishing sofisticado. Una vez que un atacante obtiene acceso a una cuenta de usuario con pocos privilegios incluso, es relativamente sencillo para obtener acceso a los recursos importantes de la empresa a través de movimiento lateral. Por lo tanto, es esencial para proteger todas las identidades y, cuando una identidad está en peligro, proactivamente impedir la identidad en peligro abusivos. 

Descubrir identidades en peligro no es tarea fácil. Afortunadamente, puede ayudar la protección de identidad: protección de identidad utiliza algoritmos de aprendizaje automático adaptable y heurística para detectar anomalías y el riesgo de incidentes que puedan indicar que se ha comprometido una identidad.
 
Con estos datos, protección de identidad genera informes y alertas que le permite investigar estos sucesos de riesgo y tomar las medidas oportunas o acción de reducción.
 
Pero la protección de identidad de Azure Active Directory es más que una herramienta de monitoreo y reporting. Basándose en los eventos de riesgo, protección de identidad calcula un nivel de riesgo de usuario para cada usuario, lo que le permite configurar directivas basadas en riesgos para proteger automáticamente la identidad de su organización.  Estas directivas basadas en riesgos, además de otros controles de acceso condicional, proporcionadas por Active Directory de Azure y EMS, pueden bloquear automáticamente o se ofrecen acciones de corrección adaptable que incluyen los restablecimientos de contraseña y exigir la autenticación multifactor.  

####<a name="explore-identity-protections-capabilities"></a>Explorar las capacidades de protección de la identidad 

**Detección de eventos de riesgo y arriesgadas cuentas:**  

- Detectar los 6 tipos de eventos de riesgo con el aprendizaje de máquinas y reglas heurísticas 

- Calcular los niveles de riesgo de usuario

- Proporcionar recomendaciones personalizadas para mejorar la posición de seguridad general resaltando las vulnerabilidades



**Investigar los eventos de riesgo:**

- Envío de notificaciones para eventos de riesgo

- Investigar los eventos de riesgo con información relevante y contextual

- Proporcionar flujos de trabajo básicos para realizar el seguimiento de las investigaciones

- Proporcionar un fácil acceso a las acciones de corrección, como el restablecimiento de contraseña



**Directivas de acceso condicional basado en el riesgo:**

- Directiva para mitigar el riesgo de inicios de sesión bloqueando los inicios de sesión o que requieren los desafíos de autenticación con varios factores.

- Directiva de cuentas de usuario de riesgo segura o bloque

- Directiva de exigir a los usuarios registrarse para la autenticación con varios factores


## <a name="detection-and-risk"></a>Detección y riesgo

### <a name="risk-events"></a>Eventos de riesgo

Eventos de riesgo son eventos que fueron marcados como sospechosos por protección de identidad e indican que una identidad han corrido. Para obtener una lista completa de los eventos de riesgo, consulte [tipos de eventos de riesgo detectados por protección de identidad de Azure Active Directory](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Nivel de riesgo

El nivel de riesgo de un evento de riesgo es una indicación (alta, Media o baja) de la gravedad del evento de riesgo. El nivel de riesgo ayuda a los usuarios de la protección de la identidad a dar prioridad a las acciones que debían adoptar para reducir el riesgo para su organización. La gravedad de los eventos de riesgo representa la intensidad de la señal como predictor de compromiso de identidad, combinado con la cantidad de ruido que presenta normalmente. 

- **Alta**: alta confianza y evento de riesgo de gravedad alta. Estos eventos son buenos indicadores que ha puesto en peligro la identidad del usuario y las cuentas de usuario afectadas deben UY inmediatamente.

- **Medio**: gravedad alta, pero el evento de riesgo de confianza más bajo, o viceversa. Estos eventos son potencialmente peligrosos y deben ser remediadas afectadas las cuentas de usuario.

- **Baja**: baja confianza y evento de riesgo de gravedad baja. Este evento no puede requerir una acción inmediata, pero cuando se combina con otros eventos de riesgo, puede proporcionar una indicación clara de que la identidad está en peligro. 


![Nivel de riesgo] (./media/active-directory-identityprotection/01.png "Nivel de riesgo")

 

Eventos de riesgo se identifican ya sea en **tiempo real**, o de procesamiento posterior tras el evento de riesgo ya ha tenido lugar (sin conexión). Actualmente la mayoría de eventos de riesgo en la protección de la identidad se calculan sin conexión y se muestran en la protección de la identidad dentro de 2 a 4 horas. Mientras evalúa en tiempo real, los eventos de riesgo en tiempo real aparecerá en la consola de protección de identidad a 5-10 minutos.

Varios clientes heredados no admiten actualmente prevención y detección de eventos de riesgo en tiempo real. Como resultado, los inicios de sesión desde estos clientes no se detecta o prevenidas en tiempo real.


## <a name="investigation"></a>Investigación
Su viaje a través de la protección de la identidad se inicia normalmente con el panel de protección de la identidad. 

![Corrección] (./media/active-directory-identityprotection/1000.png "Corrección")

El tablero le brinda acceso a:
 
- Informes como el **marcan de los usuarios de riesgo**, **eventos de riesgo** y **vulnerabilidades**
- Configuración como la configuración de **Directivas de seguridad**, **las notificaciones** y **el registro de autenticación con varios factores**
 

Normalmente es el punto de partida para la investigación, que es el proceso de revisión de las actividades, registros y otra información relevante relacionada con un evento de riesgo para decidir si son necesarias medidas de corrección o mitigación, y cómo la identidad estuviera en peligro y comprender cómo se utiliza la identidad en peligro.

Puede vincular las actividades de investigación a las [notificaciones de](active-directory-identityprotection-notifications.md) que Azure Active Directory Protection envía por correo electrónico.

Las secciones siguientes proporcionan con más detalles y los pasos que están relacionados con una investigación.  



## <a name="what-is-a-user-risk-level"></a>¿Qué es un nivel de riesgo de usuario?

Un nivel de riesgo de usuario es una indicación (alta, Media o baja) de la probabilidad de que la identidad del usuario se ha comprometido. Se calcula basándose en los eventos de riesgo de usuario que están asociados con la identidad del usuario. 

El estado de un evento de riesgo es **activo** o **cerrado**. Eventos de riesgo que están **activas** contribuyen al cálculo del riesgo de usuario. 

El nivel de riesgo de usuario se calcula mediante las siguientes entradas:

- Eventos de riesgo activo que afectan al usuario
- Nivel de riesgo de estos eventos 
- Si se han tomado las acciones de corrección 

![Riesgos de usuario] (./media/active-directory-identityprotection/1001.png "Riesgos de usuario")



Puede utilizar los niveles de riesgo del usuario para crear directivas de acceso condicional para impedir que los usuarios arriesgados identificarte u obligarles a cambiar su contraseña de forma segura. 


## <a name="closing-risk-events-manually"></a>Cierre manualmente los eventos de riesgo

En la mayoría de los casos, tome las acciones de corrección, como una contraseña segura que restablecer para cerrar automáticamente los eventos de riesgo. Sin embargo, esto no siempre posible.  
Por ejemplo, este es el caso, cuando:

- Se ha eliminado un usuario con los eventos de riesgo activo
- Una investigación revela que el usuario legítimo ha ha realizar un evento de riesgo informado

Eventos de riesgo que están **activas** suponen el cálculo de riesgo del usuario, tendrá que reducir manualmente un nivel de riesgo cerrando manualmente los eventos de riesgo.  
Durante el curso de investigación, puede tomar cualquiera de estas acciones, para cambiar el estado de un evento de riesgo:

![Acciones] (./media/active-directory-identityprotection/34.png "Acciones")

- **Resolver** - si después de investigar un evento de riesgo, ha realizado una acción de corrección apropiadas fuera de la protección de la identidad y cree que se debe considerar el evento de riesgo cerrado, marcar el evento como resuelto. Resolver eventos establecerá el riesgo status del evento a cerrado y el evento de riesgo ya no contribuirán al riesgo del usuario.

- **Marcar como falsos positivos** : en algunos casos, puede investigar un evento de riesgo y descubrir que se ha marcado incorrectamente como un riesgo. Puede ayudar a reducir el número de tales ocurrencias marcando el evento de riesgo como los falsos positivos. Esto le ayudará a la máquina de aprendizaje algoritmos para mejorar la clasificación de eventos similares en el futuro. El estado de eventos de falsos positivos es **cerrado** y ya no contribuyen al riesgo del usuario.

- **Omitir** - si no se ha realizado una acción de corrección, pero desea que se quitará de la lista activa el evento de riesgo, puede marcar un evento de riesgo omitir y se cerrará el estado del evento. Eventos omitidos no contribuyen al riesgo del usuario. Esta opción sólo debe utilizarse en circunstancias inusuales. 

- **Reactivar** - eventos de riesgo que se hayan cerrado manualmente (si elige **Omitir**, **falso positivo**o **resolver**) se pueden reactivar, el evento vuelve a establecer estado **activo**. Eventos de riesgo reactivados contribuyan a que el cálculo de nivel de riesgo de usuario. No se puede reactivar los eventos de riesgo cerrados mediante la corrección (como restablece una contraseña segura). 




**Para abrir el cuadro de diálogo de configuración relacionados**:

1. En la hoja de **protección de la identidad de AD de Azure** en **investigar**, haga clic en **eventos de riesgo**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1002.png "Restablecimiento de contraseña manual")

2. En la lista de **eventos de riesgo** , haga clic en un riesgo.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1003.png "Restablecimiento de contraseña manual")

2. En la hoja de riesgo, haga clic en un usuario.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1004.png "Restablecimiento de contraseña manual")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Cierre todos los eventos de riesgo para un usuario manualmente

En lugar de manualmente cerrar individualmente los eventos de riesgo para el usuario, protección de identidad de Azure Active Directory también proporciona un método para cerrar todos los eventos de un usuario con un solo clic.


![Acciones] (./media/active-directory-identityprotection/2222.png "Acciones")

Al hacer clic en **Descartar todos los eventos**, se cierran todos los eventos y el usuario afectado ya no está en peligro.



## <a name="remediating-user-risk-events"></a>Eventos de riesgo adecuadas de usuario

Una corrección es una acción para proteger una identidad o un dispositivo que sospecha o sabe que se ve comprometida anteriormente. Una acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve los eventos anteriores de riesgo asociados con el dispositivo o identidad.

Para corregir los eventos de riesgo del usuario, puede:

- Realizar una contraseña segura que restablecer para corregir manualmente los eventos de riesgo del usuario 

- Configurar una directiva de seguridad de usuario riesgo para mitigar o remediar automáticamente los eventos de riesgo del usuario

- Crear una nueva imagen del dispositivo infectado  


### <a name="manual-secure-password-reset"></a>Restablecimiento de contraseña manual de seguro

Un restablecimiento de contraseña segura es un remedio eficaz para muchos de los eventos de riesgo y cuándo realizar, automáticamente se cierra estos eventos de riesgo y vuelve a calcular el nivel de riesgo del usuario. Puede utilizar el panel de protección de identidad para iniciar un restablecimiento de un usuario arriesgado de contraseña. 

El cuadro de diálogo relacionado proporciona dos métodos para restablecer una contraseña:

**Restablecer contraseña** - Select **requieren que el usuario restablecer contraseña** para permitir que al usuario if Self-recuperar el usuario se ha registrado para la autenticación con varios factores. Durante del usuario en el siguiente inicio de sesión, el usuario se requiera para resolver correctamente un desafío de autenticación con varios factores y, obliga a cambiar la contraseña. Esta opción no está disponible si la cuenta de usuario no está registrada autenticación con varios factores.

**Contraseña temporal** : seleccionar **generar una contraseña temporal** para inmediatamente invalida la contraseña existente y crear una nueva contraseña temporal para el usuario. Enviar la nueva contraseña temporal a una dirección de correo electrónico alternativa para el usuario o el administrador del usuario. Porque la contraseña es temporal, le pedirá al usuario cambiar la contraseña al iniciar sesión.


![Directiva] (./media/active-directory-identityprotection/1005.png "Directiva")


**Para abrir el cuadro de diálogo de configuración relacionados**:

1. En el módulo de **protección de la identidad de AD de Azure** , haga clic en **usuarios de marcan para el riesgo**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1006.png "Restablecimiento de contraseña manual")


2. En la lista de usuarios, seleccione un usuario con eventos de al menos un riesgo.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1007.png "Restablecimiento de contraseña manual")


2. En el módulo de usuario, haga clic en **Restablecer contraseña**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1008.png "Restablecimiento de contraseña manual")





## <a name="user-risk-security-policy"></a>Directiva de seguridad de usuario riesgo

Una directiva de seguridad de usuario riesgo es una directiva de acceso condicional que evalúa el nivel de riesgo a un usuario específico y aplica acciones de mitigación y corrección en función de las reglas y condiciones predefinidas.


![Directiva de usuario ridk] (./media/active-directory-identityprotection/1009.png "Directiva de usuario ridk")


Protección de identidad AD Azure le ayuda a administrar la mitigación y la corrección de los usuarios marcados para riesgo por lo que le permite:

- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1010.png "Directiva de usuario ridk")


- Establecer el usuario nivel umbral de riesgo (bajo, medio o alto) que desencadena la directiva: 

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1011.png "Directiva de usuario ridk")


- Establecer los controles que se deben cumplir cuando se desencadena la directiva:

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1012.png "Directiva de usuario ridk")


- Cambiar el estado de la directiva:

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/403.png "Registro de AMF")


- Revisar y evaluar el impacto de un cambio antes de activarlo:

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1013.png "Directiva de usuario ridk")


Elegir un umbral **alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios.
Sin embargo, excluye los usuarios **bajo** y **medio** marcados de riesgo de la directiva, que no se puede proteger identidades o dispositivos anteriormente sospecha o sabe que estar en peligro.

Cuando se establece la directiva,

- Excluir a los usuarios que son propensos a generar un lote de falsos positivos (programadores, analistas de seguridad)

- Excluir usuarios en configuraciones regionales donde no es práctico si se habilita la directiva (por ejemplo, sin acceso al servicio de asistencia)

- Uso de implantar un umbral **alto** durante la directiva inicial, o si debe reducir al mínimo los desafíos de los usuarios finales ver.

- Si su organización requiere mayor seguridad, utilice un umbral **bajo** . Seleccionar un umbral **bajo** presenta adicionales usuario inicio de sesión retos, pero una mayor seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones consiste en configurar una regla para un umbral **medio de** alcanzar un equilibrio entre usabilidad y seguridad.

Información general de la experiencia del usuario relacionados, consulte:

- [Flujo de recuperación de cuenta en peligro](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [La cuenta en peligro bloqueada flujo](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Para abrir el cuadro de diálogo de configuración relacionados**:

1. En el módulo de **protección de la identidad de AD de Azure** , en la sección **Configurar** , haga clic en **Directiva de riesgo del usuario**.

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1009.png "Directiva de usuario ridk")






## <a name="mitigating-user-risk-events"></a>Mitigación de los eventos de riesgo de usuario
Los administradores pueden establecer una directiva de seguridad de usuario riesgo para bloquear usuarios al iniciar sesión en función del nivel de riesgo. 

Bloqueo de un inicio de sesión:
 
- Evita la generación de nuevos eventos de riesgo de usuario para el usuario afectado

- Permite a los administradores solucionar los eventos de riesgo que afectan a la identidad del usuario y restaurarla a un estado seguro manualmente



## <a name="what-is-a-sign-in-risk-level"></a>¿Qué es un nivel de riesgo de inicio de sesión?

Un nivel de riesgo de inicio de sesión es una indicación (alta, Media o baja) de la probabilidad de que en un determinado inicios de sesión, otra persona está intentando autenticar con la identidad del usuario. El nivel de riesgo de inicio de sesión se evalúa en el momento de un signo de y considera los eventos de riesgo y los indicadores detectan en tiempo real para ese inicio de sesión en específico. 

## <a name="mitigating-sign-in-risk-events"></a>Mitigar el riesgo de inicio de sesión de eventos 
Un factor atenuante es una acción para limitar la capacidad de un atacante para explotar una identidad en peligro o el dispositivo sin tener que restaurar el dispositivo o identidad a un estado seguro. Un factor atenuante no resolver eventos anteriores de riesgo de inicio de sesión asociados con el dispositivo o identidad.

Puede utilizar acceso condicional en la protección de la identidad de AD de Azure para mitigar el riesgo de inicio de sesión de eventos automáticamente. Mediante estas directivas, considere el nivel de riesgo del usuario o el signo de bloquear los inicios de sesión arriesgados o requieren que el usuario para realizar la autenticación con varios factores. Estas acciones pueden evitar que un atacante aproveche una identidad robada para causar daños y pueden darle algún tiempo para proteger la identidad. 


## <a name="sign-in-risk-security-policy"></a>Directiva de seguridad de inicio de sesión de riesgo

Una directiva de inicio de sesión de riesgo es una directiva de acceso condicional que evalúa el riesgo a un inicio de sesión específico y aplica factores atenuantes basados en reglas y condiciones predefinidas.

![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1014.png "Inicio de sesión de la directiva de riesgo")


Protección de identidad AD Azure le ayuda a administrar la mitigación del riesgo de inicios de sesión, ya que permite:

- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1015.png "Inicio de sesión de la directiva de riesgo")


- Establecer el inicio de sesión de nivel umbral de riesgo (bajo, medio o alto) que desencadena la directiva: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1016.png "Inicio de sesión de la directiva de riesgo")


- Establecer los controles que se deben cumplir cuando se desencadena la directiva:  

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1017.png "Inicio de sesión de la directiva de riesgo")
    

- Cambiar el estado de la directiva:

    ![Registro de AMF] (./media/active-directory-identityprotection/403.png "Registro de AMF")

- Revisar y evaluar el impacto de un cambio antes de activarlo: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1018.png "Inicio de sesión de la directiva de riesgo")


### <a name="what-you-need-to-know"></a>Lo que necesita saber

Puede configurar una directiva de seguridad de inicio de sesión de riesgo para requerir la autenticación con varios factores:

![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1017.png "Inicio de sesión de la directiva de riesgo")

Sin embargo, por razones de seguridad, esta configuración sólo funciona para los usuarios que ya se han registrado para la autenticación con varios factores. Si se cumple la condición para requerir la autenticación con varios factores para un usuario que aún no esté registrado para la autenticación con varios factores, se bloquea el usuario. 

Como práctica recomendada, si desea exigir la autenticación con varios factores para inicios de sesión arriesgadas, debe:

1. Habilitar la [Directiva de registro de autenticación con varios factores](#multi-factor-authentication-registration-policy) para los usuarios afectados.
2. Requiere que los usuarios afectados al inicio de sesión en una sesión sin riesgo de explosión para realizar un registro AMF

Completar estos pasos garantiza que la autenticación multifactor es necesaria para un inicio de sesión en riesgo. 


### <a name="best-practices"></a>Mejores prácticas

 
Elegir un umbral **alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios.  
 
Sin embargo, excluye **bajo** y **medio de** inicios de sesión marcados de riesgo de la directiva, que no puede impedir que un atacante pueda aprovechar una identidad en peligro. 

Cuando se establece la directiva, 

- Excluir a los usuarios que no lo hacen y no pueden tener la autenticación con varios factores

- Excluir usuarios en configuraciones regionales donde no es práctico si se habilita la directiva (por ejemplo, sin acceso al servicio de asistencia)

- Excluir a los usuarios que son propensos a generar un lote de falsos positivos (programadores, analistas de seguridad)

- Uso de implantar un umbral **alto** durante la directiva inicial, o si debe reducir al mínimo los desafíos de los usuarios finales ver.

- Si su organización requiere mayor seguridad, utilice un umbral **bajo** . Seleccionar un umbral **bajo** presenta adicionales usuario inicio de sesión retos, pero una mayor seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones consiste en configurar una regla para un umbral **medio de** alcanzar un equilibrio entre usabilidad y seguridad.

 
La directiva de inicio de sesión de riesgo es:

- Se aplica a todos los inicios de sesión mediante la autenticación moderna y el tráfico del explorador.
- No se aplica a aplicaciones que usan protocolos de seguridad antiguos deshabilitando el extremo WS-Trust en el IDP federado, como ADFS.

La página de **Eventos de riesgo** en la consola de protección de identidad enumera todos los eventos:

- Esta directiva se aplica a
- Puede revisar la actividad y determinar si la acción fue adecuada o no 

Información general de la experiencia del usuario relacionados, consulte:

- [Recuperación de inicio de sesión arriesgado](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Arriesgado signo de bloqueado](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Registro de autenticación con varios factores durante un inicio de sesión de riesgo](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Para abrir el cuadro de diálogo de configuración relacionados**:

1. En el módulo de **protección de la identidad de AD de Azure** , en la sección **Configurar** , haga clic en **Inicio de sesión de la directiva de riesgo**.

    ![Directiva de usuario ridk] (./media/active-directory-identityprotection/1014.png "Directiva de usuario ridk")





## <a name="multi-factor-authentication-registration-policy"></a>Política de registro de autenticación con varios factores

La autenticación multifactor Azure es un método de comprobar quién eres que requiere el uso de algo más que un nombre de usuario y una contraseña. Proporciona una segunda capa de seguridad a inicios de sesión de usuario y las transacciones.  
Recomendamos que requieren la autenticación multifactor Azure para inicios de sesión de usuario porque él:

- Ofrece autenticación segura con una gama de opciones de comprobación fácil

- Desempeña un papel clave en la preparación de su organización a proteger y recuperar de compromisos de cuenta

![Directiva de usuario ridk] (./media/active-directory-identityprotection/1019.png "Directiva de usuario ridk")



Para obtener más detalles, vea [¿Qué es la autenticación con varios factores Azure?](../multi-factor-authentication/multi-factor-authentication.md)


Protección de identidad AD Azure le ayuda a administrar la puesta en servicio de registro de autenticación con varios factores mediante la configuración de una directiva que le permite: 



- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Directiva de AMF] (./media/active-directory-identityprotection/1020.png "Directiva de AMF")



- Establecer los controles que se deben cumplir cuando se desencadena la directiva::  

    ![Directiva de AMF] (./media/active-directory-identityprotection/1021.png "Directiva de AMF")


- Cambiar el estado de la directiva:

    ![Directiva de AMF] (./media/active-directory-identityprotection/403.png "Directiva de AMF")

- Ver el estado actual del registro: 

    ![Directiva de AMF] (./media/active-directory-identityprotection/1022.png "Directiva de AMF")


Información general de la experiencia del usuario relacionados, consulte:

- [Flujo de registro de autenticación con varios factores](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registro de autenticación con varios factores durante un inicio de sesión en riesgo](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Para abrir el cuadro de diálogo de configuración relacionados**:

1. En el módulo de **protección de la identidad de AD de Azure** , en la sección **Configurar** , haga clic en **registro de autenticación con varios factores**.

    ![Directiva de AMF] (./media/active-directory-identityprotection/1019.png "Directiva de AMF")





## <a name="next-steps"></a>Próximos pasos

 - [Channel 9: Azure AD y mostrar identidad: vista previa de protección de identidad](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipos de eventos de riesgo detectados por protección de identidad de Azure Active Directory](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilidades detectadas por protección de identidad de Azure Active Directory](active-directory-identityprotection-vulnerabilities.md)
 - [Notificaciones de Azure protección de identidad de Active Directory](active-directory-identityprotection-notifications.md)
 - [Guía de protección de identidad de Active Directory de Azure](active-directory-identityprotection-playbook.md)
 - [Glosario de Azure protección de identidad de Active Directory](active-directory-identityprotection-glossary.md)

 - [Experiencias de inicio de sesión con protección de la identidad de AD de Azure](active-directory-identityprotection-flows.md)

 - [Al habilitar la protección de la identidad de Azure de Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory Identity Protection - cómo desbloquear usuarios](active-directory-identityprotection-unblock-howto.md)

 - [Introducción a la protección de identidad de Azure Active Directory y Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


