Recurso|Límite predeterminado|Límite máximo
---|---|---
[Funciones Web/trabajador por distribución](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup>|25|25
[Extremos de entrada de instancia](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por distribución|25|25
[Extremos de entrada](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por distribución|25|25
[Extremos internos](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por distribución|25|25

<sup>1</sup> Cada servicio de nube con los roles de Web y trabajador puede tener dos implementaciones, uno para producción y otro para el ensayo. Tenga en cuenta que este límite se refiere al número de funciones distintas (configuración) y no el número de instancias por función (escalar).
