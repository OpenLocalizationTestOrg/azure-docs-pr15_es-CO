<properties
    pageTitle="Solución de problemas de extremos de Azure CDN devolver estado 404 | Microsoft Azure"
    description="Solucionar problemas de códigos de respuesta 404 con extremos de Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Solución de problemas de extremos CDN devolver 404 Estados

Este artículo le ayuda a solucionar problemas con [extremos CDN](cdn-create-new-endpoint.md) devolver 404 errores.

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [el Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede presentar un incidente de soporte de Azure. Ir al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

## <a name="symptom"></a>Síntoma

Ha creado un perfil CDN y un extremo, pero su contenido no parece estar disponible en la CDN.  Los usuarios que intentan tener acceso a su contenido a través de la dirección URL de CDN de recibirán los códigos de estado HTTP 404. 

## <a name="cause"></a>Causa

Hay varias causas posibles, incluyendo:

- Origen del archivo no es visible para la CDN
- El extremo está configurado incorrectamente, causando la CDN buscar en el lugar equivocado
- El host está rechazando el encabezado de host desde la CDN
- El extremo no ha tenido tiempo en propagarse por la CDN

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

> [AZURE.IMPORTANT] Después de crear un extremo CDN, no inmediatamente estará disponible para su uso, como toma tiempo para que el registro se propaguen a través de la CDN.  Para los perfiles de <b>Azure CDN de Akamai</b> , propagación normalmente se completa dentro de un minuto.  Perfiles de <b>Azure CDN de Verizon</b> , propagación generalmente se completará dentro de 90 minutos, pero en algunos casos puede tardar más tiempo.  Si realiza los pasos en este documento y aún obtiene 404 respuestas, considere la posibilidad de esperar unas horas para comprobar otra vez antes de abrir un ticket de soporte.

### <a name="check-the-origin-file"></a>Compruebe el archivo de origen

En primer lugar, quieres que verifiquemos que el archivo que queremos en caché está disponible en nuestro origen y es accesible públicamente.  La forma más rápida de hacerlo es abrir un explorador en un In Private o sesión de Incognito y busque el archivo.  Simplemente escriba o pegue la dirección URL en el cuadro Dirección y ver si da como resultado el archivo que espera.  Para este ejemplo, voy a utilizar un archivo en una cuenta de almacenamiento de Azure, accesible en `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como puede ver, pasa correctamente la prueba.

![¡Un éxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Aunque esta es la forma más rápida y sencilla para comprobar que el archivo está disponible públicamente, algunas configuraciones de red de su organización podrían dar la impresión de que este archivo está públicamente disponible cuando es, de hecho, sólo visible para los usuarios de la red (incluso si está hospedado en Azure).  Si tienes un explorador externo desde el que puede probar, por ejemplo, un dispositivo móvil que no está conectado a la red de su organización o una máquina virtual en Azure, que sería mejor.

### <a name="check-the-origin-settings"></a>Compruebe la configuración de origen

Ahora que ya hemos verificado que el archivo está disponible públicamente en internet, quieres que verifiquemos nuestra configuración de origen.  En el [Portal de Azure](https://portal.azure.com), vaya a su perfil CDN y haga clic en el extremo que está solucionando.  En la hoja de **extremo** resultante, haga clic en el origen.  

![Placa de extremo con origen resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Aparece la hoja de **origen** . 

![Hoja de origen](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Nombre de host y el tipo de origen

Compruebe que el **tipo de origen** es correcto y el **nombre de host de origen**.  En mi ejemplo, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, la parte de nombre de host de la dirección URL es `cdndocdemo.blob.core.windows.net`.  Como puede ver en la captura de pantalla, esto es correcto.  Para el almacenamiento de Azure, Web App y orígenes de servicio de nube, el campo de **nombre de host de origen** es una lista desplegable, por lo que no necesitamos preocuparse escribe correctamente.  Sin embargo, si utiliza un origen personalizado, es *absolutamente crítico* que se ha escrito correctamente el nombre de host!

#### <a name="http-and-https-ports"></a>Puertos HTTP y HTTPS

La otra cosa para comprobar aquí es los **puertos** **HTTP** y HTTPS.  En la mayoría de los casos, 80 y 443 son correctos y no se requerirá ningún cambio.  Sin embargo, si el servidor de origen está escuchando en un puerto diferente, que deberá estar representado aquí.  Si no está seguro, simplemente mirar la dirección URL para el archivo de origen.  Las especificaciones de HTTP y HTTPS especifican los puertos 80 y 443 como los valores predeterminados. En mi URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, no se especifica un puerto, por lo que se asume el valor predeterminado de 443 y la configuración es correcta.  

Decir, la dirección URL para el archivo de origen que ha comprobado anteriormente es `http://www.contoso.com:8080/file.txt`.  Nota el `:8080` al final del segmento del nombre de host.  Que indica al explorador que utilice el puerto `8080` para conectarse al servidor de web en `www.contoso.com`, por lo que deberá introducir 8080 en el campo **puerto HTTP** .  Es importante destacar que estas configuraciones de puerto sólo afectan a qué puerto del extremo que se utiliza para recuperar información desde el origen.

> [AZURE.NOTE] Extremos de **Azure CDN de Akamai** no permiten la gama completa de puerto TCP de origen.  Para obtener una lista de los puertos de origen que no están permitidos, vea [Azure CDN de Akamai permitidos los puertos de origen](https://msdn.microsoft.com/library/mt757337.aspx).  
  
### <a name="check-the-endpoint-settings"></a>Compruebe la configuración de extremo

En la hoja de **extremo** , haga clic en el botón **Configurar** .

![Placa de extremo con botón configurar resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Aparece la hoja de **Configurar** del extremo.

![Configurar la hoja](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos

Para los **protocolos**, compruebe que está seleccionado el protocolo utilizado por los clientes.  El mismo protocolo usado por el cliente será la utilizada para obtener acceso al origen, por lo que es importante tener los puertos de origen configurados correctamente en la sección anterior.  El extremo sólo escucha en el predeterminado puertos HTTP y HTTPS (80 y 443), independientemente de los puertos de origen.

Volvamos a nuestro ejemplo hipotético con `http://www.contoso.com:8080/file.txt`.  Como recordará, Contoso especificado `8080` como su HTTP puerto pero supongamos también que especifiquen `44300` como su puerto HTTPS.  Si crea un extremo denominado `contoso`, su nombre de host del extremo CDN sería `contoso.azureedge.net`.  Una solicitud de `http://contoso.azureedge.net/file.txt` es una solicitud HTTP, por lo que el extremo utilizaría HTTP en el puerto 8080 para recuperarlo desde el origen.  Una solicitud segura a través de HTTPS, `https://contoso.azureedge.net/file.txt`, provocaría el extremo utilizar HTTPS en el puerto 44300 cuando al recuperar el archivo desde el origen.

#### <a name="origin-host-header"></a>Encabezado de host de origen

El **encabezado de host de origen** es el valor del encabezado host enviado al origen con cada solicitud.  En la mayoría de los casos, debe ser el mismo que el **nombre de host de origen** se comprobó anteriormente.  Un valor incorrecto en este campo normalmente no causará 404 Estados, pero es probable que se produzcan otros Estados 4xx, dependiendo de lo que espera el origen.

#### <a name="origin-path"></a>Ruta de acceso de origen

Por último, quieres que verifiquemos nuestra **ruta de origen**.  De forma predeterminada está en blanco.  Sólo debe utilizar este campo si desea limitar el ámbito de los recursos alojados por el origen que desea que estén disponibles en la CDN.  

Por ejemplo, en el extremo, quería que todos los recursos en mi cuenta de almacenamiento de información esté disponible, por lo que he dejado en blanco a **la ruta de origen** .  Esto significa que una solicitud para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` da como resultado una conexión desde Mi extremo a `cdndocdemo.core.windows.net` que solicita `/publicblob/lorem.txt`.  Asimismo, una solicitud de `https://cdndocdemo.azureedge.net/donotcache/status.png` da como resultado el extremo que solicita `/donotcache/status.png` desde el origen.

Pero, ¿qué sucede si no deseo utilizar la CDN para cada ruta de acceso en mi origen?  Digamos que sólo desea exponer la `publicblob` ruta de acceso.  Si escribo */publicblob* en el campo de **ruta de origen** , que hará que el extremo insertar */publicblob* antes de cada solicitud realizada al origen.  Esto significa que la solicitud de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` realmente llevará la parte de la solicitud de la dirección URL, `/publicblob/lorem.txt`y anexar `/publicblob` al principio. Esto da como resultado una solicitud de `/publicblob/publicblob/lorem.txt` desde el origen.  Si no se resuelve esa ruta de acceso a un archivo real, el origen devolverá un estado 404.  La dirección URL correcta para recuperar lorem.txt en este ejemplo sería realmente `https://cdndocdemo.azureedge.net/lorem.txt`.  Nota que no incluimos la ruta de acceso */publicblob* , porque la parte de la solicitud de la dirección URL es `/lorem.txt` y agrega el extremo `/publicblob`, lo `/publicblob/lorem.txt` pasen la solicitud al origen.
