Una vez se ha propagado los registros del nombre de dominio, debe asociarlos a su aplicación Web. Utilice los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [AZURE.NOTE] Puede tardar algún tiempo para registros TXT creado en los pasos anteriores para propagar a través del sistema DNS. No se puede agregar el nombre de dominio a la aplicación web hasta que se haya propagado el registro TXT. Si está utilizando un registro, no puede agregar el nombre de un registro de dominio a la aplicación web hasta que se haya propagado el registro TXT creado en el paso anterior.
>
> Puede utilizar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro TXT está disponible.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En la ficha **Aplicaciones Web** , haga clic en el nombre de su aplicación web y, a continuación, seleccione los **dominios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. En la hoja de **dominios personalizados** , haga clic en **Agregar un nombre de host**.
    
4. Utilice los cuadros de texto de **nombre de host** para especificar los nombres de dominio para asociar a esta aplicación web.

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Haga clic en **Validar**.

7.  Al hacer clic en **Validar** Azure se iniciará el flujo de trabajo de comprobación de dominios. Comprueba la titularidad del dominio así como Hostname disponibilidad e informe de éxito o error detallado con orienteción preceptiva sobre cómo solucionar el error.    

En este punto, puede escribir el nombre de dominio personalizado en el explorador y ver que se tarda con éxito a la aplicación web.
