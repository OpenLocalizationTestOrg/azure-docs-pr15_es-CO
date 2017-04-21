##<a name="create-client"></a>Crear una conexión de cliente

Crear una conexión de cliente mediante la creación de un `WindowsAzure.MobileServiceClient` objeto.  Reemplazar `appUrl` con la dirección URL de su aplicación móvil.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Trabajar con tablas

Acceso o actualización de datos, cree una referencia a la tabla de back-end. Reemplazar `tableName` con el nombre de la tabla

```
var table = client.getTable(tableName);
```

Una vez que tenga una referencia a la tabla, se puede trabajar con la tabla:

* [Una tabla de consulta](#querying)
  * [Filtrado de datos](#table-filter)
  * [Paginación de datos](#table-paging)
  * [Ordenar datos](#sorting-data)
* [Insertar datos](#inserting)
* [Modificación de los datos](#modifying)
* [Eliminación de datos](#deleting)

###<a name="querying"></a>Cómo: consultar una referencia de tabla

Una vez que tenga una referencia a la tabla, se puede utilizar para consultar datos en el servidor.  Las consultas se realizan en un lenguaje de "LINQ".
Para devolver todos los datos de la tabla, utilice lo siguiente:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Se llama a la función de éxito con los resultados.   No utilice `for (var i in results)` para el éxito funcione como que establece una iteración entre la información que se incluye en los resultados cuando otras funciones de consulta (como `.includeTotalCount()`) se utilizan.

Para obtener más información sobre la sintaxis de la consulta, consulte el [documentación del objeto de consulta].

####<a name="table-filter"></a>Filtrado de datos en el servidor

Puede utilizar un `where` cláusula en la referencia de tabla:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

También puede utilizar una función que filtra el objeto.  En este caso el `this` variable está asignada al objeto actual que se está filtrando.  Es funcionalmente equivalente al ejemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Paginación de datos

Utilizar los métodos take() y skip().  Por ejemplo, si desea dividir la tabla en la fila 100 registros:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

El `.includeTotalCount()` método se utiliza para agregar un campo totalCount para el objeto results.  El campo totalCount se rellena con el número total de registros que se devolverá si no se utiliza paginación.

A continuación, puede utilizar la variable de páginas y algunos botones de la interfaz de usuario para proporcionar una lista de páginas; Utilice loadPage() para cargar los nuevos registros para cada página.  Debe implementar a algún tipo de almacenamiento en caché para agilizar el acceso a registros que ya se han cargado.


####<a name="sorting-data"></a>Cómo: devolver datos ordenados

Utilice los métodos de consulta .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obtener más información sobre el objeto de consulta, consulte [documentación de objeto de consulta].

###<a name="inserting"></a>Cómo: insertar datos

Crear un objeto de JavaScript con la fecha adecuada y llamar de forma asincrónica table.insert():

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

En la inserción correcta, se devuelve el elemento insertado con los campos adicionales que son necesarios para las operaciones de sincronización.  Debe actualizar su propia caché de esta información para posteriores actualizaciones.

Tenga en cuenta que el SDK de Azure Mobile Apps Node.js Server admite esquema dinámico para fines de desarrollo.
En el caso de esquema dinámico, se actualiza el esquema de la tabla sobre la marcha, lo que le permite agregar columnas a la tabla especificándolas en una inserción o la operación de actualización.  Le recomendamos que desactive el esquema dinámico antes de mover la aplicación a la producción.

###<a name="modifying"></a>Cómo: modificar datos

De forma similar al método .insert(), debe crear un objeto de la actualización y, a continuación, llamar a .update().  El objeto de actualización debe contener el ID del registro que se actualice, esto se obtiene cuando se lee el registro o cuando se llama a .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Cómo: eliminar datos

Llame al método .del() para eliminar un registro.  Pase el identificador de una referencia de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
