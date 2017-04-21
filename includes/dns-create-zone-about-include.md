Una zona DNS se utiliza para alojar los registros DNS para un dominio determinado. Para iniciar el alojamiento de su dominio, debe crear una zona DNS. Cualquier registro DNS que se crean para un dominio determinado será dentro de una zona DNS para el dominio. 

Por ejemplo, el dominio "contoso.com" puede contener un número de registros DNS, como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). 


## <a name="names"></a>Acerca de los nombres de zona DNS
 
- El nombre de la zona debe ser único dentro del grupo de recursos y la zona no debe existir ya. De lo contrario, se producirá un error en la operación.

- El mismo nombre de zona se pueden reutilizar en otro grupo de recursos o una suscripción de Azure diferente. 

- Cuando varias zonas que comparten el mismo nombre, cada instancia se asignarán diferentes direcciones del servidor, y sólo una instancia puede delegarse desde el dominio primario. Para obtener más información, vea [delegado un dominio DNS de Azure](../articles/dns/dns-domain-delegation.md).