### <a name="prerequisites"></a>Requisitos previos

Debe tener una cuenta de [Servicio de Bus](https://azure.microsoft.com/services/service-bus/) .  

Para poder utilizar su cuenta de Azure Service Bus en una lógica de la aplicación, debe autorizar la lógica de la aplicación para conectarse a su cuenta de bus de servicio. Afortunadamente, puede hacerlo fácilmente desde dentro de la lógica de la aplicación en el portal de Azure.  

Estos son los pasos para autorizar su lógica de la aplicación para conectarse a su cuenta de Bus de servicios:  

1. Para crear una conexión al Bus de servicio, en el Diseñador de la aplicación lógica, seleccione **Mostrar Microsoft API administradas** en la lista desplegable. A continuación, escriba el **bus de servicio** en el cuadro de búsqueda. Seleccione la acción que desea utilizar o el desencadenador.  
    ![Imagen de la conexión de Bus de servicio 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Si no ha creado ninguna conexión al Bus de servicio antes, se le pedirá que proporcione sus credenciales de Bus de servicio. Estas credenciales se utilizan para autorizar su lógica de la aplicación para conectarse y tener acceso a los datos de su cuenta de Bus de servicio. El conector de Bus de servicio necesita la cadena de conexión para el Bus de servicio de nombres. También se requiere **Administrar** permisos. Una buena manera de saber si la cadena de conexión es el espacio de nombres o una entidad específica es si contiene el `EntityPath` parámetro. Si es así, no es la cadena de conexión adecuada para una aplicación lógica.  
    ![Cadena de conexión de Bus de servicio](./media/connectors-create-api-servicebus/connectionstring.png)

1. Después de haber recibido la cadena de conexión para el espacio de nombres, se puede utilizar para la conexión de API en lógica de aplicaciones.  
    ![Imagen de la conexión de Bus de servicio 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Observe que se ha creado la conexión, y ahora está libre continuar con el resto de los pasos en la lógica de la aplicación.  
    ![Imagen de la conexión de Bus de servicio 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
