Administración de identidad es tan importante en la nube pública como en locales. Para facilitar esto, Azure admite varias tecnologías de identidad nube diferente. Éstos incluyen:

- Puede ejecutar Windows Server Active Directory (normalmente llamado simplemente AD) en la nube usando máquinas virtuales creadas con máquinas virtuales de Azure. Este enfoque tiene sentido cuando utilizas Azure para ampliar su centro de datos local en la nube.


- Puede utilizar Active Directory de Azure para dar a su usuarios, single sign-on a aplicaciones de [Software como servicio (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) . Por ejemplo, Office 365 de Microsoft usa esta tecnología y aplicaciones que se ejecutan en Azure u otras plataformas de nube también pueden utilizar.


- Aplicaciones que se ejecutan en la nube o local pueden utilizar Control de acceso de directorio activo de Azure para registro permiten a los usuarios en el uso de identidades de Facebook, Google, Microsoft y otros proveedores de identidades.


Este artículo describe los tres de estas opciones.

## <a name="table-of-contents"></a>Tabla de contenido

- [Ejecutan Active Directory de Windows Server en máquinas virtuales](#adinvm)

- [Mediante Active Directory Azure](#ad)

- [Utilizar Control de acceso de Active Directory Azure](#ac)


## <a name="adinvm"></a>Ejecutan Active Directory de Windows Server en máquinas virtuales

Ejecuta Windows Server AD en Azure máquinas virtuales es muy similar a ejecución local. [La figura 1](#fig1) muestra un ejemplo típico de este aspecto.

![Active Directory Azure en Máquina Virtual](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figura 1: Puede ejecutar Active Directory de Windows Server en Azure máquinas virtuales conectadas al centro de datos de la organización local utilizando la red Virtual de Azure.

En el ejemplo mostrado aquí, AD del servidor de Windows se ejecuta en máquinas virtuales creadas con máquinas virtuales de Azure, la tecnología de la plataforma de IaaS. Estas máquinas virtuales y algunos otros se agrupan en una red virtual conectada a un centro de datos local utilizando la red Virtual de Azure. Talla de la red virtual a un grupo de máquinas virtuales de nube que interactúan con la red local a través de una conexión de red privada virtual (VPN). Haciendo esto permite estas máquinas virtuales Azure aspecto sólo otra subred al centro de datos local. Como se muestra en la figura, dos de las máquinas virtuales ejecutan controladores de dominio de Windows Server AD. Las demás máquinas virtuales en la red virtual podría ejecutar aplicaciones como SharePoint o utilizar de otra manera, como para desarrollo y pruebas. El centro de datos local también ejecuta dos controladores de dominio de Windows Server AD.

Hay varias opciones para conectar los controladores de dominio en la nube con las que se ejecutan en instalaciones:

- Hacer que todos ellos forman parte de un único dominio de Active Directory.

- Crear independiente AD dominios locales y en la nube que forman parte del mismo bosque.

- Crear bosques independientes de AD en la nube y locales y después conectar los bosques mediante las confianzas entre bosques o Windows servidor de federación de servicios de Active Directory (AD FS), que también se puede ejecutar en máquinas virtuales en Azure.

Se realiza independientemente de la opción, un administrador debe asegurarse de que las solicitudes de autenticación de usuarios locales vayan a controladores de dominio de la nube sólo cuando sea necesario, ya que el enlace a la nube es probable que sea más lento que las redes locales. Otro factor a considerar en la nube de conexión y los controladores de dominio local es el tráfico generado por la replicación. Controladores de dominio en la nube se encuentran normalmente en su propio sitio de AD, que permite a un administrador programar la frecuencia se realiza la replicación. Gastos de Azure para el tráfico que se envía fuera de un centro de datos Azure, aunque no de bytes enviados en que puede afectar a las opciones del Administrador de replicación. También merece la pena señalar que mientras que Azure proporcionan su propia compatibilidad de sistema de nombres de dominio (DNS), este servicio no tiene características requeridas por Active Directory (por ejemplo, soporte para registros DNS dinámicos y SRV). Por este motivo, quedando AD del servidor de Windows Azure requiere la configuración de sus propios servidores DNS en la nube.

Con Windows Server AD en Azure VM puede tener sentido en varias situaciones diferentes. Éstos son algunos ejemplos:

- Si está utilizando máquinas virtuales de Azure como una extensión de su propio centro de datos, puede ejecutar aplicaciones en la nube que necesitan controladores de dominio local para controlar las cosas como las solicitudes de autenticación de Windows integrada o consultas LDAP. SharePoint, por ejemplo, interactúa con frecuencia con Active Directory y, por lo que aunque es posible ejecutar una granja de SharePoint en Azure con un directorio local, configuración de controladores de dominio en la nube se mejora significativamente el rendimiento. (Es importante tener en cuenta que esto no es necesariamente necesario, sin embargo; multitud de aplicaciones se pueden ejecutar correctamente en la nube con sólo los controladores de dominio locales).

- Supongamos que una sucursal lejana carece de los recursos para ejecutar sus propios controladores de dominio. En la actualidad, los usuarios deben autenticarse en controladores de dominio en el otro lado del mundo - inicios de sesión son lentos. Ejecutando Active Directory en Azure en un centro de datos de Microsoft más cercano puede acelerar esto sin necesidad de más servidores de la sucursal.

- Una organización que utiliza Azure para recuperación ante desastres puede mantener un pequeño conjunto de máquinas virtuales activas en la nube, incluyendo un controlador de dominio. A continuación, podrá prepararse para expandir este sitio según sea necesario para asumir el control de errores en otro lugar.

También existen otras posibilidades. Por ejemplo, no sea necesario conectar Windows Server AD en la nube a un centro de datos local. Si desea ejecutar una granja de SharePoint que sirve un determinado conjunto de usuarios, por ejemplo, todos los cuales podrían iniciar exclusivamente con identidades basados en cloud, podría crear un bosque independiente en Azure. Cómo utilizar esta tecnología depende de ¿cuáles son sus objetivos. (Para información más detallada sobre cómo utilizar Windows Server AD con Azure, [Consulte aquí](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Mediante Active Directory Azure

A medida que las aplicaciones SaaS más común, provocan una pregunta obvia: ¿qué tipo de servicio de directorio deben utilizar estas aplicaciones en la nube? Respuesta de Microsoft a esa pregunta es Active Directory de Azure.

Existen dos opciones principales para utilizar este servicio de directorio en la nube:

- Individuos y organizaciones que utilizan aplicaciones de SaaS sólo pueden confiar en Azure Active Directory como su servicio de directorio único.

- Las organizaciones que ejecutan Active Directory de Windows Server pueden conectarse a su directorio local a Active Directory de Azure y luego ofrecer a su usuarios, single sign-on para las aplicaciones SaaS.


[La figura 2](#fig2) ilustra la primera de estas dos opciones, donde Active Directory de Azure es todo lo que se necesita.

![Active Directory Azure en Máquina Virtual](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figura 2: Azure Active Directory proporciona usuarios, single sign-on una organización a aplicaciones SaaS, incluyendo Office 365.

Como se muestra en la figura, AD Azure es un servicio multiempresa. Esto significa que puede soportar simultáneamente muchas organizaciones diferentes, almacenamiento de información de directorio acerca de los usuarios en cada uno de ellos. En este ejemplo, un usuario de la organización A está intentando obtener acceso a una aplicación SaaS. Esta aplicación podría formar parte de Office 365, como SharePoint Online, o puede ser otra cosa: aplicaciones ajenas a Microsoft también pueden utilizar esta tecnología. Como AD Azure admite el protocolo SAML 2.0, todo lo que se requiere de una aplicación es la capacidad para interactuar con este estándar de la industria. (De hecho, pueden ejecutar aplicaciones que utilizan AD Azure en cualquier centro de datos, no sólo a un centro de datos Azure.)

El proceso comienza cuando el usuario tiene acceso a una aplicación SaaS (paso 1). Para usar esta aplicación, el usuario debe presentar un token emitido por AD Azure.

Este token contiene información que identifica al usuario y está firmado digitalmente por AD Azure. Para obtener el token, el usuario se autentica a sí mismo a Azure AD proporcionando un nombre de usuario y la contraseña (paso 2). Anuncio de Azure, a continuación, devuelve el símbolo (token) que necesita (paso 3).

Este símbolo (token), a continuación, se envía a la aplicación de SaaS (paso 4), que valida la firma del token y utiliza su contenido (paso 5). Normalmente, la aplicación utilizará la información de identidad que contiene el token para decidir qué información se permite al usuario a acceso y quizás de otras maneras.

Si la aplicación necesita más información sobre el usuario que lo que se incluye en el token, puede solicitarlo directamente desde AD Azure usando la API de Azure AD gráfico (paso 6). En la versión inicial de Azure AD, el esquema de directorio es bastante simple: contiene sólo usuarios y grupos y relaciones entre ellos. Aplicaciones pueden utilizar esta información para aprender acerca de las conexiones entre los usuarios. Por ejemplo, suponga que una aplicación necesita saber quién es el Administrador de este usuario decidir si ha permitido el acceso a algún fragmento de datos. Puede obtener consultando Azure AD a través de la API de gráfico.

La API de gráfico se utiliza un protocolo RESTful normal, lo que es fácil de usar de la mayoría de los clientes, incluidos los dispositivos móviles. La API también admite las extensiones definidas por OData, agregar cosas tales como un lenguaje de consulta para permitir a los clientes acceso a los datos de maneras más útiles. (Para obtener más información sobre OData, consulte [Introducción de OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf).) Porque la API de gráficos puede utilizarse para obtener información acerca de las relaciones entre los usuarios, permite aplicaciones de comprender el gráfico social que está incrustado en el esquema de AD de Azure para una organización concreta (lo que se denomina la API Graph). Y para autenticarse a sí mismo a Azure AD para las solicitudes de Graph API, una aplicación utiliza OAuth 2.0.

Si una organización no utiliza Active Directory de Windows Server, no tiene servidores locales o dominios y depende únicamente de las aplicaciones de nube que utilizan AD Azure, utilizando sólo este directorio nube daría a usuarios, single sign-on la empresa a todos ellos. Aún mientras este escenario obtiene más comunes de cada día, continuará utilizando la mayoría de las organizaciones locales dominios creados con Active Directory de Windows Server. Anuncio de Azure tiene una útil función aquí también, como se muestra en [la figura 3](#fig3) .

![Azure Active Directory en el equipo Virtual](./media/identity/identity_03_AD.png)
<a id="fig3"></a>figura 3: una organización puede federar Active Directory Windows Server con Active Directory para dar a su usuarios, single sign-on para las aplicaciones SaaS de Azure.

En este escenario, un usuario en la organización B desea obtener acceso a una aplicación SaaS. Antes de hacerlo, los administradores del directorio de la organización deben establecer una relación de federación con Azure AD mediante AD FS, como se muestra en la figura. Los administradores también deben configurar sincronización de datos entre local Windows Server la organización AD y AD de Azure. Esto copia automáticamente información de usuario y grupo desde el directorio local en Azure AD. Observe qué permite: de hecho, la organización está ampliando su directorio local en la nube. Combina Windows Server AD y AD Azure de esta manera, la organización da un servicio de directorio que se puede administrar como una entidad única, mientras sigue teniendo una superficie de ambos locales y en la nube.

Para utilizar AD Azure, el usuario primero inicie sesión su dominio de Active Directory local como de costumbre (paso 1). Cuando intenta tener acceso a la aplicación de SaaS (paso 2), los resultados del proceso de federación en Azure AD ella emite un token para esta aplicación (paso 3). (Para obtener más información acerca de cómo funciona la federación, consulte [identidad basada en notificaciones para Windows: escenarios y tecnologías](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Como antes, este token contiene información que identifica al usuario, y está firmado digitalmente por AD Azure. Este símbolo (token), a continuación, se envía a la aplicación de SaaS (paso 4), que valida la firma del token y utiliza su contenido (paso 5). Y se encuentra en el escenario anterior, la aplicación puede utilizar la API de gráficos para obtener más información acerca de este usuario si de SaaS necesarias (paso 6).

En la actualidad, AD Azure no es un reemplazo completo de AD del servidor de Windows local. Como ya se mencionó, el directorio de la nube tiene un mucho más sencillo esquema y también falta cosas como la directiva de grupo, la capacidad de almacenar información sobre equipos y soporte para LDAP. (En realidad, una máquina de Windows no se puede configurar para permitir a los usuarios iniciar sesión en con nada más que Azure AD - no es un escenario admitido). En su lugar, los objetivos iniciales de AD Azure incluyen permitir que aplicaciones de acceso de usuarios empresariales en la nube sin mantener una conexión independiente y liberación local Administradores directorio de sincronizar manualmente su directorio local con todas las aplicaciones SaaS que utiliza su organización. Con el tiempo, sin embargo, esperan que este servicio de directorio de nube para enfrentar una amplia gama de escenarios.

## <a name="ac"></a>Utilizar Control de acceso de Active Directory Azure

Tecnologías de identidad basada en nube pueden utilizarse para resolver una variedad de problemas. Azure Active Directory puede dar usuarios, single sign-on una organización a varias aplicaciones de SaaS, por ejemplo. Pero también se pueden utilizar tecnologías de identidad en la nube de otras maneras.

Por ejemplo, suponga que desea una aplicación permitir a los usuarios iniciar sesión utilizando tokens emitidos por varios *proveedores de identidad (IDP)*. Muchos proveedores de identidades diferentes existen en la actualidad, incluyendo Facebook, Google, Microsoft y otros, y aplicaciones con frecuencia permiten a los usuarios iniciar sesión con alguna de estas identidades. ¿Por qué debe preocuparse una aplicación para mantener su propia lista de usuarios y contraseñas cuando en su lugar puede depender de las identidades que ya existen? Aceptación de identidades existentes simplifica la vida tanto para los usuarios, con uno menos el nombre de usuario y una contraseña para recordar como para las personas que crean la aplicación, que ya no necesite mantener sus propias listas de nombres de usuario y contraseñas.

Pero mientras que cada proveedor de identidad emite algún tipo de símbolo (token), esos tokens no son estándares, cada IdP cuenta con su propio formato. Además, la información de esos tokens también no es estándar. Una aplicación que pretenda acepte tokens emitidos por, digamos, Facebook, Google y Microsoft se enfrenta al desafío de escribir un código único para controlar cada uno de estos formatos diferentes.

Pero, ¿por qué hacerlo? ¿Por qué no crear en su lugar un intermediario que puede generar un único formato de símbolo (token) con una representación común de información de identidad? Este enfoque sería la vida más sencilla para los desarrolladores que crean aplicaciones, ya que se necesitan ahora para controlar sólo una tipo de símbolo (token). Control de acceso de directorio activo Azure hace exactamente esto, proporcionando a un intermediario en la nube para trabajar con diversos símbolos (tokens). [La figura 4](#fig4) muestra cómo funciona

![Azure Active Directory en el equipo Virtual](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>figura 4: Control de acceso de directorio activo de Azure facilita aplicaciones Aceptar los símbolos de identidad emitidos por proveedores de identidades diferentes.

El proceso comienza cuando un usuario intenta tener acceso a la aplicación desde un explorador. La aplicación redirige a un IdP de su elección (y que también confía la aplicación). Ella se autentica a sí misma en esta IdP como introduciendo un nombre de usuario y la contraseña (paso 1) y el IdP devuelve un símbolo (token) que contiene información sobre su (paso 2).

Como se muestra en la figura, Control de acceso admite una variedad de diferentes IdPs basados en cloud, incluyendo las cuentas creadas por Google, Yahoo, Facebook, Microsoft (anteriormente conocido como Windows Live ID) y cualquier proveedor de OpenID. También admite identidades creadas con Azure Active Directory y, a través de federación de AD FS, Active Directory de Windows Server. El objetivo es cubrir las identidades utilizadas con más frecuencia en la actualidad, si se está emitiendo de PDV en la nube o local.

Una vez que el explorador del usuario tiene un token IdP de su elegido IdP, envía este token para Control de acceso (paso 3). Control de acceso valida el token, asegurándose de que realmente ha sido emitido por esta IdP, a continuación, se crea un nuevo token de acuerdo con las reglas que se han definido para esta aplicación. Como Active Directory de Azure, Control de acceso es un servicio multiempresa, pero los inquilinos son aplicaciones en lugar de organizaciones de clientes. Cada aplicación puede obtener su propio espacio de nombres, como se muestra en la figura y puede definir varias reglas acerca de la autorización y mucho más.

Estas reglas permiten Administrador de cada aplicación definir cómo testigos de PDV distintos deben transformarse en un símbolo (token) de Control de acceso. Por ejemplo, si IdPs diferentes utiliza diferentes tipos para representar los nombres de usuario, las reglas de Control de acceso pueden transformar todo esto en un tipo común de nombre de usuario. Control de acceso, a continuación, envía este nuevo token al explorador (paso 4), que se envía a la aplicación (paso 5). Una vez que tiene el símbolo (token) de Control de acceso, la aplicación comprueba que este token realmente fue emitida por el Control de acceso y luego utiliza la información que contiene (paso 6).

Aunque este proceso puede parecer un poco complicado, que realmente hace la vida bastante más sencillo para el creador de la aplicación. En lugar de controlar diversos símbolos (tokens) que contiene información diferente, la aplicación puede aceptar identidades emitidas por varios proveedores de identidad mientras sigue recibiendo sólo un token único con información familiar. Además, en vez de requerir que cada aplicación que se va a configurar para que confíe IdPs diversos, estas relaciones de confianza en su lugar se mantienen por el Control de acceso: una aplicación sólo necesita confiar en él.

Merece la pena señalar que nada acerca de Control de acceso está ligado a Windows, bien podría ser utilizado por una aplicación de Linux que acepta sólo las identidades de Google y Facebook. Y aunque el Control de acceso es parte de la familia de Azure Active Directory, se puede considerar como un servicio totalmente distinto de lo que se describe en la sección anterior. Aunque ambas tecnologías funcionan con identidad, enfrentar problemas muy diferentes (aunque Microsoft ha dicho que espera integrar los dos en algún momento).

Trabajar con identidad es importante en casi todas las aplicaciones. El objetivo de Control de acceso es facilitar a los desarrolladores crear aplicaciones que aceptan las identidades de los proveedores de identidades diferentes. Al colocar este servicio en la nube, Microsoft ha hecho disponible para cualquier aplicación que se ejecuta en cualquier plataforma.

##<a name="about-the-author"></a>Acerca del autor

David Chappell es el director de Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) en San Francisco, California.
