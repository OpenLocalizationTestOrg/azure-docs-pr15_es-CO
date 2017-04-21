<properties
    pageTitle="Uso de PowerShell la clonación de la aplicación Web"
    description="Aprenda a clonar sus aplicaciones Web a aplicaciones Web mediante PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicación servicio de Azure App clonación usando PowerShell#

Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0 se ha agregado una nueva opción para AzureRMWebApp de nuevo que diera al usuario la capacidad para clonar una aplicación Web existente para una aplicación recién creada en una región diferente o en la misma región. Esto permitirá a los clientes a implementar una serie de aplicaciones a través de diferentes regiones rápida y fácilmente.

La clonación de la aplicación está actualmente sólo se admite para los planes de servicio de aplicación de nivel premium. La nueva característica utiliza las mismas limitaciones como característica de copia de seguridad de aplicaciones Web, vea [copia de seguridad de una aplicación web en el servicio de aplicación de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para obtener información acerca del uso de Azure Resource Manager base Azure PowerShell cmdlets para administrar aplicaciones Web comprobar [Azure el Administrador de recursos en función de los comandos de PowerShell para Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>La clonación de una aplicación existente ##

Escenario: Una aplicación web existente en la región sur Central de EE, al usuario le gustaría duplicar el contenido a una nueva aplicación web en la región Norte Central de EE. Esto puede realizarse mediante la versión de administrador de recursos de Azure del cmdlet de PowerShell para crear una nueva aplicación web con la opción - SourceWebApp.

Conocer el nombre del grupo de recursos que contiene el origen de la aplicación web, podemos utilizar el siguiente comando de PowerShell para obtener información de la aplicación web de origen (en este caso se denomina webapp de origen):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para crear un nuevo Plan de servicio de la aplicación, podemos utilizar comandos de AzureRmAppServicePlan de nuevo como en el ejemplo siguiente

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Mediante el comando New-AzureRmWebApp, podemos crear la nueva aplicación web en la región Norte Central de EE y asociarla a un nivel premium existente Plan de servicio de la aplicación, además podemos utilizar el mismo grupo de recursos como la aplicación web de origen, o definir un nuevo grupo de recursos, el siguiente demuestra que:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Para clonar una aplicación web existente incluyendo implementación asociado todas las ranuras, el usuario tendrá que utilizar el parámetro IncludeSourceWebAppSlots, el siguiente comando de PowerShell muestra el uso de este parámetro con el comando New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Para clonar una aplicación web existente dentro de la misma región, el usuario deberá crear un nuevo grupo de recursos y un nuevo servicio de aplicación previsto en la misma región y, a continuación, usando el siguiente comando de PowerShell para clonar la aplicación web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>La clonación de una aplicación existente a un entorno de servicio de la aplicación ##

Escenario: Una aplicación web existente en la región sur Central de EE, al usuario le gustaría duplicar el contenido a una nueva aplicación web a un entorno de servicio de aplicación existente (ASE).

Conocer el nombre del grupo de recursos que contiene el origen de la aplicación web, podemos utilizar el siguiente comando de PowerShell para obtener información de la aplicación web de origen (en este caso se denomina webapp de origen):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Conocer el nombre del ASE y el nombre del grupo de recursos al que pertenece el ASE, el usuario puede utilizar el comando New-AzureRmWebApp para crear la nueva aplicación web en el ASE existente, el siguiente demuestra que:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

El parámetro de ubicación es necesario debido a la razón heredado, pero en el caso de la creación de una aplicación en un ASE se omitirá. 

## <a name="cloning-an-existing-app-slot"></a>La clonación de una ranura de aplicación existente ##

Escenario: El usuario le gustaría clonar una ranura de la aplicación Web existente que ya sea una nueva aplicación Web o una nueva ranura de la aplicación Web. La nueva aplicación Web puede estar en la misma región como la ranura original de la aplicación Web o en una región distinta.

Conocer el nombre del grupo de recursos que contiene el origen de la aplicación web, podemos utilizar el siguiente comando de PowerShell para obtener información de la fuente web app ranura (en este caso se denomina origen webappslot) ligado a webapp de origen de la aplicación Web:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

A continuación muestra la creación de un clon de la aplicación web de origen a una nueva aplicación web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuración del Administrador de tráfico durante la clonación de una aplicación ##

Crear aplicaciones web consta de varias regiones y configurar Azure Traffic Manager para enrutar el tráfico a todas estas aplicaciones web, es un escenario importante n para asegurar que las aplicaciones de los clientes son altamente disponibles, al clonar una aplicación web existente tiene la opción de conectar ambas aplicaciones web a un nuevo perfil de administrador de tráfico o en uno existente, tenga en cuenta que el Administrador de recursos Azure sólo se admite la versión del Administrador de tráfico.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Crear un nuevo perfil de Traffic Manager durante la clonación de una aplicación ###

Escenario: El usuario le gustaría clonar una aplicación web a otra región, al configurar un perfil de administrador de administrador de recursos de Azure tráfico que incluyen tanto aplicaciones web. A continuación muestra la creación de un clon de la aplicación web de origen a una nueva aplicación web al configurar un nuevo perfil de Traffic Manager:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Agregar un nuevo clona Web App a un perfil existente de Traffic Manager ###

Escenario: El usuario ya tiene un perfil de administrador de administrador de recursos de Azure tráfico que le gustaría agregar tanto aplicaciones web como extremos. Para ello, primero es necesario montar el identificador del perfil de administrador de tráfico existente, necesitaremos el identificador de suscripción, el nombre del grupo de recursos y el nombre del perfil de administrador de tráfico existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Después de tener el id de administrador de tráfico, el siguiente muestra cómo crear un clon de la aplicación web de origen a una nueva aplicación web al agregarlos a un perfil existente de Traffic Manager:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restricciones actuales ##

Esta característica está actualmente en la vista previa, estamos trabajando para agregar nuevas capacidades con el tiempo, en la lista siguiente son las limitaciones conocidas de la versión actual de la clonación de la aplicación:

- No se clonan los ajustes de escala automática
- Copia de seguridad de la configuración no se clona
- No se clonan los valores de VNET
- Perspectivas de la aplicación no están automáticamente configuradas en la aplicación web de destino
- Fácil configuración de autenticación no se clona
- No se clonan extensión kudu
- No se clonan reglas de sugerencia
- Contenido de la base de datos no se clonan


### <a name="references"></a>Referencias ###
- [Administrador de recursos del Azure basado comandos de PowerShell para Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Clonación de la aplicación Web mediante el Portal de Azure](app-service-web-app-cloning-portal.md)
- [Hacer copia de seguridad de una aplicación web en el servicio de aplicación de Azure](web-sites-backup.md)
- [Soporte de Azure Resource Manager para vista previa de Azure Traffic Manager](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introducción al entorno de servicio de la aplicación](app-service-app-service-environment-intro.md)
- [Usando PowerShell Azure con Azure Resource Manager](../powershell-azure-resource-manager.md)
