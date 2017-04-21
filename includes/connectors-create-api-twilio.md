### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Twilio
- Un número de teléfono de Twilio comprobado que puede recibir SMS
- Un número de teléfono de Twilio comprobado que puede enviar SMS

>[AZURE.NOTE] Si está utilizando una cuenta de prueba de Twilio, sólo puede enviar SMS a números de teléfono **verificado** .  

Para poder utilizar su cuenta de Twilio en la lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta de Twilio. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el Portal de Azure. 

Estos son los pasos para autorizar la lógica de la aplicación para conectarse a su cuenta de Twilio:

1. Para crear una conexión a Twilio, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable y escriba *Twilio* en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador:  
  ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Si no ha creado ninguna conexión para Twilio antes, obtener le pedirá que proporcione sus credenciales de Twilio. Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse a y tener acceso a los datos de su cuenta de Twilio:  
  ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Necesitará el **token de acceso de Twilio** y un **id de Twilio** en el panel de Twilio, por lo que inicie sesión en su cuenta de Twilio ahora para tomar estos dos fragmentos de información:  
  ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio y lógica de aplicaciones utilizan nombres distintos para identificar estos dos fragmentos de información. Le mostramos cómo debe asignar al cuadro de diálogo aplicaciones de lógica:![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Seleccione el botón **Crear conexión** :  
  ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Observe que la conexión se ha creado y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación:  
  ![](./media/connectors-create-api-twilio/twilio-5.png)