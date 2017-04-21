<properties
    pageTitle="Habilitar la sincronización sin conexión para su aplicación de Azure Mobile (Xamarin Android)"
    description="Aprenda a usar la aplicación servicio Mobile App a caché y sincronización de datos sin conexión en la aplicación Xamarin Android"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Habilitar la sincronización sin conexión para aplicaciones móviles Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

Este tutorial presenta la característica de sincronización sin conexión de aplicaciones móviles de Azure para Xamarin.Android. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil--ver, agregar o modificar datos, incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local.
Una vez que el dispositivo se vuelve a conectar, los cambios se sincronizan con el servicio remoto.

En este tutorial, actualiza el proyecto de cliente desde el tutorial [crear una aplicación Xamarin Android] para admitir las características de aplicaciones de Azure móviles sin conexión. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso a datos a su proyecto. Para obtener más información acerca de los paquetes de la extensión de servidor, vea [trabajar con el servidor back-end de .NET SDK para aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema de la [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Actualización de la aplicación cliente para admitir características sin conexión

Características sin conexión de Azure App Mobile permiten interactuar con una base de datos local en un escenario sin conexión. Para utilizar estas características en su aplicación, inicializar un [SyncContext] en un almacén local. A continuación, hacer referencia a la tabla a través de la interfaz [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite se utiliza como el almacén local del dispositivo.

1. En Visual Studio, abra el Administrador de paquetes de NuGet en el proyecto que se ha completado en el tutorial para [crear una aplicación Xamarin Android] .  Buscar e instalar el paquete de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Abra el archivo ToDoActivity.cs y quite el comentario de la `#define OFFLINE_SYNC_ENABLED` definición.

3. En Visual Studio, presione la tecla **F5** para generar y ejecutar la aplicación cliente. La aplicación funciona igual como lo hacía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos se rellena con datos que pueden utilizarse en un escenario sin conexión.

## <a name="update-sync"></a>Actualización de la aplicación para desconectarse del servidor de

En esta sección, se interrumpe la conexión a la aplicación Mobile de back-end para simular una situación sin conexión. Cuando se agregan elementos de datos, el controlador de excepciones indica que la aplicación está en modo sin conexión. En este estado, nuevos elementos agregan en el almacén local y se sincronizan con el servidor de la aplicación móvil cuando se ejecuta una inserción en estado conectado.

1. Editar ToDoActivity.cs en el proyecto compartido. Cambiar el **ApplicationUrl** para que apunte a una dirección URL no válida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    También puede demostrar el comportamiento sin conexión deshabilitando wifi y redes de telefonía móvil en el dispositivo o utilizando el modo de avión.

2. Presione **F5** para generar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inicia la aplicación.

3. Introduzca nuevos elementos y observe que inserción produce un estado de [CancelledByNetworkError] cada vez que haga clic en **Guardar**. Sin embargo, los nuevos elementos de todo existen en el almacén local hasta que se pueden insertar en el back-end de la aplicación móvil.  En una aplicación de producción, si se suprimen estas excepciones en la aplicación cliente se comporta como si aún está conectado a la aplicación móvil de back-end.

4. Cierre la aplicación y reiniciarla para comprobar que los nuevos elementos que creó se conservan en el almacén local.

5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Desplácese a la base de datos en **Azure**->**Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede examinar la tabla de base de datos SQL y su contenido. Compruebe que no ha cambiado los datos de la base de datos back-end.

6. (Opcional) Utilice una herramienta de resto como Fiddler o cartero a tu móvil backend, utilizando una consulta GET en el formulario de consulta `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar la aplicación Mobile de back-end

En esta sección, vuelva a conectar la aplicación móvil de back-end de la aplicación. Primera vez que ejecute la aplicación, el `OnCreate` controlador de eventos llama `OnRefreshItemsSelected`. Este método llama a `SyncAsync` para sincronizar el almacén local con la base de datos back-end.

1. Abra ToDoActivity.cs en el proyecto compartido, y revertir el cambio de la propiedad **ApplicationUrl del** .

2. Presione la tecla **F5** para generar y ejecutar la aplicación. La aplicación sincroniza los cambios locales con la aplicación Mobile de Azure back-end mediante operaciones de inserción y de extracción cuando el `OnRefreshItemsSelected` método se ejecuta.

3. (Opcional) Ver los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler. Se ha sincronizado el aviso los datos entre la base de datos de back-end de Azure Mobile App y el almacén local.

4. En la aplicación, haga clic en la casilla de verificación al lado de algunos elementos para completarlos en el almacén local.

  `CheckItem`llamadas `SyncAsync` al elemento completada la sincronización cada con la aplicación Mobile de back-end. `SyncAsync`llama la inserción y extracción. **Cada vez que se ejecuta una extracción contra una tabla que el cliente ha realizado cambios, siempre se ejecuta automáticamente una inserción**. Esto garantiza que todas las tablas en el almacén local junto con las relaciones siguen siendo coherentes. Este comportamiento puede resultar en una inserción inesperada. Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

## <a name="review-the-client-sync-code"></a>Revise el código de sincronización de cliente

El proyecto de cliente de Xamarin que ha descargado cuando ya completado el tutorial [crear aplicaciones Xamarin Android] contiene código admite la sincronización sin conexión con una base de datos local SQLite. Presentamos una breve descripción de lo que ya está incluido en el tutorial ode c. Para obtener información general de la característica, consulte [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

* Antes de poder realizar cualquier operación de la tabla, se debe inicializar el almacén local. Se inicializa la base de datos del almacén local cuando `ToDoActivity.OnCreate()` se ejecuta `ToDoActivity.InitLocalStoreAsync()`. Este método crea una base de datos de SQLite local utilizando la `MobileServiceSQLiteStore` clase proporcionada por el cliente de aplicaciones móviles de Azure SDK.

    El `DefineTable` método crea una tabla en el almacén local que coincide con los campos de tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota. Es posible almacenar sólo un subconjunto de columnas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* El `toDoTable` miembro de `ToDoActivity` es de la `IMobileServiceSyncTable` escriba en lugar de `IMobileServiceTable`. El IMobileServiceSyncTable dirige todo crear, leer, actualizar y eliminar (CRUD) operaciones de tabla a la base de datos del almacén local.

    Decide cuando se pulsan los cambios a la aplicación Mobile de Azure de back-end mediante una llamada a `IMobileServiceSyncContext.PushAsync()`. El contexto de sincronización ayuda a conservar las relaciones de tablas de seguimiento y empujando cambios en todas las tablas que ha modificado una aplicación cliente cuando `PushAsync` se llama.

    El código proporcionado llama `ToDoActivity.SyncAsync()` sincronizar cada vez que se actualiza la lista de todoitem o un todoitem se agrega o se ha completado. Sincronizaciones de código después de cada cambio local.

    En el código proporcionado, todos los registros en el control remoto `TodoItem` se consulta la tabla, pero también es posible filtrar registros pasando un identificador de consulta y consulta a `PushAsync`. Para obtener más información, consulte la sección *Sincronización Incremental* en la [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles Azure]
* [Azure aplicaciones móviles .NET SDK HOWTO][8]

<!-- URLs. -->
[Crear una aplicación Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Crear una aplicación Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
