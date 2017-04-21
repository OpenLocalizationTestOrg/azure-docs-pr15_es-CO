## <a name="connect-to-outlookcom"></a>Conectarse a Outlook.com

### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Outlook.com

Para poder utilizar su cuenta de Outlook.com en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta de Outlook.com. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure. 

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta de Outlook.com:

1. Todas las aplicaciones de lógica necesitan sea iniciado por un desencadenador, así que después de crear la aplicación de la lógica, el diseñador abrirá y muestra una lista de desencadenadores a los que puede utilizar para iniciar la lógica de la aplicación:

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. En el cuadro Buscar, escriba "outlook". Observe que la lista se filtra para obtener una lista de todos los desencadenadores con "Outlook" en el nombre:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Seleccione **Office 365 Outlook - correo electrónico nuevo**.   
  Si no ha creado ninguna conexión a Outlook antes, obtener le pedirá que proporcione sus credenciales de Outlook.com. Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de su cuenta de Outlook.com:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Proporcionar sus credenciales para Outlook e iniciar sesión en:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
  Eso es todo. Ahora ha creado una conexión con Outlook. Esta conexión estará disponible para su uso en cualquier otra aplicación de lógica que cree.


