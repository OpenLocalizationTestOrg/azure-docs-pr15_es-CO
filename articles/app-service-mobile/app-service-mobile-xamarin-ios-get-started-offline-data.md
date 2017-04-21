<properties
    pageTitle="Habilitar la sincronización sin conexión para su aplicación móvil de Azure (iOS de Xamarin)"
    description="Aprenda a usar la aplicación servicio Mobile App en caché y sincronizar datos sin conexión en la aplicación de iOS de Xamarin"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Habilitar la sincronización sin conexión para aplicaciones móviles Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

Este tutorial presenta la característica de sincronización sin conexión de aplicaciones móviles de Azure para Xamarin.iOS. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil--ver, agregar o modificar datos, incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. Una vez que el dispositivo se vuelve a conectar, los cambios se sincronizan con el servicio remoto.

En este tutorial, actualizar el proyecto de aplicación de Xamarin.iOS de [crear una aplicación de iOS de Xamarin] para admitir las características de aplicaciones de Azure móviles sin conexión. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso a datos a su proyecto. Para obtener más información acerca de los paquetes de la extensión de servidor, vea [trabajar con el servidor back-end de .NET SDK para aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema de la [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Actualización de la aplicación cliente para admitir características sin conexión

Características sin conexión de Azure App Mobile permiten interactuar con una base de datos local en un escenario sin conexión. Para utilizar estas características en su aplicación, inicializar un [SyncContext] en un almacén local. Hacer referencia a la tabla a través de la interfaz [IMobileServiceSyncTable]. SQLite se utiliza como el almacén local del dispositivo.

1. Abra el Administrador de paquetes de NuGet en el proyecto que se ha completado el tutorial de [crear una aplicación de iOS de Xamarin] , a continuación, buscar e instalar el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Abra el archivo QSTodoService.cs y quite el comentario de la `#define OFFLINE_SYNC_ENABLED` definición.

3. Vuelva a generar y ejecutar la aplicación cliente. La aplicación funciona igual como lo hacía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos se rellena con datos que pueden utilizarse en un escenario sin conexión.

## <a name="update-sync"></a>Actualización de la aplicación para desconectarse del servidor de

En esta sección, se interrumpe la conexión a la aplicación Mobile de back-end para simular una situación sin conexión. Cuando se agregan elementos de datos, el controlador de excepciones indica que la aplicación está en modo sin conexión. En este estado, nuevos elementos agregan en el almacén local y se sincronizará con el servidor de la aplicación móvil cuando la inserción se vuelva a ejecutar en un estado de conexión.

1. Editar QSToDoService.cs en el proyecto compartido. Cambiar el **ApplicationUrl** para que apunte a una dirección URL no válida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    También puede demostrar el comportamiento sin conexión deshabilitando wifi y redes de telefonía móvil en el dispositivo o utilizando el modo de avión.

2. Genere y ejecute la aplicación. Observe que la sincronización no se pudo actualizar cuando se inicia la aplicación.

3. Introduzca nuevos elementos y observe que inserción produce un estado de [CancelledByNetworkError] cada vez que haga clic en **Guardar**. Sin embargo, los nuevos elementos de todo existen en el almacén local hasta que se pueden insertar en el back-end de la aplicación móvil.  En una aplicación de producción, si se suprimen estas excepciones en la aplicación cliente se comporta como si aún está conectado a la aplicación móvil de back-end.

4. Cierre la aplicación y reiniciarla para comprobar que los nuevos elementos que creó se conservan en el almacén local.

5. (Opcional) Si tiene Visual Studio instalado en un PC, abra el **Explorador de servidores**. Desplácese a la base de datos en **Azure**-> **Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede examinar la tabla de base de datos SQL y su contenido. Compruebe que no ha cambiado los datos de la base de datos back-end.

6. (Opcional) Utilice una herramienta de resto como Fiddler o cartero a tu móvil backend, utilizando una consulta GET en el formulario de consulta `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar la aplicación Mobile de back-end

En esta sección, vuelva a conectar la aplicación móvil de back-end de la aplicación. Esto simula el movimiento desde un estado sin conexión en un estado en línea con la aplicación móvil de back-end de la aplicación.   Si simula la rotura de la red al desactivar la conectividad de red, cambios de código no necesarios.
La red para volver a activar.  Primera vez que ejecute la aplicación, el `RefreshDataAsync` se llama al método. Esto a su vez llama `SyncAsync` para sincronizar el almacén local con la base de datos back-end.

1. Abra QSToDoService.cs en el proyecto compartido, y revertir el cambio de la propiedad **ApplicationUrl del** .

2. Vuelva a generar y ejecutar la aplicación. La aplicación sincroniza los cambios locales con la aplicación Mobile de Azure back-end mediante operaciones de inserción y de extracción cuando el `OnRefreshItemsSelected` método se ejecuta.

3. (Opcional) Ver los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler. Se ha sincronizado el aviso los datos entre la base de datos de back-end de Azure Mobile App y el almacén local.

4. En la aplicación, haga clic en la casilla de verificación al lado de algunos elementos para completarlos en el almacén local.

  `CompleteItemAsync`llamadas `SyncAsync` al elemento completada la sincronización cada con la aplicación Mobile de back-end. `SyncAsync`llama la inserción y extracción.
  **Cada vez que se ejecuta una extracción contra una tabla que el cliente ha realizado cambios, una inserción en el contexto de sincronización de cliente siempre se ejecuta en primer lugar automáticamente**. La inserción implícita garantiza que todas las tablas en el almacén local junto con las relaciones siguen siendo coherentes. Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

## <a name="review-the-client-sync-code"></a>Revise el código de sincronización de cliente

El proyecto de cliente de Xamarin que ha descargado cuando ya finalizado el tutorial, [crear una aplicación de iOS de Xamarin] contiene código admite la sincronización sin conexión con una base de datos local SQLite. Presentamos una breve descripción de lo que ya está incluido en el código del tutorial. Para obtener información general de la característica, consulte [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

* Antes de poder realizar cualquier operación de la tabla, se debe inicializar el almacén local. Se inicializa la base de datos del almacén local cuando `QSTodoListViewController.ViewDidLoad()` se ejecuta `QSTodoService.InitializeStoreAsync()`. Este método crea un nuevo local SQLite base de datos mediante el `MobileServiceSQLiteStore` clase proporcionada por el cliente de aplicación de Azure Mobile SDK.

    El `DefineTable` método crea una tabla en el almacén local que coincide con los campos de tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota. Es posible almacenar sólo un subconjunto de columnas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* El `todoTable` miembro de `QSTodoService` es de la `IMobileServiceSyncTable` escriba en lugar de `IMobileServiceTable`. El IMobileServiceSyncTable dirige todo crear, leer, actualizar y eliminar (CRUD) operaciones de tabla a la base de datos del almacén local.

    Decide cuando esos cambios se insertan en el back-end de Azure Mobile App llamando a `IMobileServiceSyncContext.PushAsync()`. El contexto de sincronización ayuda a conservar las relaciones de tablas de seguimiento y empujando cambios en todas las tablas que ha modificado una aplicación cliente cuando `PushAsync` se llama.

    El código proporcionado llama `QSTodoService.SyncAsync()` sincronizar cada vez que se actualiza la lista de todoitem o un todoitem se agrega o se ha completado. Las sincronizaciones de la aplicación después de cada cambio local. Si una extracción se ejecuta contra una tabla que se espera de actualizaciones locales de seguimiento por el contexto, esa operación de extracción activará automáticamente una inserción de contexto en primer lugar.

    En el código proporcionado, todos los registros en el control remoto `TodoItem` se consulta la tabla, pero también es posible filtrar registros pasando un identificador de consulta y consulta a `PushAsync`. Para obtener más información, consulte la sección *Sincronización Incremental* en la [Sincronización de datos sin conexión en aplicaciones móviles de Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles Azure]
* [Azure aplicaciones móviles .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Crear una aplicación de iOS de Xamarin]: app-service-mobile-xamarin-ios-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md