### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure; puede crear una [cuenta gratuita](https://azure.microsoft.com/free)
- Una [Base de datos de SQL Azure](../articles/sql-database/sql-database-get-started.md) con su información de conexión, incluyendo el nombre del servidor, nombre de la base de datos y nombre de usuario y contraseña. Esta información se incluye en la cadena de conexión de base de datos SQL:
  
    Servidor = tcp:*yoursqlservername*. database.windows.net,1433;Initial catálogo =*yourqldbname*; Persist Security Info = False; Id. de usuario = {su_nombre_de_usuario}; Password = {su_contraseña}; MultipleActiveResultSets = False; Cifrar = True; TrustServerCertificate = False; Connection Timeout = 30;

    Más información acerca de [Bases de datos de SQL Azure](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Cuando se crea una base de datos de SQL Azure, también puede crear las bases de datos de ejemplo incluidas con SQL. 



Antes de utilizar la base de datos de SQL Azure en la lógica de la aplicación, conectarse a la base de datos de SQL. Puede hacerlo fácilmente dentro de la lógica de la aplicación en el portal de Azure.  

Conectarse a la base de datos de SQL Azure mediante los pasos siguientes:  

1. Crear una lógica de la aplicación. En el Diseñador de aplicaciones de lógica, agregar un desencadenador y, a continuación, agregar una acción. Seleccione **Mostrar Microsoft API administradas** en la lista desplegable y, a continuación, escriba "sql" en el cuadro de búsqueda. Seleccione una de las acciones:  

    ![Paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Si no ha creado ninguna conexión a la base de datos de SQL, se le pedirá que especifique los detalles de conexión:  

    ![Paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 

3. Especifique los detalles de la base de datos de SQL. Propiedades con un asterisco son necesarios.

    | Propiedad | Detalles |
|---|---|
| Conectar a través de la puerta de enlace | Dejar esta opción desactivada. Se utiliza al conectarse a una de SQL Server local. |
| Nombre de conexión * | Escriba un nombre para la conexión. | 
| Nombre de SQL Server * | Escriba el nombre del servidor; que es similar a *servername.database.windows.net*. El nombre del servidor aparece en las propiedades de la base de datos de SQL en el portal de Azure y también aparece en la cadena de conexión. | 
| Nombre de base de datos SQL * | Escriba el nombre que dio a la base de datos de SQL. Esto se muestra en las propiedades de la base de datos de SQL en la cadena de conexión: Initial Catalog =*yoursqldbname*. | 
| Nombre de usuario * | Escriba el nombre de usuario que creó cuando se creó la base de datos de SQL. Esto se muestra en las propiedades de la base de datos de SQL en el portal de Azure. | 
| Contraseña * | Escriba la contraseña que creó cuando se creó la base de datos de SQL. | 

    Estas credenciales se utilizan para autorizar la lógica de la aplicación para conectarse y tener acceso a los datos SQL. Cuando haya finalizado, los detalles de conexión parecen al siguiente:  

    ![Paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 

4. Seleccione **crear**. 

5. Observe que la conexión se ha creado. Ahora, continúe con el resto de los pasos en la lógica de la aplicación: 

    ![Paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/table.png)