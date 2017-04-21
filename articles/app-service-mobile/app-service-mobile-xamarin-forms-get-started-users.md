<properties
    pageTitle="Empezar a trabajar con la autenticación para aplicaciones móviles en la aplicación Xamarin.Forms | Microsoft Azure"
    description="Aprenda a usar aplicaciones móviles para autenticar a los usuarios de la aplicación de formularios de Xamarin a través de diversos proveedores de identidades, incluyendo DAA, Google, Twitter, Facebook y Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Agregar autenticación para su aplicación Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Información general

Este tema muestra cómo autenticar a los usuarios de una aplicación de servicio de Mobile App desde la aplicación cliente. En este tutorial, agregar autenticación al proyecto Xamarin.Forms quickstart utiliza un proveedor de identidad que es compatible con el servicio de la aplicación. Tras ser correctamente autenticado y autorizado por su aplicación móvil, se muestra el valor de ID de usuario, y podrá tener acceso a datos de la tabla restringida.

##<a name="prerequisites"></a>Requisitos previos

Para obtener el mejor resultado con este tutorial, se recomienda que complete primero el tutorial para [crear una aplicación de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) . Después de completar este tutorial, tendrá un proyecto de Xamarin.Forms es una aplicación de TodoList multiplataforma.

Si no utiliza el proyecto de servidor de inicio rápido descargadas, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de la extensión de servidor, vea [trabajar con el servidor back-end de .NET SDK para aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar la aplicación para la autenticación y configurar los servicios de la aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir los permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Agregar autenticación a la biblioteca de clases portables

Aplicaciones móviles se utiliza el método de extensión [LoginAsync] en el [MobileServiceClient] al inicio de sesión de un usuario mediante la autenticación de servicio de la aplicación. Este ejemplo utiliza un flujo de autenticación de servidor administrado que muestra la interfaz del proveedor de inicio de sesión en la aplicación. Para obtener más información, vea [autenticación de servidor administrado](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para proporcionar una mejor experiencia de usuario en la aplicación de producción, puede considerar en lugar de utilizar [autenticación gestionado por el cliente](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Para autenticar con un proyecto Xamarin.Forms, defina una interfaz **IAuthenticate** en la biblioteca de clases portables de la aplicación. También actualizar la interfaz de usuario definida en la biblioteca de clases Portable para agregar un botón de **Inicio de sesión** , que el usuario hace clic para iniciar la autenticación. Después de una autenticación correcta, los datos se cargan desde el servidor de la aplicación móvil.

Debe implementar la interfaz **IAuthenticate** para cada plataforma soportada por la aplicación.


1. En Visual Studio o Xamarin Studio, App.cs abierto desde el proyecto con **portátiles** en el nombre, que es el proyecto de biblioteca de clases Portable, a continuación, agregue la siguiente `using` instrucción:

        using System.Threading.Tasks;

2. En App.cs, agregue la siguiente `IAuthenticate` inmediatamente antes de la definición de interfaz del `App` definición de clase.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Agregue a los siguientes miembros estáticos en la clase **App** para inicializar la interfaz con una implementación específica de la plataforma.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml desde el proyecto de biblioteca de clases Portable, agregue el siguiente elemento de **botón** en el elemento de diseño *buttonsPanel* , detrás del botón existente: 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Este botón desencadena administrados por el servidor de autenticación con el servidor de la aplicación móvil.

5. Abra TodoList.xaml.cs en el proyecto de biblioteca de clases Portable, agregue el siguiente campo a la `TodoList` clase:

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Reemplace el método **OnAppearing** con el código siguiente:

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Esto garantiza que los datos sólo se actualizan desde el servicio después de que el usuario ha sido autenticado.

7. Agregue el siguiente controlador para el evento **Clicked** a la clase **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Guarde los cambios y volver a generar el proyecto de biblioteca de clases Portable no comprobar errores.


##<a name="add-authentication-to-the-android-app"></a>Agregar autenticación a la aplicación para Android

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación para Android. Omitir esta sección si no son compatibles con los dispositivos Android.

1. En Visual Studio o Xamarin Studio, haga clic en el proyecto **droide** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) después de iniciar la aplicación. Esto sucede porque el acceso en el back-end está restringido sólo a usuarios autorizados.

3. Abra MainActivity.cs en el proyecto Android y agregue el siguiente `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Actualizar la clase de **actividad principal** para implementar la interfaz **IAuthenticate** , como sigue:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Actualizar la clase de **actividad principal** agregando un campo **MobileServiceUser** y un método de **autenticación** , que es necesaria para la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Si está utilizando un proveedor de identidad que no sea de Facebook, elegir un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue el código siguiente al método **OnCreate** de la clase de **actividad principal** antes de llamar a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Esto asegura que el autenticador se inicializa antes de la carga de la aplicación.

7. Volver a generar la aplicación, ejecutarla e inicio de sesión con el proveedor de autenticación ha elegido y comprobar puede tener acceso a datos como un usuario autenticado.

##<a name="add-authentication-to-the-ios-app"></a>Agregar autenticación para la aplicación de iOS

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de iOS. Omitir esta sección si no son compatibles con dispositivos iOS.

1. En Visual Studio o Xamarin Studio, haga clic en el proyecto de **iOS** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) después de iniciar la aplicación. Esto sucede porque el acceso en el back-end está restringido sólo a usuarios autorizados.

4. Abra AppDelegate.cs en el proyecto de iOS y agregue el siguiente `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Actualizar la clase **AppDelegate** para implementar la interfaz **IAuthenticate** , como sigue:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Actualizar la clase **AppDelegate** agregando un campo **MobileServiceUser** y un método de **autenticación** , que es necesaria para la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Si está utilizando un proveedor de identidad que no sea de Facebook, elegir un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código al método **FinishedLaunching** antes de llamar a `LoadApplication()`: 

        App.Init(this);

    Esto asegura que el autenticador se inicializa antes de que se cargue la aplicación.

7. Volver a generar la aplicación, ejecutarla e inicio de sesión con el proveedor de autenticación ha elegido y comprobar puede tener acceso a datos como un usuario autenticado.


##<a name="add-authentication-to-windows-app-projects"></a>Agregar autenticación para proyectos de aplicación de Windows

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el Windows 8.1 y proyectos de aplicación de Windows Phone 8.1. Los mismos pasos se aplican para los proyectos de la plataforma de Windows Universal (UWP). Omitir esta sección si no son compatibles con dispositivos de Windows.

1. En Visual Studio, haga clic en el **WinApp** o el proyecto **WinPhone81** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) después de iniciar la aplicación. Esto sucede porque el acceso en el back-end está restringido sólo a usuarios autorizados.

3. Abra MainPage.xaml.cs para el proyecto de aplicación de Windows y agregue el siguiente `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portables.

4. Actualizar la clase **MainPage** para implementar la interfaz **IAuthenticate** , como sigue:

        public sealed partial class MainPage : IAuthenticate


5. Actualizar la clase **MainPage** agregando un campo **MobileServiceUser** y un método de **autenticación** , que es necesaria para la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Si está utilizando un proveedor de identidad que no sea de Facebook, elegir un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código en el constructor para la clase **MainPage** antes de llamar a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portables.  
    Si éste es el proyecto WinApp, puede saltar hasta el paso 8. El siguiente paso se aplica sólo al proyecto WinPhone81, donde debe completar la devolución de llamada de inicio de sesión.

7. (Opcional) En el proyecto de la aplicación **WinPhone81** , abra App.xaml.cs y agregue el siguiente `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portables.

8.  Agregue la invalidación del método **OnActivated** siguiente a la clase de **la aplicación** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Cuando ya existe la invalidación del método, simplemente agregue el código condicional desde el fragmento de código anterior.

7. Volver a generar la aplicación, ejecutarla e inicio de sesión con el proveedor de autenticación ha elegido y comprobar puede tener acceso a datos como un usuario autenticado.

##<a name="next-steps"></a>Próximos pasos

Ahora que ya completado este tutorial autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar las notificaciones de inserción a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md)  
  Aprenda a agregar push notificaciones apoyan a su aplicación y configuración la aplicación Mobile de back-end para utilizar Azure concentradores de notificación para enviar las notificaciones de inserción.

+ [Habilitar la sincronización sin conexión para su aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Aprenda a agregar compatibilidad sin conexión con la aplicación mediante una aplicación móvil de back-end. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

