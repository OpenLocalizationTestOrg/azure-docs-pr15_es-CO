<properties
    pageTitle="Cómo instalar y configurar PowerShell de Azure"
    description="Aprenda a instalar y configurar PowerShell de Azure."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Cómo instalar y configurar PowerShell de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI de Azure</a></div>

##<a name="what-is-azure-powershell"></a>¿Qué es PowerShell Azure?
PowerShell Azure es un conjunto de módulos que proporcionan los cmdlets para administrar Azure con Windows PowerShell. Puede usar los cmdlets de para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma Windows Azure. En la mayoría de los casos, los cmdlets puede utilizarse para las mismas tareas que el Portal de Azure, como la creación y configuración de servicios de nube, máquinas virtuales, redes virtuales y aplicaciones web.

## <a name="how-versioning-works"></a>Cómo funciona el control de versiones

PowerShell Azure usa versiones semánticos, que significa que si versión A > versión B y, a continuación, versión A tiene las API más actualizadas. Además, significa que en la media un salto de versión principal los cambios realizados en uno o varios cmdlets.  Así, por ejemplo, versión 1.7.0 es una revisión para enfrentar un cambio importante en las versiones 1.x de PowerShell de Azure.

Para obtener más información acerca de las prácticas de versiones semántico en Azure PowerShell, vea la especificación semántica de control de versiones en: http://semver.org
 
Para obtener las API más recientes, debe utilizar la versión 2.x. Pero si tiene scripts escritos con la versión 1.x y no desea que puedan absorber los cambios importantes en la versión 2.x descritos en las [Notas de la versión](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md)2.x, a continuación, debe instalar 1.7.0.

Una discrepancia de versiones puede producirse si está instalada la versión más reciente del módulo de perfil, y posteriormente se carga una versión anterior de un módulo que depende de él. La forma más sencilla de resolver este problema es instalar desde el archivo .msi más reciente. El archivo .msi limpia automáticamente las versiones anteriores de los módulos.
 
###<a name="installing-module-versions-side-by-side"></a>Instalar versiones de módulo side-by-side

Versión 2.1.0 (versión 1.2.6 para AzureStack) son las primeras versiones de módulo diseñadas para ser instalado y utilizado side-by-side. Como PowerShell Azure utiliza módulos binarios, debe abrir una nueva ventana de PowerShell y usar **Import-Module** para importar una versión específica de los cmdlets de AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versiones antes 2.1.0 (excepto 1.2.6) no funcionan bien en paralelo con otras versiones de módulo de PowerShell de Azure. Al cargar una versión anterior de los módulos de Azure PowerShell utilizando un comando como la anterior, se cargarán versiones incompatibles del módulo **AzureRM.Profile** , lo que los cmdlets que le pide que inicie sesión cada vez que se ejecuta un cmdlet, incluso después de que haya iniciado sesión.

Fuente de la forma más sencilla de resolver que esto consiste en instalar el más reciente PowerShell Azure desde el WebPI o .msi, esto quita las versiones anteriores de los módulos instalados de la galería. 

Tenga en cuenta que los módulos de Azure y AzureRM tienen dependencias en común, por lo que si usa ambos módulos, al actualizar uno, debe actualizarlos. Las versiones anteriores del módulo Azure tienen el mismo problema con el módulo de side-by-side cargar que las versiones anteriores del módulo AzureRM.

<a id="Install"></a>
## <a name="step-1-install"></a>Paso 1: instalar

Siguientes son los dos métodos que se puede instalar PowerShell de Azure. Puede instalarlo desde la Galería de PowerShell o el WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalar PowerShell Azure desde la Galería de PowerShell

