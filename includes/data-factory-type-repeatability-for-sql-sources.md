## <a name="repeatability-during-copy"></a>Repetibilidad durante la copia

Cuando copias datos de SQL Server y SQL Azure de otros datos almacena uno necesita tener repetibilidad en cuenta para evitar resultados no deseados. 

Al copiar datos a la base de datos de Azure SQL o SQL Server, será actividad de copia predeterminada ANEXAR el conjunto de datos a la tabla de receptor de forma predeterminada. Por ejemplo, al copiar datos de un origen de archivo CSV (datos de valores separados por comas) que contiene dos registros a la base de datos de Azure SQL o SQL Server, este es el aspecto de la tabla:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Supongamos que encontró errores en el archivo de origen y actualiza la cantidad del tubo hacia abajo de 2 a 4 en el archivo de origen. Si vuelve a ejecutar la rebanada de datos durante ese período, encontrará dos nuevos registros anexados a la base de datos de Azure SQL o SQL Server. El a continuación supone que ninguna de las columnas de la tabla tiene la restricción de clave principal.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Para evitar este problema, debe especificar la semántica UPSERT aprovechando uno de los por debajo de 2 mecanismos que se exponen a continuación.

> [AZURE.NOTE] Un segmento se puede volver a ejecutar automáticamente en la factoría de datos de Azure según la directiva de reintento especificada.

### <a name="mechanism-1"></a>Mecanismo 1

Puede aprovechar la propiedad **sqlWriterCleanupScript** para llevar a cabo acciones de limpieza en primer lugar cuando se ejecuta un sector. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

La secuencia de comandos de limpieza se ejecutaría primero durante la copia de un sector determinado que eliminará los datos de la tabla SQL correspondiente a dicho segmento. La actividad posteriormente insertará los datos en la tabla de SQL. 

Si el sector está ahora volver a ejecutar, a continuación, encontrará que la cantidad se actualiza como se desea.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Supongamos que el registro de la arandela plana se quitará el csv original. A continuación, volver a ejecutar el sector produciría el siguiente resultado: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Nada nuevo tenía que hacer. La actividad de copia ejecutó la secuencia de comandos de limpieza para eliminar los datos correspondientes para ese sector. Después de leer la entrada, desde el archivo csv (que, a continuación, encuentra sólo 1 registro) y se inserta en la tabla. 

### <a name="mechanism-2"></a>Mecanismo 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName no se admite para el almacén de datos de SQL Azure en este momento. 

Otro mecanismo para lograr la repetibilidad es teniendo una columna dedicada (**sliceIdentifierColumnName**) en la tabla de destino. Esta columna se utilizará por la factoría de datos de Azure para garantizar que el origen y el destino estén sincronizadas. Este enfoque funciona cuando hay flexibilidad para cambiar o definir el esquema de la tabla SQL de destino. 

Esta columna se utilizaría por factoría de datos de Azure para propósitos de repetibilidad y en el proceso de la factoría de datos de Azure no realizar cambios de esquema en la tabla. Forma de utilizar este enfoque:

1.  Defina una columna de tipo binario (32) en la tabla SQL de destino. No debe haber ninguna restricción en esta columna. Vamos a nombrar esta columna como 'ColumnForADFuseOnly' para este ejemplo.
2.  Utilizarlo en la actividad de copia como sigue:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Generador de datos Azure llenará esta columna según su necesidad de garantizar que el origen y el destino estén sincronizadas. Los valores de esta columna no deben utilizarse fuera de este contexto por parte del usuario. 

Similar al mecanismo de 1, actividad de copia será automáticamente limpiar primero los datos para el sector determinado de la tabla SQL de destino y vuelva a ejecutar la actividad de copia normalmente para insertar los datos de origen a destino para ese sector. 
