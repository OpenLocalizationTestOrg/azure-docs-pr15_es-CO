**Objetivo C**: 

1. En el equipo Mac, abra _QSTodoListViewController.m_ en Xcode y agregue el método siguiente. Cambiar _google_ _cuentaMicrosoft_, _twitter_, _facebook_, o _windowsazureactivedirectory_ si no utilizas Google como proveedor de identidad. Si utilizas Facebook, [necesitará lista blanca dominios de Facebook en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Reemplazar `[self refresh]` en `viewDidLoad` en _QSTodoListViewController.m_ con lo siguiente:

            [self loginAndGetData];

3. Pulse _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Cuando está conectado, podrá ver la lista de tareas pendientes y realizar actualizaciones.

**Swift**:

1. En el equipo Mac, abra _ToDoTableViewController.swift_ en Xcode y agregue el método siguiente. Cambiar _google_ _cuentaMicrosoft_, _twitter_, _facebook_, o _windowsazureactivedirectory_ si no utilizas Google como proveedor de identidad. Si utilizas Facebook, [necesitará lista blanca dominios de Facebook en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Quite las líneas `self.refreshControl?.beginRefreshing()` y `self.onRefresh(self.refreshControl)` al final de `viewDidLoad()` en _ToDoTableViewController.swift_. Agregue una llamada a `loginAndGetData()` en su lugar:

            loginAndGetData()

3. Pulse _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Cuando está conectado, podrá ver la lista de tareas pendientes y realizar actualizaciones.
