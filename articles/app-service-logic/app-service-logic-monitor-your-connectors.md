<properties
    pageTitle="Administrar y supervisar los conectores y la API de aplicaciones de servicio de la aplicación | Microsoft Azure"
    description="Ver el rendimiento de los conectores y API de aplicaciones en aplicaciones de lógica; arquitectura de microservicios"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Administrar y supervisar los conectores y API aplicaciones integradas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema de lógica apps 2014-12-01-vista previa.

Crea una App API integrada. ¿Y ahora qué?

En Azure, cada App API es un sitio web independiente alojado en Azure. Como resultado, puede ver fácilmente el número de solicitudes se realiza y ver la cantidad de datos se está utilizando el conector. También puede tu App API de copia de seguridad, crear alertas, habilitar la seguridad de estaño y agregar los usuarios y funciones.

En este tema se describe algunas de las distintas opciones para administrar tus App API.

Para ver estas características integradas, abra su App API en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si es la App API en su estribor, selecciónelo para abrir las propiedades. Puede también seleccionar **Examinar**, seleccione **API aplicaciones**y, a continuación, seleccione su App API:

![][browse]

## <a name="see-the-properties-you-entered"></a>Vea las propiedades que escribió

Al abrir la App API, hay varias características y tareas disponibles:

![][settings]

Puedes:

- **Configuración** muestra información específica sobre la App API, incluidos los detalles de su suscripción y enumera los usuarios que tienen acceso a la API de la aplicación. También puede aumentar o disminuir el número de instancias de su App de API mediante la característica de escala; entre otras características.
- Utilice los botones de **Start** y **Stop** para controlar la App API.
- Cuando se realizan actualizaciones de productos en los archivos subyacentes utilizados por su App API, puede hacer clic en **Actualizar** para obtener las versiones más recientes. Por ejemplo, si existe una revisión o una actualización de seguridad publicada por Microsoft, al hacer clic en **Actualizar** automáticamente actualiza su App API para incluir esta corrección.
- Seleccione **El Plan de cambio** para actualizar o degradar basándose en el uso de datos de la API de App. También puede utilizar esta característica para ver el uso de datos.
- Al crear un conector que tiene tablas, como el conector SQL, puede escribir un nombre de tabla al que conectarse. Un esquema basado en la tabla es automáticamente creado y disponible al hacer clic en **Descargar esquemas**. A continuación, puede utilizar este esquema descargado para crear una transformación o una asignación.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Cambiar el conector o valores de configuración de API introducidos

Después de configurar o crear construido conector, puede cambiar los valores introducidos. Por ejemplo, si ha configurado el conector SQL y desea cambiar el nombre de tabla o nombre de SQL Server, puede hacerlo en la hoja de API App para el conector.

Los pasos incluyen:

1. Abra el conector o API App. Al hacerlo, se abre el módulo API App.
2. En **Essentials**, haga clic en el hipervínculo en la propiedad Host. El hipervínculo se denomina algo como *slackconnector* o *microsoftsqlconnector123*:

    ![][apiapphost]

3. En el módulo API App Host, seleccione **configuración**. En la hoja de configuración, seleccione **Configuración de la aplicación**. Los valores de configuración se muestran en **Configuración de la aplicación**:

    ![][hostsettings]

4. Haga clic en la opción que desea cambiar, escriba el valor nuevo y **Guardar** los cambios.


## <a name="install-the-hybrid-connection-manager---optional"></a>Instalar al administrador de conexión híbrida - opcional

![][hcsetup]

Híbrido de Connection Manager le ofrece la capacidad para conectarse a un sistema local, como SQL Server o SAP. Esta conectividad híbrida utiliza Bus de servicios de Azure para conectarse y controlar la seguridad entre los recursos de Azure y los recursos locales.

