Ahora que ha agregado una condición, es hora de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar la acción de la **fuerza de ventas: obtener el objeto** . Esta acción se obtendrá los datos cada vez que se crea un nuevo cliente potencial. También agregará una segunda acción que utilizará los datos de la fuerza de ventas - Get una acción de objeto para enviar un correo electrónico mediante el conector de Office 365.  

Para configurar esta acción, debe proporcionar la siguiente información. Observará que es fácil de utilizar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|Crear propiedades de archivo|Descripción|
|---|---|
|Tipo de objeto|Este es el tipo de objeto de Salesforce que le interesa. Algunos ejemplos son el cliente potencial, cuenta, etcetera.|
|Id. de objeto|Esto representa un identificador para el objeto.|


1. Seleccione el vínculo **Agregar una acción** . Se abre el cuadro de búsqueda, donde puede buscar cualquier acción le gustaría tener. Para este ejemplo, las acciones de la fuerza de ventas son de interés.      
![Imagen de acción de la fuerza de ventas 1](./media/connectors-create-api-salesforce/action-1.png)  
- Introduzca la *fuerza de ventas* para buscar acciones relacionadas con la fuerza de ventas.
- Seleccione **Salesforce - objeto Get** como la acción que se realizará.   **Nota**: se le pedirá para autorizar su aplicación lógica para tener acceso a su cuenta de la fuerza de ventas si no se ha hecho previamente.    
![Imagen de acción de la fuerza de ventas 2](./media/connectors-create-api-salesforce/action-2.png)    
- Se abre el control para **obtener el objeto** .  
- Como el tipo de objeto, seleccione *cliente potencial* .
- Seleccione el control de **Identificador de objeto** .
- Seleccione **...** para expandir la lista de símbolos (tokens) que puede utilizarse como entrada para las acciones.       
![Imagen de acción de la fuerza de ventas 3](./media/connectors-create-api-salesforce/action-3.png)    
- Se abre el control SELECT de **Identificador del cliente potencial** .   
![Imagen de acción de la fuerza de ventas 4](./media/connectors-create-api-salesforce/action-4.png)     
- Observe que el token de identificador del cliente potencial está ahora en el control de identificador de objeto, que indica que la acción de objeto Get buscará para un cliente potencial con un identificador que está en el ID de cliente potencial de plomo que desencadenó la lógica de la aplicación.  
![Imagen de acción de la fuerza de ventas 5](./media/connectors-create-api-salesforce/action-5.png)  
- Guarde su trabajo. Eso es todo, la acción de objeto Get que haya agregado a su lógica de la aplicación. El control de objeto Get debería tener este aspecto:    
![Imagen de acción de la fuerza de ventas 6](./media/connectors-create-api-salesforce/action-6.png)  

Ahora que ha agregado una acción para obtener clientes potenciales, desea hacer algo interesante con el cliente potencial recién creado. En una empresa, desea enviar un correo electrónico para notificar a una lista de distribución que se ha creado un nuevo cliente potencial. Vamos a usar el conector de Office 365 para enviar un correo electrónico con la información pertinente desde el objeto de cliente potencial de Salesforce.  

1. Seleccione **una acción de agregar** , a continuación, escriba el *correo electrónico* en el control de búsqueda. Esto filtra las acciones a las que están relacionados con enviar y recibir correo electrónico.  
- Seleccione el elemento de la lista de **Office 365 Outlook - enviar un correo electrónico** . Si no ha creado todavía una *conexión* a su cuenta de Office 365, se le pedirá que escriba sus credenciales de Office 365 para crearla ahora. Una vez haya terminado, se abre el control **Enviar un correo electrónico** .        
![Imagen de acción de la fuerza de ventas 7](./media/connectors-create-api-salesforce/action-7.png)  
- Escriba la dirección de correo electrónico que le gustaría enviar mensaje de correo electrónico en el control **a** .
-  En el control de **asunto** , escriba un *nuevo cliente potencial creado* : a continuación, seleccione el símbolo (token) de *la empresa* . Se mostrará el campo *compañía* de la jefa de nuevo creado en la fuerza de ventas.  
-  En el control del **cuerpo** , puede seleccionar cualquiera de los símbolos del nuevo objeto de plomo y también puede escribir cualquier texto que le gustaría mostrar en el cuerpo del correo electrónico. Éste es un ejemplo:  
![Imagen de acción de la fuerza de ventas 8](./media/connectors-create-api-salesforce/action-8.png)   
- Guardar el flujo de trabajo.  

Eso es todo. La lógica de la aplicación está ahora completa.  

Ahora, puede probar la lógica de la aplicación: en la fuerza de ventas, crear un nuevo cliente potencial que cumpla la condición que creó.  Si ha seguido completamente este tutorial, cree un cliente potencial con una dirección de correo electrónico que contenga *amazon.com* en él. Después de unos segundos debe activarse la aplicación lógica y el resultado puede ser similar a esto:  
![Imagen de acción de la fuerza de ventas 9](./media/connectors-create-api-salesforce/action-9.png)  

