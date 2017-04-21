<properties 
    pageTitle="Cómo configurar un servicio de nube (portal clásico) | Microsoft Azure" 
    description="Aprenda a configurar servicios de nube en Azure. Aprenda a actualizar la configuración de servicio de nube y configurar el acceso remoto a las instancias de la función." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-configure-cloud-services"></a>Cómo configurar servicios de nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-configure-portal.md)
- [Portal clásico de Azure](cloud-services-how-to-configure.md)

Puede configurar las opciones más utilizadas de un servicio de nube en el portal Azure clásico. O bien, si desea actualizar los archivos de configuración directamente, descargar un archivo de configuración de servicio para actualizar y, a continuación, cargar el archivo actualizado y actualizar el servicio de nube con los cambios de configuración. En ambos casos, las actualizaciones de configuración están expulsadas a todas las instancias de la función.

El portal clásico Azure también le permite [Habilitar conexión a Escritorio remoto para un rol de servicios de nube de Azure](cloud-services-role-enable-remote-desktop.md)

Azure sólo puede asegurar la disponibilidad de servicio del 99,95 por ciento durante las actualizaciones de configuración si tiene al menos dos instancias de rol para cada rol. Que permite que una máquina virtual procesar las solicitudes de cliente mientras se actualiza el otro. Para obtener más información, vea [Los acuerdos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Cambiar de un servicio de nube

1. En el [portal de clásico de Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio de nube y, a continuación, haga clic en **Configurar**.

    ![Página de configuración](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    En la página **Configurar** , puede configurar la supervisión, actualizar la configuración de la función y elija el sistema operativo invitado y familia para instancias de la función. 

2. En la **supervisión**, el nivel de supervisión en Verbose o mínima y configurar las cadenas de conexión de diagnósticos que se requieren para un seguimiento detallado.

3. Para las funciones de servicio (agrupadas por función), puede actualizar la siguiente configuración:
    
    >**Configuración**  
    >Modificar los valores de opciones de configuración varios que se especifican en los elementos *ConfigurationSettings* el servicio (.cscfg) del archivo de configuración.
    >
    >**Certificados**  
    >Cambiar la huella digital del certificado que se utiliza en el cifrado SSL para un rol. Para cambiar un certificado, primero debe cargar el nuevo certificado (en la página de **certificados** ). A continuación, actualice la huella digital en la cadena de certificado que se muestra en la configuración de la función.

4. En el **sistema operativo**, puede cambiar la familia de sistemas operativos o versión para instancias de la función, o elegir **automático** para habilitar las actualizaciones automáticas de la versión del sistema operativo actual. La configuración del sistema operativo se aplica a los roles de web y los roles de trabajo, pero no afectan a las máquinas virtuales.

    Durante la implementación, la versión más reciente del sistema operativo está instalada en todas las instancias de la función y los sistemas operativos se actualizan automáticamente de forma predeterminada. 
    
    Si necesita para que su servicio de nube para que se ejecute en una versión de sistema operativo diferente debido a los requisitos de compatibilidad en el código, puede elegir una familia de sistemas operativos y la versión. Cuando se selecciona una versión de sistema operativo específico, se suspenden las actualizaciones automática del sistema operativo para el servicio de nube. Debe asegurarse de que los sistemas operativos de recibir actualizaciones.
    
    Si resuelve todos los problemas de compatibilidad que tienen sus aplicaciones con la versión más reciente del sistema operativo, puede habilitar actualizaciones automáticas del sistema operativo estableciendo la versión de sistema operativo en **automático**. 
    
    ![Configuración de sistema operativo](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para guardar la configuración y empuja a las instancias de la función, haga clic en **Guardar**. (Haga clic en **Descartar** para cancelar los cambios.) **Guardar** y **Descartar** se agregan a la barra de comandos después de cambiar un valor.

## <a name="update-a-cloud-service-configuration-file"></a>Actualizar un archivo de configuración de servicio de nube

1. Descargar un archivo de configuración del servicio de nube (.cscfg) con la configuración actual. En la página **Configurar** el servicio de nube, haga clic en **Descargar**. A continuación, haga clic en **Guardar**, o haga clic en **Guardar como** para guardar el archivo.

2. Después de actualizar el archivo de configuración del servicio, cargar y aplicar las actualizaciones de configuración:

    1. En la página **Configurar** , haga clic en **cargar**.
    
        ![Cargar configuración](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. En el **archivo de configuración**, utilice **Examinar** para seleccionar el archivo actualizado .cscfg.
    
    3. Si su servicio de nube contiene las funciones que tienen sólo una instancia, seleccione la casilla de verificación **Aplicar configuración, incluso si uno o más roles contienen una sola instancia** para habilitar las actualizaciones de configuración de las funciones continuar.
    
        A menos que defina al menos dos instancias de todas las funciones, Azure no puede garantizar al menos el 99,95 por ciento la disponibilidad de su servicio de nube durante las actualizaciones de configuración de servicio. Para obtener más información, vea [Los acuerdos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).
    
    4. Haga clic en **Aceptar** (marca). 


## <a name="next-steps"></a>Próximos pasos

* Aprenda cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administrar el servicio de nube](cloud-services-how-to-manage.md).
* [Habilitar conexión a Escritorio remoto para un rol de servicios de nube de Azure](cloud-services-role-enable-remote-desktop.md)
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).
