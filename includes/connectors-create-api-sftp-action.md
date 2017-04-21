Ahora que ha agregado un desencadenador, su tiempo de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar una acción de **SFTP - carpeta de extracción** . Esta acción extraerá el contenido de un archivo si se cumplen las condiciones definidas. 

Para configurar esta acción, debe proporcionar la siguiente información. Observará que es fácil de utilizar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|SFTP: extraer propiedades de carpeta|Descripción|
|---|---|
|Ruta de archivo de archivo de origen|Se trata de la ruta de acceso del archivo que se extraen. Puede seleccionar uno de los tokens de una acción anterior o examinar el servidor SFTP para buscar la ruta de acceso del archivo.|
|Ruta de la carpeta de destino|Ésta es la ruta donde se colocarán los archivos extraídos. Puede seleccionar uno de los tokens de una acción anterior como la ruta de destino o examinar el servidor SFTP y seleccione una ruta de acceso.|
|¿Desea sobrescribir?|Indica si se encuentra un archivo con el mismo nombre que el archivo extraído en la ruta de la carpeta de destino, si se debe sobrescribir el archivo existente o no.|

Comencemos agregando la acción para extraer los archivos si la condición definida anteriormente se evalúa como *True*. 

1. Seleccione **Agregar una acción**.        
![Imagen SFTP acción condición 6](./media/connectors-create-api-sftp/condition-6.png)   
- Seleccione la acción de **SFTP - carpeta de extracción**      
![Imagen de condición SFTP acción 7](./media/connectors-create-api-sftp/condition-7.png)   
- Seleccione la **ruta del archivo de archivo de origen**              
![Imagen SFTP acción condición 9](./media/connectors-create-api-sftp/condition-9.png)   
- Seleccione el símbolo (token) de **ruta de acceso del archivo** . Esto indica que va a utilizar la ruta de acceso del archivo que se encuentra el desencadenador como la ruta de archivo del archivo de origen.           
![Imagen SFTP acción condición 10](./media/connectors-create-api-sftp/condition-10.png)   
- Seleccione la **ruta de la carpeta de destino**           
![Imagen de condición SFTP acción 11](./media/connectors-create-api-sftp/condition-11.png)   
- Seleccione el símbolo (token) de **ruta de acceso del archivo** . Esto indica que se utilizará la ruta de acceso del archivo que se encuentra el desencadenador como la ruta de destino para los archivos extraídos.   
- Escriba *\ExtractedFile* en el control de la **ruta de la carpeta de destino** . Ello sólo tras el símbolo (token) de ruta de acceso de archivo en el control de ruta de acceso de carpeta de destino.         
![Imagen SFTP acción condición 12](./media/connectors-create-api-sftp/condition-12.png)   
- Especifique *True* en el **Sobrescribir?* control para indicar que se deben sobrescribir los archivos existentes si tienen el mismo nombre que los archivos extraídos.      
![Imagen de condición de acción SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
- Guardar los cambios en el flujo de trabajo  
