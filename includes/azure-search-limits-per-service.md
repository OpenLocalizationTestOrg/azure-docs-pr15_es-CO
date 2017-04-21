Almacenamiento de información está limitado por el espacio en disco o por un límite en el *número máximo* de índices o de documentos, lo que ocurra primero. 

Recurso|Gratis|Básico|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Acuerdo de nivel de servicio (SLA)|No <sup>1</sup> |Sí |Sí  |Sí |Sí |Sí
Almacenamiento de información por partición|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
Particiones por servicio|N /|1|12|12|12|3
Tamaño de la partición|N /|2 GB|25 GB|100 GB|200 GB |200 GB
Réplicas|N /|1|12|12|12|12
Índices máximo|3|5|50|200|200|1000 por cada partición o 3000 por servicio
Máximo de documentos|10.000|1 millón|15 millones cada partición o 180 millones por servicio |60 millones por partición o 720 millones por servicio |120 millones por partición o 1,4 mil millones por servicio|1 millón por índice o 200 millones por partición |
Estimado consultas por segundo (QPS)|N /|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|~ 60 por réplica|> 60 por réplica

<sup>1</sup> libre y SKU de vista previa no se incluyen en los acuerdos de nivel de servicio (SLA). Los SLA se aplican una vez que esté disponible un SKU.