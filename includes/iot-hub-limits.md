La tabla siguiente muestra los límites de los niveles de servicio diferentes (S1, S2, S3, F1). Para obtener información acerca del costo de cada *unidad* en cada nivel, consulte [Precios de concentrador de IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | Estándar de s1 | S2 estándar | Estándar de S3 | F1 gratis |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensajes por día | 400.000 | 6.000.000   | 300,000,000 | 8.000   |
| Máximo de unidades | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Si prevé utilizar unidades de más de 200 con un concentrador de nivel S1 o S2 o S3, póngase en contacto con el soporte técnico de Microsoft.

La tabla siguiente enumeran los límites que se aplican a recursos IoT concentrador:

| Recurso | Límite de |
| -------- | ----- |
| Máximo pago IoT concentradores por suscripción de Azure | 10 |
| Concentradores de IoT libres máximo por suscripción de Azure | 1 |
| Número máximo de las identidades del dispositivo<br/>  devuelto en una única llamada | 1000 |
| Concentrador de IoT mensaje máximo de retención de mensajes del dispositivo a la nube | 7 días |
| Tamaño máximo de mensaje del dispositivo a la nube | 256 KB |
| Tamaño máximo de lote de dispositivo a la nube | 256 KB |
| Número máximo de mensajes en el lote de dispositivo a la nube | 500 |
| Tamaño máximo de mensaje del dispositivo de nube | 64 KB |
| TTL máximo para los mensajes de la nube a dispositivo | 2 días |
| Recuento máximo de entrega para el dispositivo de nube <br/> mensajes | 100 |
| Recuento máximo de entrega para los mensajes de comentarios <br/> en respuesta a un mensaje de nube para dispositivos | 100 |
| TTL máximo para los mensajes de comentarios en <br/> respuesta a un mensaje de dispositivo de nube | 2 días |

> [AZURE.NOTE] Si necesita más de 10 concentradores IoT pagadas con una suscripción de Azure, póngase en contacto con el soporte técnico de Microsoft.

El servicio IoT concentrador acelera las solicitudes cuando se superan los contingentes siguientes:

| Acelerador | Valor por el concentrador |
| -------- | ------------- |
| Operaciones del registro de identidad <br/> (crear, recuperar, enumerar, actualizar y eliminar) <br/> importación/exportación individual o masiva | min-5000/unidad (para S3) <br/> 100/min/unidad (para S1 y S2). |
| Conexiones de dispositivos | s/6000/unidad (para S3), 120/seg/unidad (para S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. |
| Envía el dispositivo a la nube | s/6000/unidad (para S3), 120/seg/unidad (para S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. |
| Dispositivo de nube envía | min-5000/unidad (para S3), / min/unidad de 100 (para S1 y S2). |
| Dispositivo de nube recibe | 50000/min/unidad (para S3), 1000/min/unidad, (para S1 y S2). |
| Operaciones de carga de archivo | carga de archivos de 5000 min/notificaciones/unidad (para S3), carga de archivo 100 notificaciones/min/unidad (para S1 y S2). <br/> URI de SAS 10000 puede ser por una cuenta de almacenamiento de Azure al mismo tiempo.<br/> SAS 10 URI o dispositivo puede ser por al mismo tiempo. |
