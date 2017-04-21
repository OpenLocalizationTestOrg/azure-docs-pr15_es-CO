
1. En el **Explorador de proyectos** en Android Studio, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Agregue el método siguiente a la clase **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Esto crea un nuevo método para controlar el proceso de autenticación. El usuario se autentica mediante un inicio de sesión de Google. Se muestra un cuadro de diálogo que muestra el ID del usuario autenticado. No puede continuar sin una autenticación positiva.

    > [AZURE.NOTE] Si utiliza un proveedor de identidad que no sea de Google, cambie el valor pasado para el método de **Inicio de sesión** anterior a uno de los siguientes: _cuentaMicrosoft_, _Facebook_, _Twitter_o _windowsazureactivedirectory_.

3. En el método **onCreate** , agregue la siguiente línea de código después del código que crea una instancia de la `MobileServiceClient` objeto.

        authenticate();

    Esta llamada inicia el proceso de autenticación.

4. Mover el código restante después de `authenticate();` en el método **onCreate** a un nuevo método **createTable** , que se ve así:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. En el menú **Ejecutar** , haga clic en **ejecutar la aplicación** para iniciar la aplicación e inicie sesión con su proveedor de identidad solicitadas. 

    Cuando haya iniciado sesión correctamente, la aplicación se debe ejecutar sin errores, y debe ser capaz de consultar el servicio back-end y realizar actualizaciones en los datos.