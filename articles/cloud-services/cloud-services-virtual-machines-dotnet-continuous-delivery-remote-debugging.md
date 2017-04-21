<properties
    pageTitle="Habilitar la depuración remota con la continua oferta | Microsoft Azure"
    description="Obtenga información sobre cómo habilitar la depuración remota cuando se utiliza la entrega continua para implementar en Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Habilitar la depuración remota cuando se utiliza la entrega continua para publicar en Azure

Puede habilitar la depuración remota en Azure, para los servicios de nube o máquinas virtuales, cuando se utiliza la [entrega continua](cloud-services-dotnet-continuous-delivery.md) para publicar en Azure siguiendo estos pasos.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Habilitar la depuración remota de servicios de nube

1. El agente de compilación, configurar el entorno inicial de Azure tal como se indica en la [Línea de comandos de compilación para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Dado que el tiempo de ejecución de depuración remota (msvsmon.exe) es necesaria para el paquete, instalar las [Herramientas remotas para Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (o las [Herramientas remotas de Microsoft Visual Studio 2013 actualización 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) si está utilizando Visual Studio 2013). Como alternativa, puede copiar los archivos binarios de depuración remota desde un sistema que tiene Visual Studio instalado.
3. Crear un certificado como se describe en [Introducción a los certificados de servicios de nube de Azure](cloud-services-certs-create.md). Mantener el .pfx y la huella digital de certificado RDP y cargar el certificado al servicio de nube de destino.
4. Utilice las siguientes opciones en la línea de comandos de MSBuild para compilar y empaquetar con depuración remota habilitada. (Sustituya los trazados reales para los archivos de sistema y proyecto de los elementos entre corchetes de ángulo).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`es la ruta de acceso a la carpeta que contiene msvsmon.exe en las herramientas remotas de Visual Studio.
    `RemoteDebuggerConnectorVersion`es la versión del SDK de Azure en su servicio de nube. También debe coincidir con la versión instalada con Visual Studio.

5. Publicar en el servicio de nube de destino mediante el archivo de paquete y .cscfg generado en el paso anterior.
6. Importar el certificado (archivo .pfx) en el equipo que tiene Visual Studio con Azure SDK para .NET instalado. Asegúrese de que al importar a la `CurrentUser\My` el almacén de certificados, en caso contrario, asociar el depurador de Visual Studio se producirá un error.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Habilitar la depuración remota para las máquinas virtuales

1. Crear una máquina virtual en Azure. Consulte [crear una máquina Virtual con Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [crear y administrar las máquinas virtuales Azure en Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. En la [página del portal clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), ver el escritorio de la máquina virtual para ver la **Huella digital de certificado de RDP**de la máquina virtual. Este valor se utiliza para la `ServerThumbprint` valor en la configuración de la extensión.
3. Crear un certificado de cliente, como se describe en [Introducción a los certificados de servicios de nube de Azure](cloud-services-certs-create.md) (mantener el .pfx y la huella digital de certificado RDP).
4. Instale Powershell Azure (versión 0.7.4 o posterior) como se describe en [cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md).
5. Ejecute el siguiente script para habilitar la extensión de RemoteDebug. Reemplace las rutas de acceso y sus datos personales con su propio nombre, como el nombre de la suscripción, el nombre del servicio y la huella digital.

    >[AZURE.NOTE] Esta secuencia de comandos está configurado para el año 2015 de Visual Studio. Si está utilizando Visual Studio 2013, modifique la `$referenceName` y `$extensionName` las asignaciones siguientes para utilizar `RemoteDebugVS2013` (en lugar de `RemoteDebugVS2015`).

    <pre>
   AzureAccount agregar

    SELECT-AzureSubscription "Mi suscripción de Microsoft"

    $vm = "mytestvm1" de get-AzureVM - ServiceName-nombre "mytestvm1"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint en $endpoints) {Add-AzureEndpoint - VM $vm-nombre de $endpoint. Name - protocolo tcp PublicPort - $endpoint. PublicPort - PuertoLocal $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = "1.*" $publicConfiguration = "<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Conjunto de AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -versión $version ' PublicConfiguration - $publicConfiguration

    foreach ($extension de $vm. MÁQUINA VIRTUAL. ResourceExtensionReferences) {si (($extension. Nombre de referencia - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - y ($extension. Nombre - eq $extensionName) '- y ($extension. Versión - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Clave = 'config.txt' break}}

    $vm | Actualización AzureVM </pre>

6. Importar el certificado (.pfx) en el equipo que tiene Visual Studio con Azure SDK para .NET instalado.
