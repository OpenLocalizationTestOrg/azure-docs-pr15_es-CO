Recurso|Límite predeterminado|Límite máximo
---|---|---
Cuentas de Azure Media Services (AMS) en una sola suscripción||25
Activos por cuenta de AMS||1.000.000<sup>1</sup>
Tareas encadenadas por trabajo||30
Activos por tarea||50
Activos por trabajo||100
Trabajos por cuenta de AMS ||50.000<sup>2</sup>
Localizadores únicos asociados a un activo al mismo tiempo||5<sup>4</sup>
Canales en vivo por cuenta de AMS </p></td>|5</p></td>|N /<sup>1</sup>
Programas en estado detenido por canal </p></td>|50</p></td>|N /<sup>1</sup>
Programas en ejecución estado por canal </p></td>|3</p></td>|3
Transmisión de extremos en el estado de cuenta de AMS</p></td>|2</p></td>|N /<sup>1</sup>
Unidades de transmisión por secuencias de extremo </p></td>|10 </p></td>|N /<sup>1</sup>
Unidades de codificación por cuenta de AMS </p></td>|25</p></td>|N /<sup>1</sup>
Cuentas de almacenamiento | |1.000<sup>5</sup>
Directivas de || 1.000.000<sup>6</sup>

<sup>1</sup> puede solicitar para actualizar los límites de esta cuota abriendo un ticket de soporte. No cree más cuentas de AMS para aumentar los límites, en su lugar enviar un ticket de soporte.

<sup>2</sup> este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye trabajos eliminados. Puede eliminar los trabajos antiguos mediante **IJob.Delete** o la solicitud HTTP **DELETE** .

<sup>3</sup> al realizar una solicitud a las entidades de trabajo de lista, se devuelve un máximo de 1.000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede utilizar arriba o saltar como se describe en [Opciones de consulta de sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, utilizar soluciones de administración de derechos digitales (DRM).

<sup>5</sup> cuentas de almacenamiento deben ser de la misma suscripción de Azure.

<sup>6</sup> hay un límite de 1.000.000 políticas para diferentes políticas de AMS (por ejemplo, para la directiva de localizador o ContentKeyAuthorizationPolicy). Debe utilizar el mismo identificador de directiva si está utilizando siempre los mismos días / permisos de acceso / etcetera.
