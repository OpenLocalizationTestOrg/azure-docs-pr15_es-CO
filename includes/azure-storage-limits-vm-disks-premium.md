**Discos de máquinas virtuales: por límites de cuenta**

Recurso|Límite predeterminado
---|---
Capacidad total de disco por cuenta|35 TB
Capacidad total instantánea por cuenta|10 TB
Ancho de banda máximo por cuenta (entrada + salida<sup>1</sup>)|< = 50 GB/s

<sup>1</sup> *Entrada* hace referencia a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

**Discos de máquinas virtuales: por los límites de disco**

Tipo de disco de almacenamiento de información de Premium | P10 | P20 | P 30
---|---|---|---
Tamaño de disco | Chaveta 128 | 512 giB | Chaveta de 1024 (1 TB)
Max IOPS por disco | 500 | 2300 | 5000
Rendimiento máximo por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo
Número máximo de discos por cuenta de almacenamiento | 280 | 70 | 35

**Discos de máquinas virtuales: por límites VM**

Recurso|Límite predeterminado
---|---
Max IOPS por VM.|80.000 IOPS con GS5 MV<sup>1</sup>
Rendimiento máximo por VM.|2.000 MB/s con GS5 MV<sup>1</sup>

<sup>1</sup> Consulte [Tamaño de memoria virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) para límites en otros tamaños de máquina virtual. 
