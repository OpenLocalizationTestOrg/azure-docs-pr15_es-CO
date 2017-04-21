#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure; puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una cuenta de [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Para poder utilizar su cuenta de OneDrive en la lógica de la aplicación, autorizar la lógica de la aplicación para conectarse a su cuenta de OneDrive.  Puede hacerlo fácilmente dentro de la lógica de la aplicación en el portal de Azure. 

Autorizar la lógica de la aplicación para conectarse a su cuenta de OneDrive mediante los pasos siguientes:

1. Crear una lógica de la aplicación. En el Diseñador de aplicaciones de lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y, a continuación, escriba "onedrive" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o acciones:  
  ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si no ha creado ninguna conexión para OneDrive, se le pedirá para iniciar sesión utilizando sus credenciales de OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Seleccione **iniciar sesión**y escriba su nombre de usuario y contraseña. Seleccione **iniciar sesión en**:  
  ![](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse y tener acceso a los datos de tu cuenta de OneDrive. 
4. Seleccione **Sí** para autorizar la lógica de la aplicación para utilizar la cuenta de OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que la conexión se ha creado. Ahora, continúe con el resto de los pasos en la lógica de la aplicación:  
  ![](./media/connectors-create-api-onedrive/onedrive-5.png)
