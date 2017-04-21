1. Iniciar sesión en el [Portal de Azure].

2. Haga clic en **+ nueva** > **Web + Mobile** > **Mobile App**, proporcione un nombre para la aplicación Mobile de back-end.

3. Para el **Grupo de recursos**, seleccione un grupo de recursos existente o crear uno nuevo (con el mismo nombre que la aplicación). 
 
    Puede seleccionar otro plan de servicio de la aplicación o crear uno nuevo. Para obtener más información acerca de los servicios de aplicación de planes y cómo crear un nuevo plan en diferentes precios de nivel y en la ubicación deseada, consulte [Introducción al servicio de la aplicación de Azure planes exhaustiva](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para el **plan de servicio de la aplicación**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar un plan diferente o [crear uno nuevo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Configuración del plan de servicio de la aplicación determina la [ubicación, características, costos y los recursos computacionales](https://azure.microsoft.com/pricing/details/app-service/) asociados a su aplicación. 

    Después de decidir el plan, haga clic en **crear**. Esto crea la aplicación Mobile de back-end. 
    
6. En la hoja de **configuración** para el back-end de la aplicación móvil nuevo, haga clic en **Inicio rápido** > la plataforma de la aplicación cliente > **Conectar una base de datos**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. En la hoja de **Agregar conexión de datos** , haga clic en **Base de datos de SQL** > **crear una nueva base de datos**, escriba el **nombre**de la base de datos, elija un nivel de precios, y después haga clic en **servidor**.  Puede volver a esta nueva base de datos. Si ya tiene una base de datos en la misma ubicación, puede elegir **utilizar una base de datos existente**en su lugar. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos de ancho de banda y latencia mayor.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. En el **nuevo servidor** blade, escriba un nombre de servidor único en el campo **nombre de servidor** , proporcione un inicio de sesión y una contraseña, compruebe los **Servicios de azure permitir acceder a servidor**y haga clic en **Aceptar**. Esto crea la nueva base de datos.

9. En la hoja de **Agregar conexión de datos** , haga clic en la **cadena de conexión**, escriba los valores de inicio de sesión y la contraseña para la base de datos y haga clic en **Aceptar**. Espere unos minutos para la base de datos se implementó correctamente antes de continuar.

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/
