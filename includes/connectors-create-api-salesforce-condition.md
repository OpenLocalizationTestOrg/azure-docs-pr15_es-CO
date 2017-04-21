Esta condición evaluará el campo de dirección de correo electrónico de cada nuevo cliente potencial de Salesforce. Si la dirección de correo electrónico contiene *amazon.com*, el resultado de la condición será *True*.

1. Seleccione **+ nuevo paso**.  
![Imagen de la fuerza de ventas condición 1](./media/connectors-create-api-salesforce/condition-1.png)   
- Seleccione **Agregar una condición**.    
![Imagen de la fuerza de ventas condición 2](./media/connectors-create-api-salesforce/condition-2.png)  
- Seleccione **Elegir un valor**.    
![Imagen de condición de fuerza de ventas 3](./media/connectors-create-api-salesforce/condition-3.png)  
- Seleccione el símbolo (token) de *correo electrónico* en el cliente potencial del desencadenador.    
![Imagen de la fuerza de ventas condición 4](./media/connectors-create-api-salesforce/condition-4.png)  
- Seleccione *contiene*.      
![Imagen de la fuerza de ventas condición 5](./media/connectors-create-api-salesforce/condition-5.png)  
- Seleccione **Elegir un valor** en la parte inferior del control.     
![Imagen de la fuerza de ventas condición 6](./media/connectors-create-api-salesforce/condition-6.png)  
- Escriba *amazon.com* como el valor que le gustaría evaluar la dirección de correo electrónico del cliente potencial nuevo para. Si la dirección de correo electrónico contiene *amazon.com*, la condición se evaluará como *True* y el resto de los pasos en la lógica de la aplicación puede continuar.    
![Imagen de condición de Salesforce 7](./media/connectors-create-api-salesforce/condition-7.png)  
- Guarde sus aplicaciones de lógica.  

