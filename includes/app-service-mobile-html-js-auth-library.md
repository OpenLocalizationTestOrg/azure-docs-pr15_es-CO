###<a name="server-auth"></a>Cómo: autenticar con un proveedor (servidor de flujo)

Para aplicaciones móviles administrar el proceso de autenticación en su aplicación, debe registrar la aplicación con el proveedor de identidad. A continuación, en el servicio de aplicación de Azure, necesitará configurar el identificador de la aplicación y el secreto proporcionada por su proveedor.
Para obtener más información, vea el tutorial [agregar autenticación para su aplicación].

Una vez que haya registrado el proveedor de identidad, simplemente llame al método .login() con el nombre de su proveedor. Por ejemplo, para iniciar la sesión con el uso de Facebook en el código siguiente.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si utiliza un proveedor de identidad que no sea de Facebook, cambie el valor pasado para el método de inicio de sesión anterior a uno de los siguientes: `microsoftaccount`, `facebook`, `twitter`, `google`, o `aad`.

En este caso, servicio de aplicación de Azure administra el flujo de autenticación OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y genera un testigo de autenticación de servicio de la aplicación después de iniciar sesión correctamente con el proveedor de identidad. La función de inicio de sesión, cuando haya terminado, devuelve un objeto JSON (usuario) que expone el identificador de usuario y el servicio de aplicación token de autenticación en los campos ID de usuario y authenticationToken, respectivamente. Este token se puede almacenar en caché y volver a utilizar hasta que caduca.

###<a name="client-auth"></a>Cómo: autenticar con un proveedor (flujo de cliente)

Su aplicación puede también por separado, póngase en contacto con el proveedor de identidad y, a continuación, proporcionar el token devuelto al servicio de la aplicación para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o para recuperar datos de usuario adicionales desde el proveedor de identidad.

#### <a name="social-authentication-basic-example"></a>Ejemplo básico de autenticación social

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
En este ejemplo se supone que el token proporcionado por el proveedor respectivo SDK se almacena en la variable token.

#### <a name="microsoft-account-example"></a>Ejemplo Microsoft Account

En el ejemplo siguiente se utiliza el SDK de Live, que admite single-sign-on para aplicaciones de almacén de Windows mediante Microsoft Account:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Este ejemplo obtiene un símbolo (token) de Live Connect, que se suministra al servicio de la aplicación mediante una llamada a la función de inicio de sesión.

###<a name="auth-getinfo"></a>Cómo: obtener información sobre el usuario autenticado

La información de autenticación para el usuario actual puede obtenerse de la `/.auth/me` extremo utilizando cualquier método de AJAX.  Asegúrese de que el `X-ZUMO-AUTH` encabezado para el token de autenticación.  El símbolo de autenticación se almacena en `client.currentUser.mobileServiceAuthenticationToken`.  Por ejemplo, para utilizar la API de recuperación:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

FETCH está disponible como un paquete npm o explorador descargar desde CDNJS. También podría utilizar jQuery u otra API de AJAX para obtener la información.  Se recibirán datos como un objeto JSON.
