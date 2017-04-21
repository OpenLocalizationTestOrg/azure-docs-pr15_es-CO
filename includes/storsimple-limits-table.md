<!--author=alkohli last changed: 12/15/15-->

| Identificador de límite | Límite de | Comentarios |
|----------------- | ------|--------- |
| Número máximo de credenciales de la cuenta de almacenamiento | 64 | |
| Número máximo de contenedores de volumen | 64 | |
| Número máximo de volúmenes | 255 | |
| Número máximo de programaciones por plantilla de ancho de banda | 168 | Programación para cada hora, cada día de la semana (24 * 7). |
| Tamaño máximo de un volumen en niveles en dispositivos físicos | 64 TB para 8100 y 8600 | 8100 y 8600 son dispositivos físicos. |
| Tamaño máximo de un volumen en niveles en dispositivos virtuales en Azure | 30 TB para 8010 <br></br> 64 TB para 8020 | 8010 y 8020 son dispositivos virtuales en Azure que utilizan el almacenamiento estándar y Premium respectivamente. |
| Tamaño máximo de un volumen local anclado en dispositivos físicos | 9 TB para 8100 <br></br> 24 TB para 8600 | 8100 y 8600 son dispositivos físicos. |
| Número máximo de conexiones iSCSI | 512 | |
| Número máximo de conexiones iSCSI de iniciadores | 512 | |
| Número máximo de registros de control de acceso por dispositivo | 64 | |
| Número máximo de volúmenes por la política de backup | 24 | |
| Número máximo de copias de seguridad guardadas por la política de backup | 64 | |
| Número máximo de programaciones por la política de backup | 10 | |
| Número máximo de copias instantáneas de cualquier tipo que se pueden retener por volumen | 256 | Esto incluye copias instantáneas locales y copias instantáneas de la nube. |
| Número máximo de instantáneas que pueden estar presentes en cualquier dispositivo | 10.000 | |
| Número máximo de volúmenes que se pueden procesar en paralelo para backup, restore o clonar | 16 |<ul><li>Si hay más de 16 volúmenes, sean procesados secuencialmente a medida que las ranuras de procesamiento disponibles.</li><li>No se puede realizar nuevas copias de seguridad de un clonado o un volumen en niveles restaurado hasta que finalice la operación. Sin embargo, para un volumen local, se permiten las copias de seguridad después de que el volumen está en línea.</li></ul>|
| Restaurar y clonar el tiempo de recuperación para los volúmenes de información en niveles | < 2 minutos | <ul><li>El volumen estará disponible dentro de 2 minutos de la operación de restauración o clon, independientemente del tamaño del volumen.</li><li>El rendimiento de volumen inicialmente puede ser más lento de lo normal, como la mayoría de los datos y metadatos todavía se encuentra en la nube. El rendimiento puede aumentar como flujos de datos en la nube para el dispositivo de StorSimple.</li><li>El tiempo total de descarga de metadatos depende del tamaño de volumen asignado. Metadatos automáticamente entra en el dispositivo en el fondo a una velocidad de 5 minutos por TB de datos de volumen asignado. Esta velocidad puede verse afectada por el ancho de banda de Internet a la nube.</li><li>La restauración o la operación de clonación es completa una vez todos los metadatos en el dispositivo.</li><li>No se puede realizar operaciones de backup hasta la restauración o está totalmente completa la operación de clonación.|
| Restaurar recuperar tiempo para volúmenes fijados localmente | < 2 minutos | <ul><li>El volumen estará disponible dentro de 2 minutos, la operación de restauración, independientemente del tamaño del volumen.</li><li>El rendimiento de volumen inicialmente puede ser más lento de lo normal, como la mayoría de los datos y metadatos todavía se encuentra en la nube. El rendimiento puede aumentar como flujos de datos en la nube para el dispositivo de StorSimple.</li><li>El tiempo total de descarga de metadatos depende del tamaño de volumen asignado. Metadatos automáticamente entra en el dispositivo en el fondo a una velocidad de 5 minutos por TB de datos de volumen asignado. Esta velocidad puede verse afectada por el ancho de banda de Internet a la nube.</li><li>A diferencia de los volúmenes en niveles, en el caso de volúmenes fijados localmente, los datos de volumen también se descargan localmente en el dispositivo. La operación de restauración es completa cuando todos los datos del volumen ha sido autorizada por el dispositivo.</li><li>Las operaciones de restauración pueden ser largas y el tiempo total para completar la restauración depende del tamaño del volumen local con provisioning, el ancho de banda de Internet y los datos existentes en el dispositivo. Se permiten las operaciones de backup en el volumen fijado localmente mientras está en curso la operación de restauración.|
| Disponibilidad de restauración fino | Última conmutación por error | |
| Rendimiento de lectura y escritura de número máximo de clientes (cuando se atiende desde el nivel SSD) * | 920/720 MB/s con una sola 10GbE de interfaz de red | Hasta 2 x con MPIO y dos interfaces de red. |
| Rendimiento de lectura y escritura de número máximo de clientes (cuando se atiende desde el nivel de unidad de disco duro) * | 120/250 MB/s |
| Rendimiento de lectura y escritura de número máximo de clientes (cuando se atiende desde el nivel de nube) * | 11/41 MB/s | Rendimiento de lectura depende de clientes generar y mantener suficiente profundidad de la cola de i/OS. |

& #42; Se midió el rendimiento máximo por tipo de i/OS con la lectura del 100 por ciento y escenarios de escritura del 100%. El rendimiento real puede ser inferior y depende de entrada-salida mezclar y condiciones de la red.
