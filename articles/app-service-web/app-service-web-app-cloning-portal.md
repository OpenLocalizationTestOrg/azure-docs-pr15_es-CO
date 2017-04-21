<properties
    pageTitle="Clonación de la aplicación Web mediante el Portal de Azure"
    description="Aprenda a clonar sus aplicaciones Web a aplicaciones Web mediante el Portal de Azure."
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
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>App de Azure servicio App clonación usando Azure Portal#

La función de clonación en [Azure App servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) permite clonar fácilmente aplicaciones web existentes a una aplicación recién creada en una región diferente o en la misma región. Esto permitirá a los clientes a implementar una serie de aplicaciones a través de diferentes regiones rápida y fácilmente.

La clonación de la aplicación está actualmente sólo se admite para los planes de servicio de aplicación de nivel premium. La nueva característica utiliza las mismas limitaciones como característica de copia de seguridad de aplicaciones Web, vea [copia de seguridad de una aplicación web en el servicio de aplicación de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>La clonación de una aplicación existente ##

La aplicación web debe estar ejecutando en el modo de **prima** en orden para crear un clon de la aplicación web.

1. En el [Portal de Azure](https://portal.azure.com/), abra el módulo de su aplicación web.
2. Haga clic en **Herramientas**. La hoja de **Herramientas** , haga clic en **Clon de la aplicación**.

    ![][1]

    > [AZURE.NOTE]
    > Si la aplicación web no está en el modo **Premium** , recibirá un mensaje que indica los modos compatibles para la clonación de la aplicación. En este punto, tiene la opción de seleccionar **Actualizar**.
    
3. En la hoja de **Clon de la aplicación** proporcione el nombre de la nueva aplicación web, el grupo de recursos y el Plan de servicio de la aplicación. También el usuario podrá elegir si desea clonar un número de la configuración de la aplicación web de origen o no.

    ![][2]

4. Tras hacer clic en **crear** la plataforma empieza a trabajar en la creación de un clon de la aplicación web de origen.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>La clonación de una aplicación existente a un entorno de servicio de la aplicación##

En la hoja de **Clon de la aplicación** cliente tendrá la opción de elegir un grupo de aplicaciones en un entorno de servicio de la aplicación existente.

## <a name="current-restrictions"></a>Restricciones actuales ##

Esta característica está actualmente en la vista previa, estamos trabajando para agregar nuevas capacidades con el tiempo, en la lista siguiente son las limitaciones conocidas de la compatibilidad actual de clonación de la aplicación de Portal de Azure:

- Configuración de Azure Traffic Manager no se clona
- No se clonan los ajustes de escala automática
- Copia de seguridad de la configuración no se clona
- No se clonan los valores de VNET
- Perspectivas de la aplicación no están automáticamente configuradas en la aplicación web de destino
- Fácil configuración de autenticación no se clona
- No se clonan extensión kudu
- No se clonan reglas de sugerencia
- Contenido de la base de datos no se clonan


### <a name="references"></a>Referencias ###
- [Uso de PowerShell la clonación de la aplicación Web](app-service-web-app-cloning.md)
- [Hacer copia de seguridad de una aplicación web en el servicio de aplicación de Azure](web-sites-backup.md)
- [Cómo crear un entorno de servicio de la aplicación](app-service-web-how-to-create-an-app-service-environment.md)
- [Crear una aplicación web en un entorno de servicio de la aplicación](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introducción al entorno de servicio de la aplicación](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png