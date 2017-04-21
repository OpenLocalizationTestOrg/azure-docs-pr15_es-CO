<properties
    pageTitle="Especificar una versión de Node.js"
    description="Obtenga información sobre cómo especificar la versión de Node.js utilizado por sitios Web de Azure y servicios en la nube"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Especificar una versión de Node.js en una aplicación de Azure

Al hospedar una aplicación Node.js, desea asegurarse de que la aplicación utilice una versión específica de Node.js. Hay varias formas de lograr esto para aplicaciones alojadas en Azure.

##<a name="default-versions"></a>Versiones predeterminadas

Las versiones de Node.js proporcionadas por Azure se actualizan constantemente. A menos que se especifique lo contrario, la versión predeterminada que se especifica en el `WEBSITE_NODE_DEFAULT_VERSION` se utilizará la variable de entorno. Para reemplazar este valor predeterminado, siga los pasos descritos en las secciones siguientes de este artículo

> [AZURE.NOTE] Si aloja su aplicación en un servicio de nube Azure (función web o trabajador) y es la primera vez que se ha implementado la aplicación, Azure intentará utilizar la misma versión de Node.js que ha instalado en el entorno de desarrollo si coincide con una de las versiones predeterminadas disponibles en Azure.

##<a name="versioning-with-packagejson"></a>Control de versiones con package.json

Puede especificar la versión de Node.js para utilizarse agregando lo siguiente al archivo **package.json** :

    "engines":{"node":version}

Donde *versión* es el número de versión específico para utilizar. Usted puede que puede especificar condiciones más complejas para la versión, como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Puesto que 0.6.22 no es una de las versiones disponibles en el entorno de hospedaje, la versión más alta de la serie disponible será de 0,8 usa - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Sitios Web de control de versiones con la configuración de la aplicación
Si aloja la aplicación en un sitio Web, puede establecer la variable de entorno **WEBSITE_NODE_DEFAULT_VERSION** a la versión deseada. 

##<a name="versioning-cloud-services-with-powershell"></a>Servicios de nube de control de versiones con PowerShell

Si aloja la aplicación en un servicio de nube y está implementando la aplicación mediante PowerShell de Azure, puede reemplazar la versión de Node.js predeterminada mediante el cmdlet de PowerShell **Set-AzureServiceProjectRole** . Por ejemplo:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Tenga en cuenta que los parámetros en la instrucción anterior se distinguen mayúsculas de minúsculas.  Puede comprobar que la versión correcta de Node.js se ha seleccionado comprobando la propiedad **motores** en **package.json de la función**.

También puede utilizar el **AzureServiceProjectRoleRuntime de Get** para recuperar una lista de versiones de Node.js disponibles para aplicaciones alojadas como un servicio de nube.  Compruebe siempre la versión de su proyecto depende de Node.js está en esta lista.

##<a name="using-a-custom-version-with-azure-websites"></a>Utilizando una versión personalizada con sitios Web de Azure

Mientras que Azure proporciona varias versiones predeterminadas de Node.js, desea utilizar una versión que no se proporciona de forma predeterminada. Si la aplicación se aloja como un sitio Web de Azure, puede hacerlo mediante el archivo **iisnode.yml** . Los pasos siguientes describen el proceso de usar una versión personalizada de Node.Js con un sitio Web de Azure:

1. Crear un nuevo directorio y, a continuación, cree un archivo **server.js** dentro del directorio. El archivo **server.js** debe contener lo siguiente:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Esto mostrará la versión de Node.js que se utiliza al explorar el sitio Web.

2. Crear un nuevo sitio Web y anote el nombre del sitio. Por ejemplo, el siguiente utiliza las [Herramientas de línea de comandos de Azure] para crear un nuevo sitio Web de Azure llamado **MiSitioWeb**y habilitar un repositorio Git para el sitio Web.

        azure site create mywebsite --git

3. Crear un nuevo directorio llamado **bin** como un elemento secundario del directorio que contiene el archivo **server.js** .

4. Descargue la versión específica de **node.exe** (la versión de Windows) que desea usar con su aplicación. Por ejemplo, el siguiente utiliza **doblez** para descargar la versión 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Guarde el archivo **node.exe** en la carpeta **bin** que creó anteriormente.

5. Crear un archivo **iisnode.yml** en el mismo directorio que el archivo **server.js** y, a continuación, agregue el contenido siguiente al archivo **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Esta ruta es donde se ubicará el archivo **node.exe** en el proyecto una vez haya publicado su aplicación para el sitio Web de Azure.

6. Publicar la aplicación. Por ejemplo, puesto que he creado un nuevo sitio Web con el parámetro--git anteriormente, los siguientes comandos agregar los archivos de aplicación a Mi repositorio Git local y después insertarlos en el repositorio del sitio Web:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Una vez que ha publicado la aplicación, abra el sitio Web en un explorador. Debería ver un mensaje que indica "Hola desde Azure versión nodo en ejecución: v0.8.1".

##<a name="next-steps"></a>Próximos pasos

Ahora que entiende cómo especificar la versión de Node.js utilizado por la aplicación, aprender cómo [trabajar con módulos], [compilar e implementar un sitio Web de Node.js]y [cómo utilizar las herramientas de línea de comandos de Azure para Mac y Linux].

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[Cómo utilizar las herramientas de línea de comandos de Azure para Mac y Linux]: xplat-cli-install.md
[Herramientas de línea de comandos de Azure]: xplat-cli-install.md
[trabajar con módulos]: nodejs-use-node-modules-azure-apps.md
[crear e implementar un sitio Web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
