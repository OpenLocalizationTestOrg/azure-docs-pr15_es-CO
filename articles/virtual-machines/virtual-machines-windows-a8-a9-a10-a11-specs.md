<properties
 pageTitle="Acerca de VMs informáticas con Windows | Microsoft Azure"
 description="Obtener información general y consideraciones para el uso de los tamaños de alto uso computacional serie H y A8, A9, A10 y A11 Azure para servicios Windows VMs y nube"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Acerca de VMs de serie A serie H e informáticas

Aquí está la información básica y algunas consideraciones para el uso de la nueva serie H Azure y el A8, A9, A10 y A11 instancias anteriores, también conocido como instancias de *actividades informáticas complejas* . En este artículo se centra en usar estas instancias para las VM de Windows. En este artículo también está disponible para [Máquinas virtuales Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA

Puede crear clústeres de instancias de servidor de Windows con capacidad RDMA e implementar una de las implementaciones de MPI compatibles para aprovechar la red RDMA de Azure. Esta red de baja latencia y alto rendimiento está reservada para sólo tráfico MPI.

* **Sistema operativo**
    * **Máquinas virtuales** - Windows Server 2012 R2, Windows Server 2012
    * **Servicios de nube** - 2012 R2 de Windows Server, Windows Server 2012, la familia del sistema operativo de invitado de Windows Server 2008 R2

* **MPI** - MPI Microsoft (MS-MPI) 2012 R2 o posterior, la Biblioteca MPI Intel 5.x

Implementaciones de MPI admitidas utilizan la interfaz directa de red de Microsoft para comunicarse entre instancias. Consulte [configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) y [Utilice las tareas de varias instancias para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) en lote de Azure](../batch/batch-mpi.md) para opciones de implementación y los pasos de configuración de ejemplo.


>[AZURE.NOTE]En VMs RDMA capaz de actividades informáticas complejas, debe agregarse la extensión HpcVmDrivers para las máquinas virtuales para instalar a controladores de dispositivos de red de Windows que son necesarios para la conectividad RDMA. En la mayoría de las implementaciones, la extensión HpcVmDrivers se agrega automáticamente. Si necesita agregar la extensión de usted mismo, consulte [VM administrar extensiones](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Consideraciones para HPC Pack y Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), clúster HPC gratuito de Microsoft y la solución de administración del trabajo, no es necesario para poder utilizar las instancias informáticas con Windows Server. Sin embargo, es una opción para crear un clúster de cálculo en Azure para ejecutar aplicaciones basadas en Windows MPI y otras cargas de trabajo HPC. HPC Pack 2012 R2 y las versiones posteriores incluyen un entorno de tiempo de ejecución para MS-MPI que puede utilizar la red RDMA Azure cuando se implementa en VMs RDMA capaz.

Para que obtener más información y listas de comprobación para utilizar las instancias informáticas con paquete de HPC en Windows Server, vea [instalar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Próximos pasos

* Para obtener más información acerca de la disponibilidad y precios de los tamaños de actividades informáticas complejas, consulte [Servicios de nube de precios](https://azure.microsoft.com/pricing/details/cloud-services/)y [precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) .

* Para las capacidades de almacenamiento de información y detalles del disco, consulte [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

* Para empezar a implementar y utilizar instancias de actividades informáticas complejas con HPC Pack en Windows, consulte [configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obtener información acerca del uso de instancias A8 y A9 para ejecutar aplicaciones de MPI con Azure lote, consulte [tareas de varias instancias de uso para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) en lote de Azure](../batch/batch-mpi.md).
