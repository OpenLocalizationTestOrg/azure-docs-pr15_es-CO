La tabla siguiente describe cada una de las principales cuotas, límites, valores predeterminados y aceleradores del programador de Azure.

|Recurso|Descripción del límite|
|---|---|
|**Tamaño de trabajo**|El tamaño máximo de trabajo es 16K. Si un puesto o una revisión da como resultado un trabajo supera estos límites, se devuelve un código de estado 400 Solicitud incorrecta.|
|**Tamaño de dirección URL de la solicitud**|Tamaño máximo de la dirección URL de la solicitud es de 2048 caracteres.|
|**Tamaño del encabezado agregado**|Tamaño máximo de encabezado agregado es de 4096 caracteres.|
|**Recuento de encabezado**|Recuento cabecera máximo es 50 encabezados.|
|**Tamaño del cuerpo**|Tamaño máximo del cuerpo es de 8192 caracteres.|
|**Intervalo de periodicidad**|Intervalo de periodicidad máximo es de 18 meses.|
|**Tiempo a la hora de inicio**|Máximo "tiempo a la hora de inicio" es 18 meses.|
|**Historial de trabajos**|Cuerpo de respuesta máximo almacenado en el historial de trabajos es de 2048 bytes.|
|**Frecuencia**|La cuota de frecuencia máxima predeterminada es 1 hora en una colección de trabajo libre y 1 minuto en una colección de trabajo estándar. La frecuencia máxima es configurable en una colección de trabajo sea inferior al máximo. Todos los trabajos de la colección de trabajo están limitados el valor establecido en el conjunto de trabajo. Si se intenta crear un trabajo con una frecuencia superior a la frecuencia máxima en la colección de trabajo solicitud fallará con un código de estado 409 conflicto.|
|**Trabajos**|La cuota de trabajos máxima predeterminada es 5 trabajos en una colección de trabajo libre y 50 trabajos en una colección de trabajo estándar. El número máximo de trabajos es configurable en un conjunto de trabajo. Todos los trabajos de la colección de trabajo están limitados el valor establecido en el conjunto de trabajo. Si intenta crear más trabajos que la cuota máxima de trabajos, la solicitud falla con un código de estado 409 conflicto.|
|**Retención de historial del trabajo**|Historial de trabajos se conserva hasta 2 meses o hasta la última 1000 ejecuciones.|
|**Retención del trabajo completado y con errores**|Trabajos completados y con errores se conservan durante 60 días.|
|**Tiempo de espera**|No hay un tiempo de espera de solicitud (no configurable) estática de 60 segundos para acciones HTTP. Para las operaciones de ejecución más largo, siga los protocolos HTTP asincrónicos; Por ejemplo, devolver un 202 inmediatamente pero continuar trabajando en segundo plano.|
