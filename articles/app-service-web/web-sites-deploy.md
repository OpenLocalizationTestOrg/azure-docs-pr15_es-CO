<properties
    pageTitle="Implementar su aplicación en Azure de la aplicación de servicio"
    description="Aprenda a implementar su aplicación al servicio de la aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Implementar su aplicación en Azure de la aplicación de servicio

Este artículo le ayudará a determinar la mejor opción para implementar los archivos de la aplicación web, back-end de la aplicación móvil o API de la aplicación al [Servicio de la aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)y le guía a los recursos apropiados con instrucciones específicas de la opción que prefiera.

## <a name="overview"></a>Resumen de implementación de la aplicación servicio Azure

Servicio de aplicación de Azure mantiene el marco de aplicación para usted (ASP.NET, PHP, Node.js, etcetera). Algunos entornos están habilitados de forma predeterminada, mientras que otros, como Java y Python, que tenga una configuración simple de la marca de verificación para activarla. Además, puede personalizar su marco de aplicación, como la versión PHP o los bits de su tiempo de ejecución. Para obtener más información, vea [Configurar la aplicación de servicio de la aplicación de Azure](web-sites-configure.md).

Puesto que no tiene que preocuparse por el entorno de servidor o aplicación web, implementar su aplicación al servicio de la aplicación es una cuestión de implementación de su código, archivos binarios, archivos de contenido y su estructura de directorios respectivos, en el [directorio **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o la **/sitio/wwwroot/App_Data/trabajos/** directorio WebJobs). Servicio de la aplicación es compatible con las siguientes opciones de implementación: 

- [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): usar el FTP o FTPS favoritos habilitado para mover los archivos a Azure, de [FileZilla](https://filezilla-project.org) a completa IDEs como [NetBeans](https://netbeans.org). Esto es estrictamente un proceso de carga de archivos. No proporcionan servicios adicionales son el servicio de la aplicación, como control de versiones, administración de la estructura de archivos, etcetera. 

- [Kudu (Git o Mercurial o OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): utilice el [motor de implementación](https://github.com/projectkudu/kudu/wiki) de servicio de la aplicación. Insertar el código en Kudu directamente desde cualquier repositorio. Kudu también proporciona servicios añadidos siempre que el código se inserta en él, incluido el control de versiones, restauración de paquete, MSBuild y [Enlaces web](https://github.com/projectkudu/kudu/wiki/Web-hooks) para implementación continua y otras tareas de automatización. El motor de implementación de Kudu es compatible con 3 tipos diferentes de orígenes de implementación:   
    * Sincronización de contenido de OneDrive y Dropbox   
    * Implementación de continua basada en el repositorio con sincronización automática de GitHub, Bitbucket y Visual Studio Team Services  
    * Implementación basada en el repositorio con la sincronización manual Git local  

- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implementar código para servicio de la aplicación directamente desde Microsoft favoritos herramientas como Visual Studio utilizan el mismo conjunto de herramientas que automatiza la implementación de servidores IIS. Esta herramienta admite implementación sólo diferencias, creación de base de datos, las transformaciones de las cadenas de conexión, etcetera. Web Deploy difiere de Kudu en que se generan archivos binarios de aplicación antes de que se implementen en Azure. Similar a FTP, no proporcionan servicios adicionales están al servicio de la aplicación.

Herramientas de desarrollo de web populares admiten uno o varios de estos procesos de implementación. Mientras la herramienta que elija determina los procesos de implementación que puede aprovechar, la funcionalidad de DevOps real a su disposición depende de la combinación del proceso de implementación y las herramientas específicas que elija. Por ejemplo, si realiza Web Deploy desde [Visual Studio con el SDK de Azure](#vspros), incluso si no obtienes automatización del Kudu, obtendrá restauración del paquete y la automatización de MSBuild en Visual Studio. 

>[AZURE.NOTE] Estos procesos de implementación no realmente [aprovisionar los recursos Azure](../resource-group-template-deploy-portal.md) que necesite su aplicación. Sin embargo, la mayoría de los artículos de procedimientos vinculados mostrarle cómo preparar la inclusión de la aplicación e implementar su código en él end-to-end. También encontrará opciones adicionales para el provisioning de recursos de Azure en la sección [implementación de la automatización mediante herramientas de línea de comandos](#automate) .
     
## <a name="ftp"></a>Implementar a través de FTP copiando manualmente los archivos en Azure
Si se utilizan para copiar manualmente el contenido web en un servidor web, puede utilizar una utilidad [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar los archivos, como el Explorador de Windows o [FileZilla](https://filezilla-project.org/).

Los profesionales de copiar manualmente los archivos son:

- Familiaridad y complejidad mínima para tooling de FTP. 
- Saber exactamente dónde se van a los archivos.
- Mayor seguridad con FTPS.

Los inconvenientes de copiar manualmente los archivos son:

- Necesidad de saber cómo implementar los archivos en los directorios correctos en el servicio de la aplicación. 
- Sin control de versiones para la reversión cuando se producen errores.
- Ningún historial de implementación integrados para solucionar problemas de implementación.
- Posibles implementación largo tiempo ya que muchas herramientas FTP no proporcionan sólo diferencias copiando y simplemente copie todos los archivos.  

### <a name="howtoftp"></a>Cómo implementar copiando manualmente los archivos en Azure
Copiar archivos a Azure implica unos sencillos pasos:

1. Suponiendo que ya se han establecido las credenciales de la implementación, obtener la información de conexión FTP desde **configuración** > **Propiedades**y, a continuación, copiar los valores de **Usuario FTP e implementación**, **Nombre de Host FTP**y **FTPS nombre de Host**. Copie el valor de usuario **FTP e implementación usuario** tal como aparece en el Portal de Azure, incluido el nombre de la aplicación con el fin de proporcionar el contexto adecuado para el servidor FTP.
2. Desde el cliente FTP, utilice la información de conexión recopilada para conectarse a la aplicación.
3. Copie los archivos y su estructura de directorios respectivos en el [directorio **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o la **/sitio/wwwroot/App_Data/trabajos/** directorio WebJobs).
4. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

Para obtener más información, consulte los siguientes recursos:

* [Crear una aplicación web de MySQL-PHP e implementar mediante FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Implementar la sincronización con una carpeta de nube
Una buena alternativa a [copiar manualmente los archivos](#ftp) está sincronizando los archivos y carpetas al servicio de la aplicación de un servicio de almacenamiento de nube como OneDrive y la lista desplegable. Sincronización con una carpeta de nube utiliza el proceso de Kudu para la implementación (vea la [información general de los procesos de implementación](#overview)).

Los profesionales de la sincronización con una carpeta de nube son:

- Sencillez de implementación. Servicios como OneDrive y Dropbox proporcionan a los clientes de escritorio de sincronización, por lo que el directorio de trabajo local también es el directorio de implementación.
- Implementación de un solo clic.
- Toda la funcionalidad en el motor de implementación de Kudu está disponible (por ejemplo, restauración de paquete, automatización).

Los inconvenientes de la sincronización con una carpeta de nube son:

- Sin control de versiones para la reversión cuando se producen errores.
- Ninguna implementación automatizada, la sincronización manual es necesario.

### <a name="howtodropbox"></a>Cómo implementar la sincronización con una carpeta de nube
En el [Portal de Azure](https://portal.azure.com), designe una carpeta para la sincronización de contenido en su almacenamiento en la nube OneDrive o Dropbox, trabajar con el código de la aplicación y el contenido de esa carpeta y sincronizar con el servicio de aplicación con el clic de un botón.

* [Sincronizar el contenido de una carpeta de nube al servicio de la aplicación de Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Implementar de forma continua desde un servicio de control de código fuente basado en la nube
Si el equipo de desarrollo utiliza un servicio de management (SCM) de código fuente basado en la nube como [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com)o [BitBucket](https://bitbucket.org/), puede configurar la aplicación de servicio para integrar con el repositorio e implementar continuamente. 

Los profesionales de la implementación de un servicio de control de código fuente basado en la nube son:

- Control de versiones para habilitar la reversión.
- Capacidad de implementación continua para Git (y Mercurial, en su caso) de configurar los repositorios. 
- La implementación específica de la sucursal, puede implementar diferentes bifurcaciones a diferentes [slots](web-sites-staged-publishing.md).
- Toda la funcionalidad en el motor de implementación de Kudu está disponible (por ejemplo, versiones de implementación, rollback, restauración de paquete, automatización).

Desventaja de la implementación de un servicio de control de código fuente basado en la nube es:

- Conocimientos del servicio SCM respectivo necesario.

###<a name="vsts"></a>Cómo implementar continuamente de un servicio de control de código fuente basado en la nube
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación continua de GitHub, Bitbucket y Visual Studio Team Services.

* [Implementación continua al servicio de la aplicación de Azure](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Implementar desde Git local
Si el equipo de desarrollo utiliza un servicio de management (SCM) de código de origen local local basado en Git, puede configurar como una fuente de distribución al servicio de la aplicación. 

Los profesionales de implementación desde Git local son:

- Control de versiones para habilitar la reversión.
- La implementación específica de la sucursal, puede implementar diferentes bifurcaciones a diferentes [slots](web-sites-staged-publishing.md).
- Toda la funcionalidad en el motor de implementación de Kudu está disponible (por ejemplo, versiones de implementación, rollback, restauración de paquete, automatización).

Estafa de implementación desde Git local es:

- Conocimientos del sistema SCM respectivo necesario.
- No hay soluciones llave para implementación continua. 

###<a name="vsts"></a>Cómo implementar desde Git local
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación local de Git.

* [Implementación de Git local al servicio de la aplicación de Azure](app-service-deploy-local-git.md). 
* [Publicación en Web Apps desde cualquier repo git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Implementar mediante un IDE
Si ya está utilizando [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [SDK de Azure](https://azure.microsoft.com/downloads/)u otros conjuntos IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/), [Eclipse](https://www.eclipse.org)y [Xcode](https://developer.apple.com/xcode/), puede implementar en Azure directamente desde el IDE. Esta opción es ideal para un programador individual.

Visual Studio es compatible con todos los procesos de implementación de tres (FTP, Git y Web Deploy), dependiendo de sus preferencias, mientras que otros IDE puede implementar al servicio de la aplicación si tienen integración FTP o Git (vea la [información general de los procesos de implementación](#overview)).

Los profesionales de implementación mediante un IDE son:

- Reducir potencialmente los útiles para el ciclo de vida de aplicaciones end-to-end. Desarrollar, depurar, seguimiento e implementar su aplicación Azure todos sin mover fuera el IDE. 

Los inconvenientes de implementación mediante un IDE son:

- Complejidad agregada de utillaje.
- Aún requiere un sistema de control de código fuente para un proyecto de equipo.

<a name="vspros"></a>Son profesionales adicionales de la implementación con Visual Studio SDK de Azure:

- Azure SDK hace recursos Azure ciudadanos de primera clase en Visual Studio. Crear, eliminar, editar, iniciar y detener aplicaciones, consulta la base de datos de back-end SQL, live-depurar la aplicación de Azure y mucho más. 
- Edición de archivos de código en Azure en vivo.
- Depuración en directo de las aplicaciones de Azure.
- Explorador integrado de Azure.
- Sólo diferencias de implementación. 

###<a name="vs"></a>Cómo implementar directamente desde Visual Studio

* [Empezar a trabajar con Azure y ASP.NET](web-sites-dotnet-get-started.md). Cómo crear e implementar un proyecto web de ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Cómo implementar Azure WebJobs utilizando Visual Studio](websites-dotnet-deploy-webjobs.md). Cómo configurar los proyectos de aplicación de consola para que implemente como WebJobs.  
* [Implementar una aplicación de MVC de ASP.NET seguras 5 con pertenencia, OAuth y base de datos SQL para aplicaciones Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Cómo crear e implementar un proyecto web de ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y Entity Framework código primera migraciones.
* [Implementación de Web ASP.NET utilizando Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de tutoriales de 12 parte que cubre una gama más completa de las tareas de implementación que otros en esta lista. Dado que el tutorial se ha escrito, pero notas agregadas posteriormente explican qué es la falta, se agregaron algunas características de la implementación de Azure.
* [Implementar un sitio Web de ASP.NET a Azure en 2012 de Visual Studio desde un repositorio Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web ASP.NET en Visual Studio, mediante el plug-in Git para confirmar el código a Git y Azure conexión al repositorio Git. A partir de Visual Studio 2013, soporte de Git está integrada y no requiere la instalación de un complemento.

###<a name="aztk"></a>Cómo implementar el uso de los kits de herramientas de Azure para Eclipse y IntelliJ IDEA

Microsoft permite implementar aplicaciones Web en Azure directamente desde Eclipse y IntelliJ a través del [Kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse.md) y el [Kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Los tutoriales siguientes ilustran los pasos que intervienen en la implementación simple un "Hola" mundo Web App en Azure utilizando el IDE:

*  [Crear una aplicación de Web de Hello World de Azure en Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). Este tutorial muestra cómo utilizar el Kit de herramientas de Azure para Eclipse para crear e implementar un Hello World Web App para Azure.
*  [Crear una aplicación de Web de Hello World de Azure en IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Este tutorial muestra cómo utilizar el Kit de herramientas de Azure para IntelliJ para crear e implementar un Hello World Web App para Azure.


## <a name="automate"></a>Automatizar la implementación mediante herramientas de línea de comandos

* [Automatizar la implementación con MSBuild](#msbuild)
* [Copiar archivos con secuencias de comandos y herramientas FTP](#ftp)
* [Automatizar la implementación con Windows PowerShell](#powershell)
* [Automatizar la implementación de la API de administración de .NET](#api)
* [Implementar desde Azure interfaz de línea de comandos (CLI Azure)](#cli)
* [Implementación de línea de comandos de implementación Web](#webdeploy)
* [Mediante secuencias de comandos por lotes de FTP](http://support.microsoft.com/kb/96269).
 
Otra opción de implementación es un servicio basado en la nube como [Pulpo implementar](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obtener más información, vea [aplicaciones ASP.NET implementar a sitios Web de Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizar la implementación con MSBuild

Si utiliza el [IDE de Visual Studio](#vs) para el desarrollo, puede utilizar [MSBuild](http://msbuildbook.com/) para automatizar cualquier cosa que puede hacer en el IDE. Puede configurar MSBuild para utilizar [Web Deploy](#webdeploy) o [FTP y FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras relacionadas con la implementación de tareas, como implementar bases de datos.

Para obtener más información acerca de la implementación de línea de comandos mediante MSBuild, vea los siguientes recursos:

* [Implementación de Web ASP.NET con Visual Studio: implementación de la línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Décimo de una serie de tutoriales sobre la implementación de Azure utilizando Visual Studio. Muestra cómo utilizar la línea de comandos para implementar después de configurar los perfiles de publicación en Visual Studio.
* [Dentro de Microsoft Build Engine: uso de MSBuild y Team Foundation Build](http://msbuildbook.com/). Libro de copia impresa que incluye capítulos sobre cómo utilizar MSBuild para la implementación.

###<a name="powershell"></a>Automatizar la implementación con Windows PowerShell

Puede realizar las funciones de despliegue de MSBuild o FTP desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Si lo hace, también puede utilizar un conjunto de cmdlets de Windows PowerShell que hacen fácil llamar a la API de administración de resto de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Implementar una aplicación web vinculada a un repositorio de GitHub](app-service-web-arm-from-github-provision.md)
* [Provisioning de una aplicación web con una base de datos de SQL](app-service-web-arm-with-sql-database-provision.md)
* [Aprovisionar e implementar microservicios predecible en Azure](app-service-deploy-complex-application-predictably.md)
* [Automatizar la creación de aplicaciones de nube reales con Azure - todo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo del libro electrónico que explica cómo la aplicación de ejemplo que se muestra en el libro electrónico utiliza secuencias de comandos de Windows PowerShell para crear un entorno de prueba de Azure y despliegue en ella. Consulte la sección [recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obtener vínculos a documentación adicional de PowerShell de Azure.
* [Mediante secuencias de comandos de Windows PowerShell para publicar en entornos de prueba y desarrollo](../vs-azure-tools-publishing-using-powershell-scripts.md). Cómo usar Windows PowerShell implementación de secuencias de comandos que genera Visual Studio.

###<a name="api"></a>Automatizar la implementación de la API de administración de .NET

Puede escribir código C# para realizar funciones de MSBuild o FTP para la implementación. Si lo hace, puede tener acceso a la administración de Azure REST API para realizar funciones de administración del sitio.

Para obtener más información, consulte los siguientes recursos:

* [Automatizar todo con las bibliotecas de administración de Azure y. NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración de .NET y vínculos a documentación más.

###<a name="cli"></a>Implementar desde Azure interfaz de línea de comandos (CLI Azure)

Puede utilizar la línea de comandos en equipos Windows, Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la gestión del resto de Azure API mediante la CLI de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Herramientas de línea de comandos de azure](/downloads/#cmd-line-tools). Página de portal de Azure.com para la información de la herramienta de línea de comandos.

###<a name="webdeploy"></a>Implementación de línea de comandos de implementación Web

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es un software de Microsoft para la implementación en IIS que no sólo proporciona características de sincronización inteligente de archivos, pero también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no pueden ser automático cuando se utiliza FTP. Por ejemplo, Web Deploy puede implementar una nueva base de datos o actualizaciones de la base de datos junto con su aplicación web. Web Deploy también puede minimizar el tiempo necesario para actualizar un sitio existente ya que inteligentemente puede copiar sólo los archivos modificados. Microsoft Visual Studio y Team Foundation Server son compatibles con Web Deploy integrado, pero también puede utilizar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Comandos de Web Deploy son muy eficaces, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte los siguientes recursos:

* [Aplicaciones Web simple: implementación de](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo acerca de una herramienta que escribió para que sea más fácil utilizar Web Deploy.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial en el sitio de Microsoft TechNet. Fecha pero todavía un buen lugar para empezar.
* [Implementar mediante Web](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial en el sitio Microsoft IIS.NET. También se fechan pero un buen punto de partida.
* [Implementación de Web ASP.NET con Visual Studio: implementación de la línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación utilizado por Visual Studio, y también puede utilizarse desde la línea de comandos para implementar aplicaciones web en aplicaciones Web. Este tutorial es parte de una serie que trata principalmente de implementación de Visual Studio.

##<a name="nextsteps"></a>Próximos pasos

En algunos casos desee poder cambiar fácilmente y hacia atrás entre un ensayo y una versión de producción de la aplicación. Para obtener más información, vea [Implementación por fases de aplicaciones Web](web-sites-staged-publishing.md).

Contar con un plan de copia de seguridad y restauración es una parte importante de cualquier flujo de trabajo de implementación. Para obtener información acerca del servicio de la aplicación de copia de seguridad y restaurar la característica, consulte [Copias de seguridad de aplicaciones Web](web-sites-backup.md).  

Para obtener información acerca de cómo utilizar Control de acceso basado en roles de Azure para administrar el acceso a la implementación del servicio de la aplicación, vea [RBAC y publicación en Web de la aplicación](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
