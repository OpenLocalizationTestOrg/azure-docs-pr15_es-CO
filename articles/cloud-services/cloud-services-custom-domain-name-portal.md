<properties
    pageTitle="Configurar un nombre de dominio personalizado en servicios en la nube | Microsoft Azure"
    description="Aprenda a exponer su aplicación para Azure o datos a internet en un dominio personalizado mediante la configuración de DNS.  Estos ejemplos utilizan el portal de Azure."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurar un nombre de dominio personalizado para un servicio de nube Azure

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-custom-domain-name-portal.md)
- [Portal clásico de Azure](cloud-services-custom-domain-name.md)

Cuando se crea un servicio de nube, Azure lo asigna a un subdominio de **cloudapp.net**. Por ejemplo, si su servicio de nube se denomina "contoso", los usuarios serán capaces de tener acceso a la aplicación en una dirección URL como http://contoso.cloudapp.net. Azure también asigna una dirección IP virtual.

Sin embargo, también puede exponer su aplicación en su propio nombre de dominio, como **contoso.com**. Este artículo explica cómo reservar o configurar un nombre de dominio personalizado para las funciones web de servicio de nube.

¿Tienes ya conceptualizar qué registros CNAME y A? [Salto más allá de la explicación](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Los procedimientos descritos en esta tarea se aplican a los servicios de nube de Azure. Para la aplicación de servicios, consulte [este](../app-service-web/web-sites-custom-domain-name.md). Para cuentas de almacenamiento, consulte [este](../storage/storage-custom-domain-name.md).

<p/>

> [AZURE.TIP]
> Póngase en marcha con mayor rapidez, utilice el nuevo Azure [guiada tutorial](http://support.microsoft.com/kb/2990804)!  Realiza la asociación de un nombre de dominio personalizado y cómo asegurar la comunicación (SSL) con servicios de nube de Azure o sitios Web de Azure un complemento.

## <a name="understand-cname-and-a-records"></a>Comprender los registros CNAME y A

CNAME (o alias) y ambos registros permiten asociar un nombre de dominio a un servidor específico (o servicio en este caso) sin embargo funcionan de manera diferente. También existen algunas consideraciones específicas al utilizar registros con servicios de nube de Azure que debe considerar antes de decidir que va a utilizar.

### <a name="cname-or-alias-record"></a>Registro CNAME o Alias

Un registro CNAME asigna un dominio *específico* , como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónico es el **.cloudapp [myapp] .net** nombre de dominio de su Azure aplicación alojada. Una vez creado, el registro CNAME crea un alias para el **[myapp] .cloudapp .net**. La entrada CNAME resolverá con la dirección IP de su **.cloudapp [myapp] .net** de servicio automáticamente, por lo que si cambia la dirección IP del servicio de nube, no tiene que realizar ninguna acción.

> [AZURE.NOTE]
> Algunos registradores de dominio sólo permiten asignar subdominios al utilizar un registro CNAME, como www.contoso.com y no nombres de raíz, como contoso.com. Para obtener más información sobre registros CNAME, consulte la documentación proporcionada por el registrador, [la entrada de Wikipedia en registro CNAME](http://en.wikipedia.org/wiki/CNAME_record)o el documento de [IETF Domain Names - implementación y especificación](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Un registro

*Un registro* asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un dominio comodín* como ** \*. contoso.com**, a una dirección IP. En el caso de un servicio de nube de Azure, la dirección IP virtual del servicio. Por lo que la ventaja principal de un registro a través de un registro CNAME es que puede tener una entrada que utiliza un carácter comodín, como \* **. contoso.com**, que debería controlar las solicitudes de varios subdominios, como **mail.contoso.com**, **login.contoso.com**o **www.contso.com**.

> [AZURE.NOTE]
> Dado que un registro se asigna a una dirección IP estática, no se puede resolver automáticamente cambios a la dirección IP de su servicio de nube. La dirección IP utilizada por el servicio de nube se asigna la primera vez que se implementa en una ranura vacía (producción o ensayo.) Si elimina la implementación de la ranura, Azure libera la dirección IP y las implementaciones futuras a la ranura de darle una nueva dirección IP.
>
> Para su comodidad, se conserva la dirección IP de una ranura de implementación concreta (producción o ensayo) al intercambio entre el ensayo y las implementaciones de producción o realizar una actualización en contexto de una implementación existente. Para obtener más información acerca de cómo realizar estas acciones, consulte [cómo administrar servicios en la nube](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Agregar un registro CNAME de tu dominio personalizado

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para tu dominio personalizado utilizando las herramientas proporcionadas por el registrador. Cada registrador tiene un método similar pero ligeramente diferente para especificar un registro CNAME, pero los conceptos son los mismos.

1. Use uno de estos métodos para buscar la **. cloudapp.net** nombre de dominio asignado a su servicio de nube.

    * Inicie sesión en el [portal de Azure], seleccione el servicio de nube, consulte la sección de **Fundamentos** de y, a continuación, busque la entrada de la **Dirección URL del sitio** .

        ![sección de referencia rápida que muestra la dirección URL del sitio][csurl]
            
        **OR**
  
    * Instalar y configurar [Azure Powershell](../powershell-install-configure.md)y, a continuación, utilice el comando siguiente:

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Guardar el nombre de dominio utilizado en la dirección URL devuelta por cualquiera de los métodos, ya que lo necesitará al crear un registro CNAME.

1.  Inicie sesión en el sitio Web de tu entidad de registro DNS y vaya a la página de administración de DNS. Busque vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o **Nombre del servidor de administración**.

2.  Ahora buscar donde puede seleccionar o introducir de CNAME. Deberá seleccionar el tipo de registro de una caída de abajo, o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias**o **subdominios**.

3.  También debe proporcionar el dominio o subdominio alias para CNAME, como **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas de tu entidad de registro DNS.

4. A continuación, debe proporcionar un nombre de host canónico, que en este caso es el dominio de **cloudapp.net** de la aplicación.

Por ejemplo, el siguiente registro CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.cloudapp.net**, el nombre de dominio personalizado de su aplicación implementada:

| Nombre de alias/Host/subdominio | Dominio canónico     |
| ------------------------- | -------------------- |
| www                       | contoso.cloudapp.NET |

> [AZURE.NOTE]
Un visitante de **www.contoso.com** nunca verán el host true (contoso.cloudapp.net), por lo que el proceso de reenvío es invisible para el usuario final.

> El ejemplo anterior se aplica sólo al tráfico en el subdominio de **www** . Dado que no puede utilizar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio o subdominio. Si desea dirigir el tráfico desde los subdominios, como *. contoso.com, en la dirección cloudapp.net, puede configurar un * *Redirigir la dirección URL* * o * *Dirección URL adelante** entrada en la configuración de DNS, o crear un registro.


## <a name="add-an-a-record-for-your-custom-domain"></a>Agregar un registro a para tu dominio personalizado

Para crear un registro, debe buscar primero la dirección IP virtual de su servicio de nube. A continuación, agregue una nueva entrada en la tabla DNS para tu dominio personalizado utilizando las herramientas proporcionadas por el registrador. Cada registrador tiene un método similar, aunque ligeramente distinto de especificar un registro, pero los conceptos son los mismos.

1. Utilice uno de los métodos siguientes para obtener la dirección IP de su servicio de nube.

    * Inicio de sesión en el [portal de Azure], seleccione su servicio de nube, mira la sección **Essentials** y, a continuación, busque la entrada de **direcciones IP públicas** .

        ![sección de referencia rápida que muestra la dirección VIP][vip]

        **OR**

    * Instalar y configurar [Azure Powershell](../powershell-install-configure.md)y, a continuación, utilice el comando siguiente:

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Guardar la dirección IP, ya que lo necesitará al crear un registro.

1.  Inicie sesión en el sitio Web de tu entidad de registro DNS y vaya a la página de administración de DNS. Busque vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o **Nombre del servidor de administración**.

2.  Ahora buscar donde puede seleccionar o escribir un registro. Deberá seleccionar el tipo de registro de una caída de abajo, o ir a una página de configuración avanzada.

3. Seleccione o escriba el dominio o subdominio que utilizará este registro. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, escriba '__*__'. Esto cubrirá todos los subdominios, como **mail.customdomain.com**, **login.customdomain.com**y **www.customdomain.com**.

    Si desea crear un registro para el dominio raíz, puede aparecer como el '**@**' símbolo en herramientas de tu entidad de registro DNS.

4. En el campo proporcionado, escriba la dirección IP de su servicio de nube. Esto asocia la entrada de dominio utilizada en el registro con la dirección IP de la implementación del servicio de nube.

Por ejemplo, el siguiente que un registro reenvía todo el tráfico desde **contoso.com** a **137.135.70.239**, la dirección IP de la aplicación implementada:

| Nombre de host/subdominio | Dirección IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


Este ejemplo muestra cómo crear un registro para el dominio raíz. Si desea crear una entrada de comodín para cubrir todos los subdominios, escriba '__*__' como el subdominio.

>[AZURE.WARNING]
>Direcciones IP en Azure son dinámicas de forma predeterminada. Probablemente deseará utilizar una [dirección IP reservada](../virtual-network/virtual-networks-reserved-public-ip.md) para asegurarse de que no cambia su dirección IP.

## <a name="next-steps"></a>Próximos pasos

* [Cómo administrar servicios en la nube](cloud-services-how-to-manage.md)
* [Cómo asignar contenido CDN a un dominio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuración general de su servicio de nube](cloud-services-how-to-configure-portal.md).
* Aprenda cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal de Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 