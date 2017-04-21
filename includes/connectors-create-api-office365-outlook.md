#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure; puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [Office 365](https://office365.com)  

Antes de utilizar su cuenta de Office 365 en la lógica de la aplicación, autorizar la lógica de la aplicación para conectarse a su cuenta de Office 365. Puede hacerlo fácilmente dentro de la lógica de la aplicación en el portal de Azure.  

Autorizar la lógica de la aplicación para conectarse a su cuenta de Office 365 mediante los pasos siguientes:

1. Crear una lógica de la aplicación. En el Diseñador de aplicaciones de lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y, a continuación, escriba "office 365" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o acciones:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  

2. Si no ha creado ninguna conexión a Office 365, se le pedirá para iniciar sesión utilizando sus credenciales de Office 365:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  

3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión en**:  
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)

    Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse y tener acceso a su cuenta de Office 365. 

4. Observe que la conexión se ha creado. Ahora, continúe con el resto de los pasos en la lógica de la aplicación:   
    ![Paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  
  