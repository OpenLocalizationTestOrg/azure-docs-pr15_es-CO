<properties
    pageTitle="Implementación de Git local al servicio de la aplicación de Azure"
    description="Aprenda a habilitar implementación Git local al servicio de la aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="local-git-deployment-to-azure-app-service"></a>Implementación de Git local al servicio de la aplicación de Azure

Este tutorial muestra cómo implementar la aplicación en [Azure App servicio] desde un repositorio Git en el equipo local. Servicio de la aplicación es compatible con este enfoque con la opción de implementación **Local Git** en el [Portal de Azure].  
Muchos de los comandos Git descritos en este artículo se realizan automáticamente al crear una aplicación de servicio de la aplicación mediante la [interfaz de línea de comandos de Azure] como se describe [aquí](app-service-web-get-started.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará:

- GIT. Puede descargar la instalación binaria [aquí](http://www.git-scm.com/downloads).  
- Conocimiento básico de la Git.
- Una cuenta de Microsoft Azure. Si no tienes una cuenta, puede [registrarse para una prueba gratuita](https://azure.microsoft.com/pricing/free-trial) o [activar las ventajas del suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de contratar una cuenta de Azure, vaya a [Tratar de servicio de la aplicación](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una starter de corta duración de la aplicación de servicio de la aplicación. No hay tarjetas de crédito requeridos; No hay compromisos.  

## <a name="Step1"></a>Paso 1: Crear un repositorio local

Realizar las tareas siguientes para crear un nuevo repositorio de Git.

1. Iniciar una herramienta de línea de comandos, como **GitBash** (Windows) o el Shell de **Bash** (Unix). En los sistemas OS X puede acceder la línea de comandos mediante la aplicación de **Terminal** .

2. Desplácese hasta el directorio donde debería hallarse el contenido a distribuir.

3. Utilice el comando siguiente para inicializar un nuevo repositorio Git:

        git init

## <a name="Step2"></a>Paso 2: Confirmar su contenido

Servicio de la aplicación es compatible con aplicaciones creadas en una variedad de lenguajes de programación. 

1. Si el repositorio ya incluye contenido omitir esto seleccione y mueva a punto 2 siguiente. Si el repositorio no ya incluye contenido simplemente rellenar con un archivo .html estáticos como sigue: 

    - Con un editor de texto, cree un nuevo archivo denominado **index.html** en la raíz del repositorio Git
    - Agregue el texto siguiente como el contenido para el index.html archivo y guárdelo: *Hola Git!*
        
2. Desde la línea de comandos, compruebe que se encuentra en la raíz del repositorio Git. A continuación, utilice el siguiente comando para agregar archivos al repositorio:

        git add -A 

4. A continuación, confirme los cambios en el repositorio mediante el comando siguiente:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Paso 3: Habilitar el repositorio de la aplicación servicio de aplicación

Realice los pasos siguientes para habilitar un repositorio Git para la aplicación de servicio de la aplicación.

1. Inicie sesión en el [Portal de Azure].

2. En el módulo de la aplicación de servicio de la aplicación, haga clic en **Configuración > origen de la implementación**. Haga clic en **Elegir fuente**, a continuación, haga clic en **Repositorio Git Local**y, a continuación, haga clic en **Aceptar**.  

    ![Repositorio Git local](./media/app-service-deploy-local-git/local_git_selection.png)

3. Si es la primera vez configurar un repositorio en Azure, debe crear las credenciales de inicio de sesión para ella. Los utilizará para iniciar sesión en los cambios de repositorio y empuje Azure desde el repositorio Git local. Desde el módulo de la aplicación, haga clic en **Configuración > credenciales de implementación**, a continuación, configurar su implementación nombre de usuario y contraseña. Cuando haya terminado, haga clic en **Guardar**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Paso 4: Implementar el proyecto

Utilice los siguientes pasos para publicar su aplicación al servicio de la aplicación mediante Git Local.

1. En el módulo de la aplicación en el Portal de Azure, haga clic en **Opciones > Propiedades** para la **Dirección URL de la Git**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Dirección URL de la GIT** es la referencia remota para desplegar en el repositorio local. En los siguientes pasos, utilizará esta dirección URL.

2. Con la línea de comandos, compruebe que se encuentra en la raíz del repositorio Git local.

3. Utilice `git remote` agregar la referencia remota enumerados en **Git URL** del paso 1. El comando será similar al siguiente:

        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] El comando **remoto** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, crea una referencia denominada 'azure' para el repositorio de sus aplicaciones web.

4. Insertar el contenido en el servicio de la aplicación mediante el nuevo **azure** remoto que acaba de crear.

        git push azure master

    Se le pedirá la contraseña que creó anteriormente al restablecer las credenciales de la implementación en el Portal de Azure. Escriba la contraseña (tenga en cuenta que Gitbash muestre los asteriscos a la consola mientras escribe la contraseña). 
       
5. Volver a la aplicación en el Portal de Azure. Una entrada de registro de su inserción más reciente debe mostrarse en la hoja de **implementaciones** . 

    ![](./media/app-service-deploy-local-git/deployment_history.png)

6. Haga clic en el botón **Examinar** en la parte superior del módulo de la aplicación para comprobar que el contenido se ha implementado. 
    
## <a name="Step5"></a>Solución de problemas

Los siguientes son errores o problemas que se producen normalmente al utilizar Git para publicar en una aplicación de servicio de la aplicación en Azure:

****

**Síntoma**: incapaz de acceso '[siteURL]': no se pudo conectar a [scmAddress]

**Causa**: este error puede producirse si la aplicación no está funcionando.

**Resolución**: inicio de la aplicación en el Portal de Azure. Implementación de GIT no funcionará a menos que la aplicación se está ejecutando. 


****

**Síntoma**: no se pudo resolver el host 'hostname'

**Causa**: este error puede producirse si la información de dirección especificada al crear el 'azure' remoto era incorrecta.

**Resolución**: uso del `git remote -v` comando para enumerar todos los controles remotos, junto con la dirección URL asociada. Compruebe que la dirección URL remota 'azure' es correcta. Si es necesario, quite y vuelva a crear este control remoto con la dirección URL correcta.

****

**Síntoma**: sin referencias en común y ninguno especificado; no hace nada. Quizás debería especificar una rama como 'master'.

**Causa**: este error puede producirse si no se especifica una rama cuando realiza un git push operación y no se ha establecido el valor de push.default utilizado por Git.

**Resolución**: realizar la operación de inserción de nuevo, especificando la bifurcación principal. Por ejemplo:

    git push azure master

****

**Síntoma**: src refspec [branchname] no coincide con ninguna.

**Causa**: este error puede producirse si intenta introducir en una rama que no sea el maestro de la 'azure' remoto.

**Resolución**: realizar la operación de inserción de nuevo, especificando la bifurcación principal. Por ejemplo:

    git push azure master

****

**Síntoma**: Error - cambios confirmados a repositorio remoto pero su aplicación web no se actualiza.

**Causa**: este error puede producirse si está implementando una aplicación de Node.js que contiene un archivo package.json que especifica los módulos necesarios adicionales.

**Resolución**: mensajes adicionales que contengan 'npm ERR'! debe ser registrado antes de este error y puede proporcionar contexto adicional sobre el error. Los siguientes son conocidos causas de este error y el correspondiente 'npm ERR!' Mensaje:

* **Archivo con formato incorrecto package.json**: npm ERR! No se pudieron leer las dependencias.

* **Un módulo nativo que no tiene una distribución binaria para Windows**:

    * ¡NPM ERR! \`cmd "/ c" "reconstrucción nodo gyp"\` falló con 1

        OR

    * ¡NPM ERR! [modulename@version]preinstalar: \`hacer || gmake\`


## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de GIT](http://git-scm.com/documentation)
* [Documentación del proyecto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Implementación continua al servicio de la aplicación de Azure](app-service-continuous-deployment.md)
* [Cómo usar PowerShell para Azure](../powershell-install-configure.md)
* [Cómo utilizar la interfaz de línea de comandos de Azure](../xplat-cli-install.md)

[Servicio de aplicación de Azure]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Portal de Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Interfaz de línea de comandos de Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
