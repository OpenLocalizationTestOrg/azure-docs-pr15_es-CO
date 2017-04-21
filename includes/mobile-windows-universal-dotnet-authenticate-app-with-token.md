
1. En el archivo de proyecto MainPage.xaml.cs, agregue las siguientes instrucciones **using** :

        using System.Linq;      
        using Windows.Security.Credentials;

2. Reemplace el método **AuthenticateAsync** con el código siguiente:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    En esta versión de **AuthenticateAsync**, la aplicación intenta utilizar las credenciales almacenadas en la **PasswordVault** para tener acceso al servicio. Un signo de regular también se realiza cuando no hay ninguna credencial almacenada.

    >[AZURE.NOTE]Un token de caché puede estar caducado y caducidad del testigo también puede producirse después de la autenticación cuando la aplicación está en uso. Para saber cómo determinar si un símbolo está caducado, vea [buscar tokens de autenticación expirada](http://aka.ms/jww5vp). Una solución para controlar los errores de autorización relacionado con símbolos que caducan, consulte [almacenamiento en caché y control tokens caducados en Azure Mobile Services administran SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 

3. Reinicie la aplicación dos veces.

    Observe que en el primer inicio, iniciar sesión con el proveedor de nuevo es necesario. Sin embargo, en el segundo reinicio se utilizan las credenciales almacenadas en caché y se omite en el inicio de sesión. 
