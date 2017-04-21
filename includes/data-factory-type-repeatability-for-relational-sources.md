## <a name="repeatability-during-copy"></a>Repetibilidad durante la copia

Cuando copie datos desde y hacia los almacenes relacionales, necesitará tener repetibilidad en cuenta para evitar resultados no deseados. 

Un sector puede volver a ejecutarse automáticamente en la factoría de datos de Azure según la directiva de reintento especificada. Recomendamos que establezca una directiva de reintento para protegerse contra errores transitorios. Por lo tanto, repetibilidad es un aspecto importante a cuidar durante el movimiento de datos. 

**Como origen:**

> [AZURE.NOTE] Los ejemplos siguientes son para SQL Azure, pero son aplicables a cualquier almacén de datos que es compatible con conjuntos de datos rectangular. Puede que tenga que ajustar el **tipo** de origen y la propiedad de **consulta** (por ejemplo: consulta en lugar de sqlReaderQuery) para los datos de almacén.   

Normalmente, al leer de almacenes relacionales, ¿desea leer sólo los datos correspondientes a dicho segmento. Una forma de hacerlo sería mediante el uso de las variables WindowStart y WindowEnd disponibles en la factoría de datos de Azure. Lea acerca de las variables y funciones en la factoría de datos de Azure aquí en el artículo de [la programación y ejecución](../articles/data-factory/data-factory-scheduling-and-execution.md) . Ejemplo: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Esta consulta lee datos de 'Mitabla' que se encuentra en el intervalo de duración del sector. Volver a ejecutar de este sector garantizará también siempre este comportamiento. 

En otros casos, puede leer toda la tabla (suponga que para una vez sólo mover) y pueden definir la sqlReaderQuery como sigue:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