Consulte [con el Administrador de conexión híbrido en Azure de la aplicación de servicio](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Híbrido de Connection Manager sólo es necesario si se está conectando a un recurso local detrás del servidor de seguridad. Si no se conecta a un sistema local, el híbrido de Connection Manager no puede mostrarse en su hoja de conector.

## <a name="monitor-the-performance"></a>Supervisar el rendimiento
Métricas de rendimiento se están integradas y se incluyen con cada App API crea. Estas métricas son específicas a su API App hospedada en Azure. Métricas de ejemplo:

![][monitoring]

Puedes:

- Seleccione los **errores y solicitudes** para agregar mediciones de performance diferentes, incluidos los códigos de error HTTP conocido como 200, 400 o 500 códigos de estado HTTP. También puede ver los tiempos de respuesta, ver cuántas peticiones se realizan en el App API y vea cuántos datos viene en y cuántos datos se apaga. Según las métricas de rendimiento, puede crear alertas de correo electrónico si una métrica supera un umbral de su elección.
- En **uso**, puede ver cuánta **CPU** usa el App API, revisar la **Cuota de uso** actual en MB y ver el uso máximo de datos basándose en su nivel de costo. **Estimado gastar** puede ayudarle a determinar el coste potencial de ejecutar su App API.
- Seleccionar **procesos** para abrir el Explorador de procesos. Muestra las instancias de la web y sus propiedades, incluyendo el uso de memoria y el recuento de subprocesos.

Con estas herramientas, puede determinar si el Plan de servicio de la aplicación deben ampliar o reducido, en función de las necesidades de su negocio. Estas características están integradas en el portal con sin necesidad de herramientas adicionales.

## <a name="control-the-security"></a>Control de la seguridad

API aplicaciones utilizar la seguridad basada en funciones. Estas funciones se aplican a toda la experiencia Azure, incluyendo aplicaciones de API y otros recursos de Azure. Las funciones incluyen:

Función | Descripción
--- | ---
Propietario | Tiene acceso completo a la experiencia de administración y puede proporcionar acceso a otros usuarios o grupos.
Colaborador | Tienen acceso completo a la experiencia de administración. No puede dar acceso a otros usuarios o grupos.
Lector | Puede ver todos los recursos excepto secretos.
Administrador de acceso de usuario | Puede ver todos los recursos, crear y administrar roles y crear y administrar admiten entradas.

Vea [control de acceso basado en roles en el portal de Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Fácilmente puede agregar usuarios y asignarles funciones específicas a su App de API. El portal muestra los usuarios que tienen acceso y su rol asignado:

![][access]  

- Seleccione **los usuarios** a agregar un usuario, asignar un rol y quitar un usuario.
- Seleccione **las funciones** para ver todos los usuarios de una función específica, agregar un usuario a una función y quitar un usuario de una función.


## <a name="more-good-stuff"></a>Más cosas buenas
- Seleccione la **definición de la API** para abrir el archivo de Pavoneo creados automáticamente para la aplicación específica de API.
- Seleccione **dependencias** para ver los archivos requeridos por su App API. Por ejemplo, si utiliza el conector SAP, instalar algunos archivos adicionales en el Administrador de conexión de local híbrido. Estas dependencias se muestran en el blade de API de la aplicación.

>[AZURE.IMPORTANT] Al abrir las propiedades de la aplicación API y busque en **Essentials**, hay vínculos de **Host** y la **puerta de enlace** que abren nuevas hojas:
>
> ![][host]
>
>Estas propiedades son específicas de la página Web que hospeda su App API. Cuando se utiliza un conector o API App integrados, la mayoría de estas propiedades no se aplica realmente y se recomienda que no actualice estas propiedades. Si creó su propia App API en Visual Studio y lo implementó a su suscripción de Azure, a continuación, puede utilizar los módulos de Host y la puerta de enlace. <br/><br/>


>[AZURE.NOTE] Para comenzar con la lógica de aplicaciones antes de contratar una cuenta de Azure, vaya a [Probar la aplicación lógica](https://tryappservice.azure.com/?appservice=logic). Puede crear una aplicación lógica de corta duración starter. No requeridas de tarjetas de crédito y sin compromisos.

## <a name="read-more"></a>Leer más

[Supervisar las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Conectores y la lista de aplicaciones API de servicio de la aplicación](app-service-logic-connectors-list.md)<br/>
[Control de acceso basado en roles en el portal de Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[Mediante el Administrador de conexión híbrido en servicio de la aplicación de Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
