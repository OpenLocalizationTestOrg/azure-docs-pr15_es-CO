El servicio de copia de seguridad de Azure tiene dos tipos de depósitos: el depósito de copia de seguridad y el depósito de los servicios de recuperación. Vino primero el depósito de copia de seguridad. A continuación, el depósito de los servicios de recuperación llegó para admitir las implementaciones expandida del Administrador de recursos. Microsoft recomienda utilizar las implementaciones del Administrador de recursos, a menos que necesite específicamente una implementación estándar.

| **Implementación** | **Portal** | **Caja fuerte** |
|-----------|------|-----|
|Clásico|[Clásico](https://manage.windowsazure.com)|Copia de seguridad|
|Administrador de recursos|[Azure](https://portal.azure.com)|Servicios de recuperación|

> [AZURE.NOTE] Depósitos de copia de seguridad no pueden proteger las soluciones implementadas por el Administrador de recursos. Sin embargo, puede utilizar un depósito de los servicios de recuperación para proteger servidores implementado tradicionalmente y máquinas virtuales.  
