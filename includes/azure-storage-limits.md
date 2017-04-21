Recurso|Límite predeterminado
---|---
Número de cuentas de almacenamiento de información por suscripción|200<sup>1</sup>
TB por cuenta de almacenamiento|500 TB
Número máximo de contenedores de blob, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento|Límite sólo es la capacidad de 500 TB de almacenamiento cuenta
Tamaño máximo de un contenedor de blob único, tabla o cola|500 TB
Máximo número de bloques de un blob de bloque o anexar blob|50.000
Tamaño máximo de un bloque en un blob de bloque o anexar blob|4 MB
Tamaño máximo de un blob de bloque o anexar blob|50.000 x 4 MB (aprox. 195 GB) 
Tamaño máximo de un blob de página |1 TB
Tamaño máximo de una entidad de la tabla|1 MB
Número máximo de propiedades en una entidad de la tabla|252
Tamaño máximo de un mensaje de una cola|64 KB
Tamaño máximo de un archivo compartido|5 TB
Tamaño máximo de un archivo en un recurso compartido de archivos|1 TB
Número máximo de archivos en un recurso compartido de archivos|Límite sólo es la capacidad total de 5 TB de recurso compartido de archivos
Máximo 8 KB IOPS por acción|1000
Número máximo de archivos en un recurso compartido de archivos|Límite sólo es la capacidad total de 5 TB de recurso compartido de archivos
Número máximo de contenedores de blob, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento|Límite sólo es la capacidad de 500 TB de almacenamiento cuenta
Número máximo de directivas de acceso almacenadas por contenedor, recurso compartido de archivo, tabla o cola|5
Solicitud de tasa total (suponiendo que el tamaño del objeto de 1KB) por cuenta de almacenamiento|Hasta 20.000 IOPS, entidades por segundo o mensajes por segundo
Rendimiento de destino para blob único|Hasta 60 MB por segunda, o hasta 500 solicitudes por segundo
Rendimiento de destino para la única cola (mensajes de 1 KB)|Hasta 2000 mensajes por segundo
Rendimiento de destino para la partición de tabla única (entidades de 1 KB)|Hasta 2000 entidades por segundo
Rendimiento de destino para el recurso compartido de archivo único|Hasta 60 MB por segundo
Max entrada<sup>2</sup> por cuenta de almacenamiento (nosotros las regiones)|10 Gbps si GRS/ZRS<sup>3</sup> habilitada, 20 Gbps para LRS
Max salida<sup>2</sup> por cuenta de almacenamiento (nosotros las regiones)|20 Gbps si RA-GRS/GRS/ZRS<sup>3</sup> habilitada, 30 Gbps para LRS
Max entrada<sup>2</sup> por cuenta de almacenamiento (Europea y regiones de Asia)|5 Gbps si GRS/ZRS<sup>3</sup> habilitada, 10 Gbps para LRS
Max salida<sup>2</sup> por cuenta de almacenamiento (Europea y regiones de Asia)|10 Gbps si RA-GRS/GRS/ZRS<sup>3</sup> habilitada, 15 Gbps para LRS

<sup>1</sup> Esto incluye cuentas de almacenamiento estándar y Premium. Si necesita más de 200 cuentas de almacenamiento de información, realizar una solicitud a través del [Soporte de Azure](https://azure.microsoft.com/support/faq/). El equipo de almacenamiento de Azure revisará su caso de negocio y podrá aprobar hasta 250 cuentas de almacenamiento. 

<sup>2</sup> *Entrada* hace referencia a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.  

<sup>3</sup> Opciones de replicación de almacenamiento de Azure incluyen:

- **RA-GRS**: almacenamiento redundante geo acceso de lectura. Si está habilitada la RA GRS, destinos de salida para la ubicación secundaria son idénticas a las de la ubicación principal.
- **GRS**: almacenamiento Geo redundante. 
- **ZRS**: almacenamiento de información redundantes para la zona. Disponible sólo para los objetos binarios de bloque. 
- **LRS**: almacenamiento redundante local. 

