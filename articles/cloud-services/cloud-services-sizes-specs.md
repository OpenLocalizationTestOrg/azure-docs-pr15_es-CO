<properties
 pageTitle="Tamaños de servicios en la nube | Microsoft Azure"
 description="Muestra los tamaños de la máquina virtual diferente (y IDs) para los roles de web y trabajador de servicio de nube de Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Tamaños de los servicios de nube

Este tema describe las opciones para las instancias de rol de servicio de nube (roles de web y los roles de trabajo) y tamaños disponibles. Incluye también las consideraciones de implementación para tener en cuenta cuando se planea utilizar estos recursos. Cada tamaño tiene un identificador que se colocará en el [archivo de definición del servicio](cloud-services-model-and-package.md#csdef).

Servicios en la nube es uno de varios tipos de recursos de cálculo ofrecidos por Azure. Haga clic [aquí](cloud-services-choose-me.md) para obtener más información acerca de los servicios de nube.

> [AZURE.NOTE]Para ver los límites de Azure relacionados, vea [suscripción de Azure y límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamaños de web y trabajador instancias de rol

Hay varios tamaños estándares para elegir en Azure. Consideraciones para algunos de estos tamaños incluyen:

* Máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor potencia de computación y el rendimiento de disco temporal. Máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor tasa de memoria al núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquina Virtual de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Dv2-serie, una versión actualizada de la serie D original, cuenta con una CPU más potente. La CPU Dv2-series es del 35% más rápido que la CPU de la serie D. Se basa en la última generación v3 de 2,4 GHz Intel Xeon® E5-2673 procesador (Haswell) y con Intel Turbo Boost Technology 2.0, puede ir hasta 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de memoria y disco que la serie D.

*   Máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y ejecutan en hosts que tengan procesadores de la familia Intel Xeon E5 V3.

*   Las máquinas virtuales de serie pueden implementarse en una variedad de tipos de hardware y procesadores. El tamaño está limitado, según el hardware, que ofrecen un rendimiento coherente de procesador para la instancia en ejecución, independientemente del hardware que se implementa en. Para determinar el hardware físico en el que se implementa este tamaño, consultar el hardware virtual desde dentro de la máquina Virtual.

*   El tamaño A0 es excesiva suscrito en el hardware físico. Para este tamaño específico únicamente, otras implementaciones de clientes pueden afectar el rendimiento de la carga de trabajo en ejecución. A continuación se describe el rendimiento relativo como línea de base esperada, sujeto a una variabilidad aproximada del 15 por ciento.


El tamaño de la máquina virtual afecta el precio. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de información de la máquina virtual. Los costos de almacenamiento se calculan por separado basándose en páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y el [Precio de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/). 


Las consideraciones siguientes pueden ayudarle a decidir en un tamaño:


* Los tamaños A8 A11 y H-series también son conocidos como *instancias de actividades informáticas complejas*. El hardware que ejecuta estos tamaños está diseñado y optimizado para cálculos intensivos y aplicaciones, modelado y simulaciones de clúster de aplicaciones intensivas de red, incluida la informática de alto rendimiento (HPC). La serie A8-A11 utiliza Intel Xeon E5-2670 @ 2,6 GHZ y la serie H utiliza Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obtener información detallada y consideraciones acerca del uso de estos tamaños, consulte [acerca de las VMs de serie A serie H e informáticas](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Serie Dv2, serie D, serie G, son ideales para aplicaciones que exigen CPUs más rápidas, local de mejor performance de disco o tienen demandas de memoria superior.  Ofrecen una potente combinación para muchas aplicaciones empresariales.

*   Algunos de los hosts físicos en centros de datos de Azure pueden no admitir mayores tamaños de máquina virtual, como A5: A11. Como resultado, verá el mensaje de error **Error al configurar la máquina virtual {nombre de equipo}** o **no se pudo crear la máquina virtual {nombre de equipo}** al cambiar el tamaño de una máquina virtual existente a un nuevo tamaño; creación de una nueva máquina virtual en una red virtual creada antes del 16 de abril de 2013; o agregar una nueva máquina virtual a un servicio de nube existente. Consulte [Error: "Error al configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte de soluciones para cada escenario de implementación.  

* La suscripción también puede limitar el número de núcleos que puede implementar en ciertas familias de tamaño. Para aumentar la cuota, póngase en contacto con el soporte de Azure.


## <a name="performance-considerations"></a>Consideraciones de rendimiento

Hemos creado el concepto de la Azure Compute unidad (ACU) para proporcionar una manera de comparar el rendimiento de cómputo (CPU) a través de unidades de almacenamiento de Azure. Esto ayudará a identificar fácilmente qué SKU es más probable que satisfacen sus necesidades de rendimiento.  ACU actualmente estandarizado en un pequeño (Standard_A1) VM está 100 y todos los SKU a continuación representa aproximadamente cuánto más rápido que UA puede ejecutar un banco de pruebas estándar. 

>[AZURE.IMPORTANT] El ACU es sólo de referencia.  Los resultados de la carga de trabajo pueden variar. 

<br>

|Familia SKU |ACU/núcleo |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8-A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[D1-15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marcados con un * utilizar la tecnología de Intel® Turbo para aumentar la frecuencia de la CPU y proporcionar una mejora del rendimiento.  La cantidad de la mejora puede variar según el tamaño de la memoria virtual, carga de trabajo y otras cargas de trabajo que se ejecutan en el mismo host.

## <a name="size-tables"></a>Tamaño de tablas

Las tablas siguientes muestran los tamaños y las capacidades que proporcionan.

* Se muestra la capacidad de almacenamiento en unidades de GiB o 1024 ^ 3 bytes. Al comparar discos medido en GB (1000 ^ 3 bytes) a discos medidos en GiB (1024 ^ 3) Recuerde que los números de capacidad en chaveta pueden aparecer más pequeños. Por ejemplo, GiB 1023 = 1098.4 GB

* Rendimiento del disco se mide en operaciones de entrada/salida por segundo (IOPS) y MBps donde MBps = 10 ^ 6 bytes/seg.

* Discos de datos pueden funcionar en modo en caché o sin almacenar en caché. Para la operación de disco de datos almacenados en caché, el modo de caché de host se establece en **sólo lectura** o **ReadWrite**.  Para la operación de disco de datos sin almacenar en caché, el modo de caché de host se establece en **None**.

* Ancho de banda de red máximo es el máximo ancho de banda agregado asignado y asignado por tipo de máquina virtual. El ancho de banda máximo proporciona directrices para seleccionar el tipo correcto de VM para garantizar la capacidad de red adecuada están disponible. Cuando se mueve entre bajo, medio, alto y muy alto, el rendimiento aumentará en consecuencia. Rendimiento de la red real depende de muchos factores como la red y cargas de aplicaciones y configuración de red de la aplicación.


## <a name="a-series"></a>Serie A

| Tamaño        | Núcleos de CPU | Memoria: GiB | Unidad de disco duro: GiB | Discos de datos MAX | Rendimiento de disco de datos máximo: IOPS | Max NICs / ancho de banda de red |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / baja                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderado              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderado              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / alta                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderado              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / alta                  |

## <a name="a-series---compute-intensive-instances"></a>Serie A - instancias de actividades informáticas complejas

Para obtener información y consideraciones sobre el uso de estos tamaños, consulte [acerca de las VMs de serie A serie H e informáticas](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Tamaño         | Núcleos de CPU | Memoria: GiB | Unidad de disco duro: GiB | Discos de datos MAX | Rendimiento de disco de datos máximo: IOPS | Max NICs / ancho de banda de red |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muy alto             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muy alto             |

* RDMA capaz

## <a name="d-series"></a>De la serie D


| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento de disco de datos máximo: IOPS | Max NICs / ancho de banda de red |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muy alta             |

## <a name="dv2-series"></a>Dv2-series

| Tamaño            | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento de disco de datos máximo: IOPS | Max NICs / ancho de banda de red |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / extremadamente alta        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / extremadamente alta        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / extremadamente alta        |

## <a name="g-series"></a>G-series

| Tamaño        | Núcleos de CPU | Memoria: GiB  | SSD local: GiB  | Discos de datos MAX | Rendimiento de disco máximo: IOPS | Max NICs / ancho de banda de red |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / alta                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / alta                  |
| Standard_G3 | 8         | 112          | 1.536                | 16             | 16 x 500           | 4 / muy alto             |
| Standard_G4 | 16        | 224          | 3.072                | 32             | 32 x 500           | 8 / extremadamente alta        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / extremadamente alta        |


## <a name="h-series"></a>H-series

Azure H-series las máquinas virtuales son la próxima generación informática de alto rendimiento que máquinas virtuales dirigidas a necesidades informáticas de gama alta, como modelado molecular y dinámica de fluidos computacional. Estas 8 y 16 core VMs se basan en la tecnología de procesador Intel Haswell E5 2667 V3 con memoria DDR4 y almacenamiento SSD de base local. 

Además de la potencia de la CPU considerable, la serie H ofrece diversas opciones para redes de RDMA de latencia baja mediante FDR InfiniBand y varias configuraciones de memoria para soportar los requerimientos computacionales intensivas de memoria.


| Tamaño           | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos MAX | Rendimiento de disco máximo: IOPS | Max NICs / ancho de banda de red |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alta                  |


* RDMA capaz

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Notas: Estándar A0 - A4 mediante CLI y PowerShell 

En el modelo de implementación clásica, algunos nombres de tamaño VM son ligeramente diferentes en CLI y PowerShell:

* Standard_A0 es ExtraSmall 
* Standard_A1 es pequeño
* Standard_A2 es el medio
* Standard_A3 es grande
* Standard_A4 es más

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamaños de servicios en la nube

Puede especificar el tamaño de la máquina Virtual de una instancia de la función como parte del modelo de servicio descrito por el [archivo de definición del servicio](cloud-services-model-and-package.md#csdef). El tamaño de la función determina el número de núcleos de CPU, capacidad de memoria y el tamaño del sistema de archivos local que está asignado a una instancia en ejecución. Elija el tamaño de papel basado en el requisito del recurso de la aplicación.

Éste es un ejemplo para establecer el tamaño de papel para ser [Standard_D2](#general-purpose-d) para una instancia del rol de Web:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Próximos pasos

- Obtenga información acerca de la [suscripción de azure y límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md).
- Obtenga más información [acerca de las VMs de serie A serie H e informáticas](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) para cargas de trabajo como High-performance Computing (HPC).

