Las organizaciones utilizan más aplicaciones de [Software como servicio (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) de productividad debido a herramientas y tecnología de nube son cada vez más disponibles. A medida que crece el número de aplicaciones de SaaS, se convierte en un reto para los administradores administrar las cuentas y los derechos de acceso y para los usuarios a recordar las contraseñas diferentes. Administrar estas aplicaciones individualmente crea un trabajo extra y es menos seguro.


- Empleados que tienen que realizar el seguimiento de muchas contraseñas tienden a utilizar métodos menos seguros para recordarlas, bien escribirlas utilizando las mismas contraseñas en muchas cuentas.

- Cuando un nuevo empleado llega o sale de uno, todas sus cuentas deben proporcionarse o anule aprovisionados individualmente.

- Además, los empleados pueden empezar a utilizar aplicaciones de SaaS para su trabajo sin pasar por TI, lo que significa que están creando sus propias cuentas en los sistemas que los administradores de TI no han aprobado y no supervisión.  

Una solución para todos estos desafíos es el inicio de sesión único (SSO). Es la forma más sencilla de administrar múltiples aplicaciones y proporcionar a los usuarios una experiencia coherente de inicio de sesión. Azure Active Directory (AD Azure) proporciona una solución sólida de SSO y tiene muchas aplicaciones previamente integradas disponibles, con tutoriales para administradores para configurar una nueva aplicación y rápidamente iniciar el aprovisionamiento de los usuarios.


## <a name="how-does-azure-active-directory-integrate-apps"></a>¿Cómo se integra aplicaciones de Azure Active Directory?  

AD Azure permite integrar sus aplicaciones y provisioning de cuentas. Esto puede hacerse a través de cualquiera de estos dos enfoques.

- Si la aplicación es previamente integrada en la Galería de la aplicación, puede ir a través de ese portal para instalar aplicaciones y configurar la configuración para permitir SSO. Para cualquier aplicación de galería, puede comenzar por siga las sencillas instrucciones paso a paso presentadas en la Galería de la aplicación y en el portal de Azure para habilitar el inicio de sesión único.

- Si la aplicación no está en la galería, puede configurar la mayoría de las aplicaciones de Azure AD como una aplicación personalizada. Esto requiere un poco más experiencia técnica para configurar. Puede agregar cualquier aplicación que admita SAML 2.0 como aplicación federada o cualquier aplicación que tenga una página de inicio de sesión del basado en HTML como aplicación SSO contraseña.

En el caso donde los usuarios han creado sus propias cuentas para las aplicaciones SaaS que no están administradas por TI, la herramienta de [Descubrimiento de aplicaciones de nube](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) proporciona una solución. Esta herramienta supervisa el tráfico de la web para identificar las aplicaciones que se utilizan en toda la organización y el número de personas que utilizan cada uno de ellos. TI puede utilizar esta información para saber qué aplicaciones los usuarios prefieren y decidan cuál desea integrar en Azure AD para SSO.  

Al integrar una aplicación en Azure AD, puede asignar identidades de los usuarios de aplicaciones establecidas a sus respectivas identidades de AD de Azure.  
