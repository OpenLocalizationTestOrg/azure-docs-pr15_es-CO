En este tutorial, aprenderá a utilizar el desencadenador de **Salesforce - cuando se crea un objeto** para iniciar un flujo de trabajo de lógica de la aplicación cuando se crea un nuevo cliente potencial en la fuerza de ventas.

>[AZURE.NOTE]Obtener le para iniciar sesión en su cuenta de la fuerza de ventas si aún no ha creado una *conexión* con la fuerza de ventas.  

1. Escriba *fuerza de ventas* en el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, a continuación, seleccione el desencadenador de la **fuerza de ventas - cuando se crea un objeto** .  
![Imagen de activación de la fuerza de ventas 1](./media/connectors-create-api-salesforce/trigger-1.png)   
- Se muestra el control **cuando se crea un objeto** .  
![Imagen de activación de la fuerza de ventas 2](./media/connectors-create-api-salesforce/trigger-2.png)   
- Seleccione el **Tipo de objeto** , a continuación, seleccione el *cliente potencial* desde la lista de objetos. En este paso se indica que se está creando un desencadenador que notifique a su aplicación lógica siempre que se crea un nuevo cliente potencial en la fuerza de ventas.   
![Imagen de activación de la fuerza de ventas 3](./media/connectors-create-api-salesforce/trigger-3.png)   
- Eso es todo. Ha creado el desencadenador. Sin embargo, debe crear al menos una acción para ello una aplicación lógica válida.    
![Imagen de activación de la fuerza de ventas 4](./media/connectors-create-api-salesforce/trigger-4.png)   

En este punto, la lógica de la aplicación se ha configurado con un desencadenador que comience una ejecución los otros desencadenadores y acciones en el flujo de trabajo cuando se crea un nuevo elemento en la fuerza de ventas.  