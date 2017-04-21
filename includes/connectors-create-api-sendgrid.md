### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de [SendGrid](https://www.SendGrid.com/) 

Para poder utilizar su cuenta de SendGrid en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta de SendGrid. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure. 

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta de SendGrid:

1. Para crear una conexión a SendGrid, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y escriba *SendGrid* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
  ![SendGrid paso 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si no ha creado ninguna conexión para SendGrid antes, obtener le pedirá que proporcione sus credenciales de SendGrid. Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de su cuenta de SendGrid:  
  ![SendGrid paso 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observe que la conexión se ha creado y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación:  
  ![Paso 3 de SendGrid](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
