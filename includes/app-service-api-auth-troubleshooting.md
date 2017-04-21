La mayoría de los errores de autenticación de tiempo resultan de configuración incorrecta o incoherente. Éstas son algunas sugerencias específicas para los aspectos que debe comprobar.

* Asegúrese de que no se pierda el botón **Guardar** en cualquier lugar. Esto a menudo es fácil de hacer, y el resultado es que se utilizan en los valores correctos en una página de portal, pero realmente no se han guardado en el entorno de Azure o aplicación de Azure AD.
* Para la configuración de la hoja de **Configuración de la aplicación** del portal de Azure, asegúrese de que se ha seleccionado la aplicación correcta de API o web app cuando se especificaron los valores.  También asegúrese de que los valores se han especificado como **configuración de la aplicación** y no de **las cadenas de conexión**, como el formato de las dos secciones es similar.
* Para la autenticación de un front-end de JavaScript, descargue el manifiesto para comprobar que `oauth2AllowImplicitFlow` se cambió correctamente a `true`.
* Compruebe que utiliza HTTPS dondequiera que ha configurado las direcciones URL:

    * En el código del proyecto
    * En CORS
    * En Azure parámetros de configuración de la aplicación para cada aplicación de API y la aplicación de web
    * En configuración de la aplicación de Azure AD.
    
    Tenga en cuenta que si copia la dirección URL de la aplicación de API desde el portal, a menudo tiene `http://` y tendrá que cambiarla manualmente para `https://`.

* Asegúrese de que los cambios de código se han implementado correctamente. Por ejemplo, en una solución de varios proyectos es posible cambiar el código de un proyecto y elegir de forma accidental uno de los otros cuando intenta implementar el cambio.
* Asegúrese de que va a URLs de HTTPS en el explorador, no direcciones URL de HTTP. De forma predeterminada, Visual Studio crea perfiles con las direcciones URL HTTP de publicación y eso es lo que se abre en el explorador después de implementar un proyecto.
* Para la autenticación de un front-end de JavaScript, asegúrese de que CORS está configurado correctamente en la API de la aplicación que llama el código JavaScript. Si tiene dudas acerca de si el problema está relacionado con la CORS, intente "*" como la dirección URL de origen permitido. 
* Para un front-end de JavaScript, abra la ficha de consola de herramientas de desarrollador de tu navegador para obtener más información y examinar las solicitudes HTTP en la red. Sin embargo, los mensajes de error de consola pueden ser confuso. Si recibe un mensaje que indica un error CORS, el verdadero problema puede ser la autenticación. Puede comprobar si éste es el caso, al ejecutar la aplicación con autenticación temporalmente temporalmente deshabilitada.
* Para una aplicación de API. NET, asegúrese de que está recibiendo tanta información en mensajes de error como sea posible al establecer [el modo customErrors a Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Iniciar una [sesión de depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)para una aplicación de API de .NET y examinar los valores de las variables que se pasan a código que usa ADAL para adquirir un símbolo (token) de portador o código que comprueba las reclamaciones promovidas contra el identificador principal de servicio previsto. Tenga en cuenta que el código puede tomar los valores de configuración de muchos orígenes diferentes, por lo que es posible encontrar sorpresas en esta forma. Por ejemplo, si escribe incorrectamente `ida:ClientId` como `ida:ClientID` al configurar la configuración del entorno de servicio de la aplicación de Azure, el código podría obtener el `ida:ClientId` valor que está buscando en el archivo Web.config, se omitirá la configuración del servicio de aplicación de Azure. 
* Si las cosas no funcionan en una ventana normal de Internet Explorer, en un registro existente puede estar interfiriendo; InPrivate e inténtelo Chrome o Firefox.
