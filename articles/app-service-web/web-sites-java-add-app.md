<properties 
    pageTitle="Agregar una aplicación de Java a Azure App servicio Web Apps" 
    description="Este tutorial muestra cómo agregar una página o aplicación a la instancia de Azure App servicio Web Apps ya está configurado para utilizar Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Agregar una aplicación de Java a Azure App servicio Web Apps

Una vez que ha inicializado su aplicación web de Java en el [Servicio de aplicación de Azure][] tal como se describe en [crear una aplicación web de Java en el servicio de aplicación de Azure](web-sites-java-get-started.md), puede cargar la aplicación colocando los WAR en la carpeta de **aplicaciones Web** .

La ruta de exploración a la carpeta **webapps** difiere en función de cómo configure la instancia de aplicaciones Web.

- Si configuró su aplicación web utilizando el catálogo de soluciones de Azure, la ruta de acceso a la carpeta **webapps** tiene la forma **d:\home\site\wwwroot\bin\application\_server\webapps**, donde **aplicación\_server** es el nombre del servidor de aplicaciones en vigor para la instancia de aplicaciones Web. 
- Si configuró su aplicación web mediante el interfaz de usuario de configuración Azure, es la ruta de acceso a la carpeta de **aplicaciones Web** en el formulario **d:\home\site\wwwroot\webapps**. 

Tenga en cuenta que puede utilizar control de código fuente para cargar la aplicación o páginas web, incluidos los [escenarios de integración continua](app-service-continuous-deployment.md). FTP es también una opción para cargar la aplicación o páginas web.

Nota para aplicaciones web de Tomcat: una vez que haya cargado el archivo WAR en la carpeta de **aplicaciones Web** , Tomcat application server detectará que ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia archivos (excepto archivos WAR) al directorio raíz, el servidor de aplicación necesitará reiniciarse antes de que se utilizan esos archivos. La funcionalidad de carga automática para las aplicaciones web de Tomcat Java ejecuta en Azure se basa en un nuevo archivo WAR que se agrega, o nuevos archivos o directorios agregados a la carpeta de **aplicaciones Web** . 

## <a name="next-steps"></a>Próximos pasos

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Servicio de aplicación de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
