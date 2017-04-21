Puede crear varios servicios dentro de una suscripción, cada uno configurado en un nivel específico, limitado sólo por el número de servicios permitidos en cada nivel. Por ejemplo, podría crear hasta 12 servicios en el nivel básico y otro 12 servicios en el nivel de S1 dentro de la misma suscripción. Para obtener más información sobre niveles, vea [Elegir un SKU o niveles para búsqueda de Azure](../articles/search/search-sku-tier.md).

Límites máximo servicio posible elevar a petición. Si necesita más servicios dentro de la misma suscripción, póngase en contacto con el soporte de Azure.

Recurso|Gratis|Básico|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Servicios máximo |1 |12 |12  |6 |6 |6 
Escala máxima en SU <sup>2</sup>|N / <sup>3</sup>|SU 3 <sup>4</sup> |SU 36|SU 36|SU 36|SU 12, 3 SU <sup>5</sup>

<sup>1</sup> S3 HD no admiten [indizadores](../articles/search/search-indexer-overview.md) en este momento. 

<sup>2</sup> unidades de búsqueda (SU) son unidades facturables por servicio, que se asigna como una *réplica* o una *partición*. Necesita ambos recursos de almacenamiento de información, indexación y operaciones de consulta. Para obtener más información acerca de las combinaciones válidas que permanecen debajo de los límites máximos, vea [niveles de escala de recursos para cargas de trabajo de consulta e índice](../articles/search/search-capacity-planning.md). 

<sup>3</sup> libre se basa en los recursos compartidos utilizados por varios suscriptores. En este nivel, no hay ningún recurso dedicado para un suscriptor individual. Por este motivo, la escala máxima está marcada como no aplicable.

<sup>4</sup> basic tiene una partición fija. En este nivel, SUs adicionales se utilizan para asignar más réplicas para cargas de trabajo de consulta mayor.

<sup>5</sup> HD S3 tiene una estructura de asignación diferentes en términos de combinaciones permitidas. Para las réplicas, puede tener un máximo de 12. Para las particiones, el máximo es 3.




