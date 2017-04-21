
El ejemplo anterior mostraba un estándar de iniciar sesión, lo que requiere el cliente ponerse en contacto con el proveedor de identidad y el servicio Azure del servidor cada vez que se inicia la aplicación. No sólo es ineficaz, lo que puede ejecutar muchos clientes intentará iniciar la aplicación al mismo tiempo en cuestiones relacionadas con el uso de este método. Un mejor enfoque consiste en almacenar en caché el token de autorización devuelto por el servicio de Azure y pruebe a utilizar esta primera antes de utilizar un signo de basado en proveedores. 

>[AZURE.NOTE]Puede almacenar en caché el token emitido por el servicio Azure independientemente de si utiliza la autenticación de cliente administrado o administrado por el servicio del servidor. Este tutorial utiliza administrado por el servicio de autenticación.


1. Abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Agregar el los siguientes miembros a la `ToDoActivity` clase.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. En el archivo ToDoActivity.java, agregue el la siguiente definición para la `cacheUserToken` método.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Este método almacena el identificador de usuario y el símbolo (token) en un archivo de preferencias que se marca como privado. Esto debe proteger el acceso a la memoria caché para que otras aplicaciones en el dispositivo no tienen acceso al token porque la preferencia está en un espacio aislado de la aplicación. Sin embargo, si alguien tiene acceso al dispositivo, es posible que puede tener acceso a la caché de símbolo (token) a través de otros medios. 

    >[AZURE.NOTE]Además, puede proteger el token con cifrado si el token de acceso a los datos se considera confidencial y alguien puede tener acceso al dispositivo. Sin embargo, una solución completamente segura queda fuera del alcance de este tutorial y que dependen de sus requisitos de seguridad.


4. En el archivo ToDoActivity.java, agregue el la siguiente definición para la `loadUserTokenCache` método.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. En el archivo *ToDoActivity.java* , reemplace el `authenticate` método con el siguiente método que utiliza una caché de símbolo (token). Cambiar el proveedor de inicio de sesión si desea utilizar una cuenta distinta de Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Generar la autenticación de la aplicación y la prueba utilizando una cuenta válida. Ejecutar al menos dos veces. Durante la primera ejecución, debe recibir un mensaje al iniciar sesión y crear la caché de símbolo (token). Después, cada ejecución intentará cargar la caché de símbolo (token) para la autenticación y no debe exigirse a inicio de sesión.



