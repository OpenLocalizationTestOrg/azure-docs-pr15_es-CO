En este ejemplo, le mostraré cómo usar **SharePoint Online - cuando se crea un nuevo elemento de** desencadenador para iniciar un flujo de trabajo de lógica de la aplicación cuando se crea un nuevo elemento en una lista de SharePoint Online.

>[AZURE.NOTE]Obtener le para iniciar sesión en su cuenta de SharePoint si aún no ha creado una *conexión* con SharePoint Online.  

1. Escriba *sharepoint* en el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, a continuación, seleccione el desencadenador **SharePoint Online - cuando se crea un nuevo elemento** .  
![Imagen de activación en línea de SharePoint](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- Se muestra el control **cuando se crea un nuevo elemento** .  
![Imagen de activación en línea de SharePoint 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
- Seleccione una **dirección URL del sitio**. Este es el sitio de SharePoint online que desea supervisar para que los nuevos elementos activar el flujo de trabajo.  
![Imagen de activación en línea de SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
- Seleccione un **nombre de la lista**. Esta es la lista en el sitio de SharePoint Online que desea supervisar para los nuevos elementos que se activarán el flujo de trabajo.  
![Imagen de activación en línea de SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

En este punto, la lógica de la aplicación se ha configurado con un desencadenador que comience una ejecución los otros desencadenadores y acciones en el flujo de trabajo. Esto llevará a cabo cada vez que se crea un nuevo elemento en la lista de SharePoint Online que ha seleccionado.  