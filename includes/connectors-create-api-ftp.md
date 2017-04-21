### <a name="prerequisites"></a>Requisitos previos

- Una cuenta de [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol)  


Para poder utilizar su cuenta FTP en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta FTP. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure.  

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta FTP:  
1. Para crear una conexión de FTP, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y escriba *FTP* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
![Paso de creación de conexión FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Si no ha creado ninguna conexión al servidor FTP, obtener le pedirá que proporcione sus credenciales FTP. Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de tu cuenta FTP:  
![Paso de creación de conexión FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observe que la conexión se ha creado y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación:  
 ![Paso de creación de conexión FTP](./media/connectors-create-api-ftp/ftp-3.png)  
