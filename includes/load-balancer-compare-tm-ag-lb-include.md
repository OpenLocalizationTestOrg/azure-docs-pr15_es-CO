## <a name="load-balancer-differences"></a>Diferencias de equilibrador de carga

Existen diferentes opciones para distribuir el tráfico de red mediante Microsoft Azure. Estas opciones funcionan de forma diferente entre sí, con una característica diferente de establecer y escenarios diferentes. Cada uno pueden utilizarse de forma aislada o la combinación de ellos.

- **Equilibrador de carga de Azure** funciona en la capa de transporte (capa 4 en la pila de referencia de red OSI). Proporciona distribución de nivel de red del tráfico a través de instancias de una aplicación que se ejecuta en el mismo centro de datos de Azure.

- **Puerta de enlace de aplicación** funciona en la capa de aplicación (capa 7 en la pila de referencia de red OSI). Actúa como un servicio de proxy inverso, finalizando la conexión del cliente y reenviar peticiones a extremos de back-end.

- **Traffic Manager** funciona en el nivel DNS.  Utiliza las respuestas DNS para dirigir el tráfico a extremos distribuidos globalmente para el usuario final. Los clientes entonces conectan a esos extremos directamente.

La tabla siguiente resume las características ofrecidas por cada servicio:

| Servicio | Equilibrador de carga de Azure | Puerta de enlace de aplicación | Traffic Manager |
|---|---|---|---|
|Tecnología| Nivel de transporte (capa 4) | Nivel de aplicación (capa 7) | Nivel DNS |
| Protocolos de aplicación compatibles | Cualquier | HTTP y HTTPS |  Cualquiera (un extremo HTTP es necesario para la supervisión de extremo) |
| Extremos | Instancias de función de máquinas virtuales y servicios en la nube de Azure | Cualquier dirección IP interna de Azure o internet pública dirección IP | VMs Azure, servicios de nube, aplicaciones Web de Azure y extremos externos |
| Soporte de Vnet | Puede utilizar ambas aplicaciones para Internet internas y enfrentados (Vnet) | Puede utilizar ambas aplicaciones para Internet internas y enfrentados (Vnet) |    Sólo es compatible con las aplicaciones a través de Internet |
Supervisión de extremo | Compatible a través de pruebas | Compatible a través de pruebas | Compatible a través de HTTP/HTTPS GET | 

Equilibrador de carga de Azure y red de rutas de puerta de enlace de aplicación tráfico a extremos pero tienen diferentes escenarios de uso para que controlen el tráfico. En la siguiente tabla ayuda a entender la diferencia entre los equilibradores de carga de dos:

| Tipo | Equilibrador de carga de Azure | Puerta de enlace de aplicación |
|---|---|---|
| Protocolos | TCP/UDP | HTTP / HTTPS |
| Reserva de IP | Admite | No se admite | 
| Modo de equilibrio de carga | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Round Robin<br>Enrutamiento basado en dirección URL | 
| Modo de equilibrio de carga (IP de origen / rápidas sesiones) |  tupla de 2 (dirección IP de origen y destino IP), 3-tupla (dirección IP de origen, dirección IP de destino y puerto). Puede escalar hacia arriba o hacia abajo según el número de máquinas virtuales | Afinidad basada en cookies<br>Enrutamiento basado en dirección URL |
| Sondeos de salud | Predeterminado: intervalo de sondeo - 15 segundos. Toma fuera de la rotación: 2 errores continuos. Admite los sondeos definidas por el usuario | Intervalo de sondeo inactivo 30 segundos. Retirado después de un error de único sondeo en modo inactivo o 5 errores consecutivos tráfico real. Admite los sondeos definidas por el usuario | 
| Descarga de SSL | No se admite | Admite | 
  