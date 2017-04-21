<properties
    pageTitle="Haz iniciado AMF Azure en la nube | Microsoft Azure"
    description="Esta es la página de autenticación multifactor de Azure de Microsoft que describe cómo empezar a trabajar con AMF Azure en la nube."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introducción a la autenticación multifactor de Azure en la nube
Este artículo describe la forma aprender a usar la autenticación multifactor de Azure en la nube.

> [AZURE.NOTE]  La siguiente documentación proporciona información acerca de cómo permitir a los usuarios mediante el **Portal de Azure clásico**. Consulte si desea obtener más información acerca de cómo configurar la autenticación de varios factores de Azure para usuarios O365, [Configurar la autenticación de varios factor para Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![AMF en la nube](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Requisitos previos
Los siguientes requisitos previos son necesarios para poder habilitar la autenticación con varios factores Azure para los usuarios.


1. [Regístrese para una suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) - si ya no dispone de una suscripción de Azure, necesita registrarse para uno. Si acaba de empezar y utilizando AMF Azure puede utilizar una suscripción de prueba
2. [Crear un proveedor de autenticación multifactor](multi-factor-authentication-get-started-auth-provider.md) y asignarlo a su directorio o [asignar licencias a los usuarios](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licencias están disponibles para los usuarios que tienen AMF de Azure, Azure AD Premium o Enterprise Mobility Suite (EMS).  AMF se incluye en Azure AD Premium y el EMS. Si tiene las licencias necesarias, no es necesario crear un proveedor de autenticación.


## <a name="turn-on-two-step-verification-for-users"></a>Activar verificación de dos pasos para los usuarios
Para iniciar la necesidad de comprobación de inicio de dos en para un usuario, cambie el estado del usuario de deshabilitado a habilitado.  Para obtener más información sobre los Estados de usuario, vea [Estados de usuario de autenticación multifactor de Azure](multi-factor-authentication-get-started-user-states.md)

Utilice el procedimiento siguiente para habilitar AMF para los usuarios.

### <a name="to-turn-on-multi-factor-authentication"></a>Para activar la autenticación con varios factores

1.  Iniciar sesión como administrador en el [portal clásico Azure](https://manage.windowsazure.com) .
2.  A la izquierda, haga clic en **Active Directory**.
3.  En directorio, seleccione el directorio para el usuario que desea activar.
![Haga clic en directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **usuarios**.
5.  En la parte inferior de la página, haga clic en **Administrar la autenticación de varios factores**. Se abre una nueva pestaña de explorador.
![Haga clic en directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Buscar el usuario que desea habilitar para la verificación de dos pasos. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado.** 
 ![Permitir al usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque una **comprobación** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Habilitar**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Haga clic en **Habilitar autenticación multifactor**.
![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Observe que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**.
![Permitir a los usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)

Después de habilitar a los usuarios, se debe notificar por correo electrónico. La próxima vez que intenten iniciar sesión en, le pedirá que inscriba su cuenta para la verificación de dos pasos. Una vez que se empiecen a utilizar verificación en dos pasos, también necesitará configurar las contraseñas de la aplicación para evitar que se bloqueen aplicaciones del navegador no.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Usar PowerShell para automatizar la activación de verificación consta de dos pasos

Para cambiar el [estado de](multi-factor-authentication-whats-next.md) uso de [AD PowerShell de Azure](../powershell-install-configure.md), puede utilizar lo siguiente.  Se puede cambiar `$st.State` igual a uno de los siguientes estados:

- Habilitado
- Exige
- Deshabilitado  

> [AZURE.IMPORTANT]  Se desaconseja contra el traslado de usuarios directamente desde el estado de deshabilitar al estado forzada. Aplicaciones no basadas en explorador dejará de funcionar porque el usuario no ha entrado a través del registro AMF y obtener una [contraseña de la aplicación](multi-factor-authentication-whats-next.md#app-passwords). Si tiene aplicaciones no basadas en explorador y requerir contraseñas de app, recomendamos ir desde un estado de deshabilitado a habilitado. Esto permite a los usuarios registrarse y obtener sus contraseñas de la aplicación. Después, puede moverlos a forzada.

Uso de PowerShell sería una opción para permitir a los usuarios de bulto. Actualmente no hay ninguna característica de habilitar masiva en el portal de Azure y debe seleccionar cada usuario individualmente. Si tiene muchos usuarios, esto puede ser bastante una tarea. Creando un PowerShell script usando la siguiente, puede recorrer una lista de los usuarios y les permitan.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Éste es un ejemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obtener más información, vea [Estados de usuario de autenticación multifactor de Azure](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Próximos pasos
Ahora que ha configurado la autenticación multifactor de Azure en la nube, puede configurar y configurar la implementación. Para obtener más detalles, vea [Configurar la autenticación de varios factores de Azure](multi-factor-authentication-whats-next.md) .