La forma preferida es utilizar la Galería de PowerShell. Se requiere el módulo de PowerShellGet utilizar la Galería de PowerShell. Esto está disponible aquí: [PowerShellGallery.com](https://www.powershellgallery.com/)

Instale PowerShell Azure 1.3.0 o superior de la Galería de PowerShell mediante un mensaje de Windows PowerShell o entorno de Scripting integrado (ISE) de PowerShell elevado mediante los siguientes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>Más información sobre estos comandos

- **AzureRM módulo de instalación** instala un módulo de paquete acumulativo de actualizaciones para los cmdlets del Administrador de recursos de Azure. Depende del módulo AzureRM 
- un intervalo de versión concreta para cada módulo del Administrador de recursos de Azure. El intervalo de versión incluye garantiza que, al instalar los módulos de AzureRM con la misma versión principal, se puede incluir ningún cambio del módulo de separación. Cuando se instala el módulo AzureRM, cualquier módulo de Azure el Administrador de recursos que no se ha instalado anteriormente se descargará y se instala desde la Galería de PowerShell. Para obtener más información sobre el control de versiones semántico utilizado por módulos de PowerShell de Azure, consulte [semver.org](http://semver.org). 
- **Módulo de instalación Azure** instala el módulo de Azure. Este módulo es el módulo de administración de servicios de Azure PowerShell 0.9.x. No debe tener grandes cambios y ser intercambiables para la versión anterior del módulo de Azure.

###<a name="installing-azure-powershell-from-webpi"></a>Instalar PowerShell Azure desde WebPI

Instalar Azure PowerShell 1.0 y mayor de WebPI es el mismo que para 0.9.x. Descargar [PowerShell de Azure](http://aka.ms/webpi-azps) y comenzar la instalación. Si tienes Azure PowerShell 0.9.x instalado, versión 0.9.x se deshabilitará durante la actualización. Si ha instalado módulos de Azure PowerShell desde galería de PowerShell, el programa de instalación quita automáticamente los módulos antes de la instalación para garantizar un entorno coherente de PowerShell de Azure.

> [AZURE.NOTE] Si anteriormente ha instalado módulos de Azure desde la Galería de PowerShell, el programa de instalación quitará automáticamente. Esto evita confusión acerca de qué versiones del módulo ha instalado y dónde se encuentran. Módulos de galería de PowerShell normalmente se instalación en **%ProgramFiles%\WindowsPowerShell\Modules**. Por el contrario, el instalador WebPI instala los módulos de Azure en * *% ProgramFiles (x86) %\Microsoft SDKs\Azure\PowerShell\**. Si se produce un error durante la instalación, puede quitar manualmente la Azure* carpetas en la carpeta **%ProgramFiles%\WindowsPowerShell\Modules** y pruebe de nuevo la instalación.

Una vez finalizada la instalación, la ```$env:PSModulePath``` opción debe incluir los directorios que contienen los cmdlets de PowerShell de Azure.

> [AZURE.NOTE] Hay un problema conocido con PowerShell **$env: PSModulePath** que puede producirse al instalar desde WebPI. Si el equipo requiere un reinicio debido a otras instalaciones o actualizaciones del sistema, puede causar las actualizaciones **$env: PSModulePath** para no incluir la ruta de acceso donde está instalado PowerShell de Azure. Si esto ocurre, verá un mensaje 'no se reconoce el cmdlet' al intentar usar los cmdlets de PowerShell de Azure después de la instalación o actualización. Si esto ocurre, reinicie el equipo debe solucionarse el problema.

Si recibe un mensaje como el siguiente al intentar cargar o ejecutar cmdlets:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Esto puede corregirse reiniciando el equipo o importando los cmdlets de C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ como el siguiente (donde XXXX es la versión de PowerShell instalado:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Paso 2: inicio
Puede ejecutar los cmdlets de la consola estándar de Windows PowerShell o de PowerShell Integrated Scripting Environment (ISE).
El método que utilice para abrir cualquier consola depende de la versión de Windows que está ejecutando:

- En un equipo con al menos 8 de Windows o Windows Server 2012, puede utilizar la búsqueda integrada. Desde la pantalla de **Inicio** , comience a escribir la energía. Devuelve una lista con ámbito de aplicaciones que incluye Windows PowerShell. Para abrir la consola, haga clic en cualquier aplicación. (Para fijar la pantalla de **Inicio** de la aplicación, haga clic en el icono.)

- En un equipo que ejecuta una versión anterior a Windows 8 o Windows Server 2012, utilice el **menú Inicio**. Desde el menú **Inicio** , seleccione **Todos los programas**, **Accesorios**, haga clic en la carpeta **Windows PowerShell** y, a continuación, haga clic en **Windows PowerShell**.

También puede ejecutar el **Windows PowerShell ISE** para usar elementos de menú y métodos abreviados de teclado para realizar muchas de las tareas que debe realizar en la consola de Windows PowerShell. Para utilizar el ISE, en la consola de Windows PowerShell, Cmd.exe, o en el cuadro **Ejecutar** , escriba **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Comandos para ayudarle a comenzar

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Paso 3: conectar
Los cmdlets necesita su suscripción para que puedan gestionar sus servicios. Puede adquirir una suscripción de Azure si aún no tiene uno. Para obtener instrucciones, vea [cómo comprar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Tipo de **Inicio de sesión AzureRmAccount**

2. Escriba la dirección de correo electrónico y contraseña asociada a su cuenta. Azure autentica y guarda la información de credenciales y, a continuación, cierra la ventana.

--O BIEN--

Iniciar sesión en el trabajo o la escuela de cuenta:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Si tiene más de un arrendatario asociada a su cuenta organizacional, especifique el parámetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Este registro no interactiva en método sólo funciona con una cuenta de trabajo o escuela. Una cuenta de trabajo o escuela es un usuario que es administrado por su trabajo o la escuela y definido en la instancia de Active Directory de Azure para su trabajo o la escuela. Si actualmente no tiene una cuenta de trabajo o escuela y está utilizando una cuenta de Microsoft para acceder a su suscripción de Azure, puede crear fácilmente uno siguiendo estos pasos.

> 1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com)y haga clic en **Active Directory**.

> 2. Si no existe ningún directorio, seleccione **crear su directorio** y proporcione la información solicitada.

> 3. Seleccione el directorio y agregar un nuevo usuario. Este nuevo usuario puede iniciar sesión con una cuenta de trabajo o escuela. Durante la creación del usuario, se suministrarán con una dirección de correo para el usuario y una contraseña temporal. Guardar esta información, tal como se utiliza en el paso 5 a continuación.

> 4. Desde el portal de Azure clásico, seleccione **configuración** y a continuación, seleccione **los administradores**. Seleccione **Agregar**y agregar el nuevo usuario como administrador del conjunto. Esto permite que la cuenta de trabajo o escuela administrar la suscripción de Azure.

> 5. Finalmente, cerrar sesión en el portal Azure clásico y volver a iniciarla utilizando el trabajo o la escuela cuenta. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

> Para obtener más información sobre suscribirse a Azure de Microsoft con una cuenta de trabajo o escuela, vea [suscribirse a Azure de Microsoft como una organización](./active-directory/sign-up-organization.md).

> Para obtener más información acerca de la administración de autenticación y suscripción en Azure, consulte [Administrar cuentas, suscripciones y funciones administrativas](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Ver detalles de cuenta y su suscripción

Puede tener varias suscripciones disponibles para el uso de PowerShell de Azure y cuentas. Puede agregar varias cuentas ejecutando **AzureRmAccount de agregar** más de una vez.

Para mostrar las cuentas disponibles de Azure, escriba **Get-AzureAccount**.

Para mostrar las suscripciones de Azure, escriba **Get-AzureRmSubscription**.

##<a id="Help"></a>Obtención de ayuda##

Estos recursos ofrecen ayuda para los cmdlets específicos:


-   Desde la consola, puede utilizar el sistema de ayuda integrada. El cmdlet **Get-Help** proporciona acceso a este sistema. 

- Para obtener ayuda de la Comunidad, pruebe estos foros populares:

 - [Foro de Azure en MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Aprende más


Consulte los siguientes recursos para obtener más información acerca de los cmdlets:

Para obtener instrucciones básicas sobre el uso de Windows PowerShell, consulte [Uso de Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para obtener información de referencia acerca de los cmdlets, vea [Referencia de Cmdlet de Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para que las secuencias de comandos de ejemplo e instrucciones para ayudarle a aprender a utilizar secuencias de comandos para administrar Azure, consulte el [Centro de secuencia de comandos](http://go.microsoft.com/fwlink/p/?LinkId=321940).

