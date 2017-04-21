Generador de datos es un servicio multiempresa que tiene los siguientes límites predeterminados en su lugar para asegurarse de que las suscripciones de cliente están protegidas frente a cargas de trabajo de los demás. Muchos de los límites pueden provocar fácilmente para su suscripción hasta el límite máximo por ponerse en contacto con soporte técnico. 

**Recurso** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
generadores de datos con una suscripción de Azure | 50 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
tuberías dentro de un generador de datos | 2500 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
conjuntos de datos dentro de un generador de datos | 5000 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
sectores simultáneas por cada conjunto de datos | 10 | 10
bytes por cada objeto de canalización de objetos <sup>1</sup> | 200 KB | 2000 KB
bytes por cada objeto de conjunto de datos y objetos de servicio vinculada <sup>1</sup> | 100 KB | 2000 KB
Núcleos de clúster a petición HDInsight dentro de una suscripción <sup>2</sup> | 48 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Movimiento de datos de nube unidad <sup>3</sup> | 8 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Número de reintentos para ejecuciones de actividad de canalización | 1000 | MaxInt (32 bits)

<sup>1</sup> canalización, conjunto de datos y objetos de servicio vinculadas representan una agrupación lógica de su carga de trabajo. Límites para estos objetos no relacionan con la cantidad de datos, puede mover y procesar con el servicio de generador de datos de Azure. Factoría de datos está diseñado para escalar a manejar petabytes de datos.

<sup>2</sup> núcleos HDInsight bajo demanda se asignan fuera de la suscripción que contiene el generador de datos. Como resultado, el límite anterior es el generador de datos exige el límite del núcleo para núcleos de HDInsight bajo demanda y es diferente del límite de núcleo asociado con la suscripción de Azure.

<sup>3</sup> unidad de movimiento de datos de nube (DMU) se está utilizando en una operación de copia de nube a la nube. Es una medida que representa la potencia (una combinación de asignación de recursos de red, memoria y CPU) de una sola unidad en el generador de datos. Puede conseguir un mayor rendimiento de la copia al aprovechar más DMUs en algunos escenarios. Consulte la sección [unidades de movimiento de datos de nube](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) en detalles.

**Recurso** | **Límite inferior predeterminado** | **Límite mínimo**
-------- | ------------------- | -------------
Intervalo de programación | 15 minutos | 15 minutos
Intervalo entre reintentos | 1 segundo | 1 segundo
Valor de tiempo de espera de reintento | 1 segundo | 1 segundo


### <a name="web-service-call-limits"></a>Límites de llamada de servicio Web

Administrador de recursos del Azure tiene límites para las llamadas a la API. Puede hacer llamadas a la API a una velocidad dentro de los [límites de la API del Administrador de recursos de Azure](../azure-subscription-service-limits.md#resource-group-limits). 


