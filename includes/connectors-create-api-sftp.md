### <a name="prerequisites"></a>Requisitos previos

- Una cuenta [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Para poder utilizar su cuenta SFTP en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta SFTP. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure.  

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta SFTP:  
1. Para crear una conexión con SFTP, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y escriba *SFTP* en el cuadro de búsqueda. Seleccione el desencadenador **SFTP - cuando se agregan o modifican un archivo** :  
![Imagen de conexión SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si no ha creado ninguna conexión para SFTP antes, obtener le pedirá que proporcione sus credenciales SFTP. Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de su cuenta SFTP:  
![Imagen de conexión SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe que la conexión se ha creado y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación:   
 ![Imagen de conexión SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 
