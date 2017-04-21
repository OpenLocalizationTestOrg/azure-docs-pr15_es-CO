Así es como utilizar el desencadenador de **Servicio Bus: cuando se recibe un mensaje en una cola** para iniciar un flujo de trabajo de la aplicación de lógica cuando se envía un elemento nuevo a una cola de Bus de servicio.  

>[AZURE.NOTE]Se le pedirá que iniciar una sesión con la cadena de conexión de Bus de servicio si aún no ha creado una conexión de Bus de servicio.  

1. En el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, escriba **bus de servicio**. A continuación, seleccione el desencadenador de **Servicio Bus: cuando se recibe un mensaje de una cola** .  
![Imagen de activación de Bus de servicio 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- Se muestra el cuadro de diálogo **cuando se recibe un mensaje de una cola** .  
![Imagen de activación de Bus de servicio 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Escriba el nombre de la cola de Bus de servicio que le gustaría supervisar el desencadenador.   
![Imagen de activación de Bus de servicio 3](./media/connectors-create-api-servicebus/trigger-3.png)   

En este punto, la lógica de la aplicación se ha configurado con un desencadenador. Cuando se recibe un elemento nuevo en la cola seleccionada, el desencadenador iniciará una ejecución los otros desencadenadores y acciones en el flujo de trabajo.    
