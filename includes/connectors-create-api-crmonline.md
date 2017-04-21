#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure; puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Antes de utilizar su cuenta de Dynamics en la lógica de la aplicación, autorizar la lógica de la aplicación para conectarse a su cuenta de CRM Online. Puede hacerlo fácilmente dentro de la lógica de la aplicación en el portal de Azure. 

Autorizar la lógica de la aplicación para conectarse a su cuenta de CRM Online mediante los pasos siguientes:

1. Crear una lógica de la aplicación. En el Diseñador de aplicaciones de lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y, a continuación, escriba "dynamics" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o acciones:  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si no ha creado ninguna conexión para Dynamics, se le pedirá para iniciar sesión utilizando sus credenciales de Dynamics:  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión**. 

    Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse y tener acceso a los datos de tu cuenta de Dynamics. 
4. Observe que la conexión se ha creado. Ahora, continúe con el resto de los pasos en la lógica de la aplicación:  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
