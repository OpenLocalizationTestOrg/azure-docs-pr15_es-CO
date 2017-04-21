Recurso|Gratis|Compartido (vista previa)|Básico|Estándar|Premium (vista previa)</th>
---|---|---|---|---|---
[Web, móvil o API aplicaciones](https://azure.microsoft.com/services/app-service/) por [plan de servicio de la aplicación](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>
[Aplicaciones de lógica](https://azure.microsoft.com/services/app-service/logic/) por cada [plan de servicio de la aplicación](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 por núcleo|20 por núcleo
[Plan de servicio de la aplicación](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 por región|10 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos
Calcular el tipo de instancia|Compartida|Compartida|Dedicado<sup>3</sup>|Dedicado<sup>3</sup>|Dedicado<sup>3</sup></p>
[Escalado horizontal](../articles/app-service-web/web-sites-scale.md) (instancias máx.)|1 compartido|1 compartido|dedicado 3<sup>3</sup>|dedicado 10<sup>3</sup>|20 dedicado (50 en ASE)<sup>3,4</sup>
Almacenamiento<sup>5</sup>|<sup>5</sup> de 1 GB|<sup>5</sup> de 1 GB|<sup>5</sup> de 10 GB|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
Tiempo de CPU (corto)<sup>6</sup>|3 minutos|3 minutos|Ilimitado, pago a estándar de [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pago a tasas estándares|Ilimitado, pago a tasas estándares
CPU de tiempo (día)<sup>6</sup>|60 minutos|240 minutos|Ilimitado, pago a estándar de [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pago a tasas estándares|Ilimitado, pago a tasas estándares
Memoria (1 hora)|1024 MB por cada plan de servicio de la aplicación|1024 MB por aplicación|N /|N /|N /
Ancho de banda|165 MB|Ilimitado, aplicar [tasas de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/)|Ilimitado, aplicarán las tasas de transferencia de datos|Ilimitado, aplicarán las tasas de transferencia de datos|Ilimitado, aplicarán las tasas de transferencia de datos
Arquitectura de la aplicación|32 bits|32 bits|32-bit/64-bit|32-bit/64-bit|32-bit/64-bit
Web Sockets por instancia<sup>7</sup>|5|35|350|Ilimitado|Ilimitado
Simultáneas [conexiones depurador](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicación|1|1|1|5|5
[subdominio azurewebsites.NET con S/FTP y SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Compatibilidad con [dominios personalizados](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
Dominio personalizado [compatibilidad con SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|||Ilimitado|Ilimitado, 5 de SNI SSL y conexiones de SSL IP 1 incluido|Ilimitado, 5 de SNI SSL y conexiones de SSL IP 1 incluido
Equilibrador de carga integrado||X|X|X|X
[Siempre encendida](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Copias de seguridad programadas](../articles/app-service-web/web-sites-backup.md)||||Una vez al día|Una vez cada 5 minutos<sup>8</sup>
[Escala automática](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
Soporte de [Programador de Azure](https://azure.microsoft.com/services/scheduler/)||X|X|X|X
[Supervisión de extremo](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Ranuras de ensayo (vista previa)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Dominios personalizados por aplicación</a>||500|500|500|500
SLA||<p>|99,9%|99,95%<sup>10</sup>|99,95%<sup>10</sup>

<sup>1</sup> Cuotas de almacenamiento y aplicaciones son por plan de servicio de la aplicación, a menos que se indique lo contrario.  
<sup>2</sup> El número real de las aplicaciones que se pueden alojar en estos equipos depende de la actividad de las aplicaciones, el tamaño de las instancias de la máquina y la utilización de los recursos correspondientes.  
<sup>3</sup> Instancias dedicadas pueden ser de diferentes tamaños. Para obtener más detalles, consulte [Tarifas de servicio de la aplicación](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) .  
<sup>4</sup> Nivel Premium permite hasta 50 calcula instancias (sujeto a disponibilidad) y 500 GB de espacio en disco cuando se utiliza en entornos de servicio de la aplicación y 20 calcular instancias y 250 GB de almacenamiento, en caso contrario.  
<sup>5</sup> El límite de almacenamiento de información es el tamaño total del contenido en todas las aplicaciones en el mismo plan de servicio de la aplicación. Hay más opciones de almacenamiento en [El entorno de servicio de la aplicación](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Estos recursos están limitados por los recursos físicos en las instancias dedicados (el tamaño de la instancia y el número de instancias).  
<sup>7</sup> Si se escala una aplicación en el nivel básico a dos instancias, tiene 350 conexiones simultáneas para cada una de las dos instancias.  
<sup>8</sup> Nivel Premium permite intervalos de copia de seguridad hacia abajo hasta cada 5 minutos mediante entornos de servicio de la aplicación y 50 veces por día en caso contrario.  
<sup>9</sup> Ejecutar ejecutables personalizados y secuencias de comandos a petición, según una programación o de forma continua como una tarea en segundo plano dentro de la instancia de servicio de la aplicación. Siempre es necesario para la ejecución continua de WebJobs. Se requiere para WebJobs programada Azure programador libre o estándar. No hay ningún límite en el número de WebJobs que puede ejecutar en una instancia de servicio de aplicación predefinido, pero hay límites prácticos que dependen de lo que el código de la aplicación está intentando hacer.   
<sup>10</sup> SLA del 99,95% referente a las implementaciones que usan varias instancias con Azure Traffic Manager configurado para failover.  
