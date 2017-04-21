### <a name="prerequisites"></a>Requisitos previos

- Una cuenta [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Para poder utilizar su cuenta de SMTP en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta de SMTP. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure.  

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta de SMTP:  
1. Para crear una conexión SMTP, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y escriba *SMTP* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Si no ha creado ninguna conexión para SMTP antes, obtener le pedirá que proporcione sus credenciales de SMTP. Estas credenciales se usará para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de su cuenta de SMTP:  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Observe que la conexión se ha creado y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación:  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

