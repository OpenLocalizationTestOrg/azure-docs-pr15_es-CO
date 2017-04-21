Ahora que ha agregado un desencadenador, su tiempo de hacer algo interesante con los datos que se generan el desencadenador. Siga estos pasos para agregar la acción de **SharePoint Online - crear archivo** . Esta acción creará un archivo en SharePoint Online cada vez que se activa el desencadenador de elemento nuevo. 

Para configurar esta acción, debe proporcionar la siguiente información. Si proporciona esta información, observará que es fácil de utilizar datos generados por el desencadenador como entrada para algunas de las propiedades para el nuevo archivo:

|Crear propiedades de archivo|Descripción|
|---|---|
|Dirección URL del sitio|Se trata de la dirección URL del sitio en línea de SharePoint donde desee crear el nuevo archivo. Seleccione el sitio de la lista.|
|Ruta de la carpeta|Ésta es la carpeta (en la dirección URL del sitio seleccionado en el paso anterior) donde se colocará el nuevo archivo. Busque y seleccione la carpeta.|
|Nombre de archivo|Este es el nombre del archivo que se está creando.|
|Contenido del archivo|El contenido que se escribirán en el archivo.|

1. Seleccione **+ nuevo paso** para agregar la acción.  
![Imagen de SharePoint online acción 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Seleccione el vínculo **Agregar una acción** . Se abre el cuadro de búsqueda, donde puede buscar cualquier acción le gustaría tener. Para este ejemplo, las acciones de SharePoint son de interés.    
![Imagen de acción en línea de SharePoint 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Escriba *sharepoint* para buscar acciones relacionadas con SharePoint.
- Seleccione **SharePoint Online - crear archivo** como la acción que se realizará.   **Nota**: se le pedirá para autorizar su lógica de la aplicación para tener acceso a su cuenta de SharePoint si no ha creado previamente una conexión con SharePoint Online.    
![Imagen de acción en línea de SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- El control **crea un archivo** se abre.   
![Imagen de acción en línea de SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Seleccione la **Dirección URL del sitio** y examinar para encontrar el sitio donde desea crear el archivo.     
![Imagen de acción en línea de SharePoint 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Seleccione la **ruta de la carpeta** y busque la carpeta donde se colocará el nuevo archivo.  
![Imagen de acción en línea de SharePoint 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Seleccione el control de **nombre de archivo** y escriba el nombre del archivo que desea crear. En este caso, puede introducir directamente el nombre del archivo o puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente. Para ello, seleccione Propiedades de la lista de **resultados de cuando se crea un nuevo elemento**. Esta lista es sólo de visualización después de seleccionar el control de **nombre de archivo** . En este tutorial, seleccioné ID (identificador del nuevo elemento de lista) como el nombre del archivo creado por la acción de **SharePoint Online - crear archivo** .    
![Imagen de SharePoint online acción 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Seleccione el control de **contenido del archivo** y escriba el contenido que se escribirán en el archivo que se va a crear. Para el contenido del archivo, tenga en cuenta que puede utilizar cualquiera de las propiedades desde el desencadenador creado previamente. Simplemente seleccione las propiedades de la lista. Como alternativa, puede introducir el **contenido del archivo** de texto directamente en el control. En este ejemplo, he seleccionado algunas propiedades y agregar espacios y un guión entre cada propiedad.        
![Imagen 8 de acción en línea de SharePoint](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guardar los cambios en el flujo de trabajo  
- Enhorabuena, ahora tiene una aplicación lógica totalmente funcional que se desencadena cuando se agrega un nuevo elemento a una lista de SharePoint Online. La aplicación creará un archivo, utilizando algunas de las propiedades del nuevo elemento de lista.  Ahora puede probar creando un nuevo elemento en la lista de SharePoint. 
