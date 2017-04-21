Vamos a agregar un desencadenador.

1. Escriba *sftp* en el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, a continuación, seleccione el desencadenador **SFTP - cuando se agregan o modifican un archivo**   
![Imagen de activación SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
- Se abre el control **cuando se agregan o modifican un archivo**  
![Imagen de activación SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Seleccione el **...** ubicado en el lado derecho del control. Esto abre el control de selector de carpeta  
![Imagen de activación SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
- Seleccione el **SFTP** para seleccionar la carpeta raíz como la carpeta supervisar archivos nuevos o modificados. Observe que la carpeta raíz aparece ahora en la **carpeta** control.  
![Imagen de activación SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

En este punto, la lógica de la aplicación se ha configurado con un desencadenador que comience una ejecución los otros desencadenadores y acciones en el flujo de trabajo cuando un archivo se modifica o crea en la carpeta específica de SFTP. 

>[AZURE.NOTE]Para que una lógica de la aplicación que sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos descritos en la sección siguiente para agregar una acción.  