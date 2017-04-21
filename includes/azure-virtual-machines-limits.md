Recurso|Límite predeterminado|Límite máximo
---|---|---
[Máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about.md) por servicio de nube<sup>1</sup>|50|50
Entrada extremos por servicio de nube<sup>2</sup>|150|150

<sup>1</sup> Las máquinas virtuales creadas en la administración de servicios (en lugar de Resource Manager) se almacenan automáticamente en un servicio de nube. Puede agregar más máquinas virtuales para ese servicio de nube para disponibilidad y equilibrio de carga. Consulte [cómo conectarse a máquinas virtuales en una red Virtual o el servicio de nube](../articles/virtual-machines/virtual-machines-linux-classic-connect-vms.md).

<sup>2</sup> Extremos de entrada permitirán a comunicaciones a una máquina virtual desde fuera de servicio en nube de la máquina virtual. Máquinas virtuales en el mismo servicio en nube o red virtual automáticamente puede comunicarse entre sí. Consulte [cómo configurar los extremos de una máquina Virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md). 
