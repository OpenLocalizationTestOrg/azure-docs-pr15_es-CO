## <a name="dynamic-service-plan"></a>Plan de servicio dinámico

En el Plan de servicio dinámico, las aplicaciones de la función se asignará a una instancia de la aplicación de la función. Si necesitan más instancias se agregan dinámicamente.
Esas instancias pueden abarcar varios recursos, realizar el máximo partido a la infraestructura disponible de Azure. Además, las funciones se ejecutarán en paralelo reduce el tiempo total necesario para procesar las solicitudes. Tiempo de ejecución de cada función se suman, en segundos y se agregan en función de la aplicación contenedora. Con costo gobernada por el número de instancias, su tamaño de memoria y tiempo de ejecución total medido en segundos de Gigabyte. Esto es una opción excelente si sus necesidades informáticas son intermitentes o las horas de trabajo tienden a ser muy breve, ya que permite solo paga por recursos de cálculo cuando en realidad están en uso.   

### <a name="memory-tier"></a>Nivel de memoria

Dependiendo de su función necesita puede seleccionar la cantidad de memoria necesaria para ejecutarlas en la función de la aplicación (contenedor de funciones).
Varían las opciones de tamaño de memoria de **128 MB a 1536 MB**. El tamaño de memoria seleccionada corresponde al conjunto de trabajo necesarios para todas las funciones que forman parte de la función de la aplicación. Si el código requiere más memoria que el tamaño seleccionado, se cerrará la instancia de la aplicación de la función debido a la falta de memoria disponible.

### <a name="scaling"></a>Ajuste de escala

La plataforma de Azure funciones evaluará el tráfico necesidades, basándose en los desencadenadores configurados, decidir cuándo escalar hacia arriba o hacia abajo. La granularidad de la escala es la aplicación de la función. En este caso el escalado significa agregar más instancias de una función de la aplicación. Inversamente como tráfico deja de funcionar, las instancias de la aplicación de la función son deshabilitado-eventual reducción de la escala a cero cuando no se están ejecutando.  

### <a name="resource-consumption-and-billing"></a>Consumo de recursos y facturación

En el dinámico asignación de recursos de modo se realiza de forma diferente que el plan de servicio de la aplicación estándar, por lo tanto, el modelo de consumo también es diferente, lo que permite un modelo "pague por usar". Consumo se presenta por la función de la aplicación, sólo para cuando se ejecuta el código de tiempo.  
Se calcula multiplicando el tamaño de la memoria (en GB) por la cantidad total de tiempo de ejecución (en segundos) para todas las funciones que se ejecuta dentro de la aplicación de esa función. La unidad de consumo será **s-GB (Gigabyte segundos)**.