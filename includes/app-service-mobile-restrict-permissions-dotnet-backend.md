
De forma predeterminada, las API en una aplicación móvil de back-end pueden invocarse anónimamente. A continuación, debe restringir el acceso a sólo los clientes autenticados.  

+ **Back-end de Node.js (a través del portal)** :  
    
    En **configuración**de su aplicación móvil, haga clic en **Tablas fácil** y seleccione la tabla. Haga clic en **Cambiar permisos**, seleccione **acceso autenticado sólo** para todos los permisos, haga clic en **Guardar**. 

+ **Back-end de .NET (C#)**:  

    En el proyecto de servidor, vaya a **controladores de** > **TodoItemController.cs**. Agregar el `[Authorize]` de atributo a la clase **TodoItemController** , como se indica a continuación. Para restringir el acceso únicamente a los métodos específicos, este atributo se puede aplicar sólo a esos métodos en lugar de la clase. Volver a publicar el proyecto de servidor.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end de Node.js (a través de código de Node.js)** :  
    
    Para requerir autenticación para el acceso a la tabla, agregue la siguiente línea a la secuencia de comandos de servidor Node.js:


        table.access = 'authenticated';

    Para obtener más detalles, consulte [Cómo: requerir autenticación para tener acceso a tablas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para aprender a descargar el proyecto de código de inicio rápido desde su sitio, vea [Cómo: descargar el proyecto de código de inicio rápido de Node.js back-end utilizando Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

