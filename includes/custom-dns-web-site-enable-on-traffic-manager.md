Una vez se ha propagado los registros del nombre de dominio, debe utilizar el explorador para comprobar que su nombre de dominio personalizado puede utilizarse para tener acceso a la aplicación web en el servicio de aplicación de Azure.

> [AZURE.NOTE] Puede tardar algún tiempo para el CNAME propagar a través del sistema DNS. Puede utilizar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que está disponible el registro CNAME.

Si aún no ha agregado su aplicación web como un extremo Traffic Manager, debe hacerlo para que funcione la resolución de nombres, como las rutas de nombre de dominio personalizado al administrador de tráfico. Traffic Manager se enruta a continuación a la aplicación web. Utilice la información en [Agregar o eliminar extremos](../articles/traffic-manager/traffic-manager-endpoints.md) para agregar la aplicación web como un extremo en el perfil de administrador de tráfico.

> [AZURE.NOTE] Si su aplicación web no aparece cuando se agrega un extremo, compruebe que está configurado para el modo de plan de servicio **estándar** de la aplicación. Debe utilizar el modo **estándar** para las aplicaciones web para poder trabajar con el Administrador de tráfico.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

1. En la ficha **Aplicaciones Web** , haga clic en el nombre de su aplicación web, seleccione **configuración**y, a continuación, seleccione los **dominios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. En la hoja de **dominios personalizados** , haga clic en **Agregar un nombre de host**.
    
1. Utilice los cuadros de texto de **nombre de host** para especificar el nombre de dominio Traffic Manager para asociar a esta aplicación web.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Haga clic en **Validar** para guardar la configuración de nombre de dominio.

7.  Al hacer clic en **Validar** Azure se iniciará el flujo de trabajo de comprobación de dominios. Comprueba la titularidad del dominio así como Hostname disponibilidad e informe de éxito o error detallado con orienteción preceptiva sobre cómo solucionar el error.    

8.  Tras una validación correcta **hostname agregar** botón estará activo y usted podrá asignar el nombre del host. Ahora vaya a su nombre de dominio personalizado en un explorador. Ahora debería ver la ejecución de la aplicación utilizando su nombre de dominio personalizado. 

    Una vez concluida la configuración, el nombre de dominio personalizado aparecerá en la sección de **nombres de dominio** de su aplicación web.

En este punto, puede escribir el nombre de dominio de Traffic Manager en el explorador y ver que se tarda con éxito a la aplicación web.
