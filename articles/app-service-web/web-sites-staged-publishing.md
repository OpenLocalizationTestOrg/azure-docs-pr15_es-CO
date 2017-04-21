<properties
    pageTitle="Configurar entornos de aplicaciones web en Azure App servicio de ensayo"
    description="Aprenda a utilizar la publicación por fases para aplicaciones web en el servicio de aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Configurar entornos de aplicaciones web en Azure App servicio de ensayo
<a name="Overview"></a>

Al implementar su aplicación web al [Servicio de la aplicación](http://go.microsoft.com/fwlink/?LinkId=529714), puede implementar en una ranura de implementación independiente en lugar de la ranura de producción de forma predeterminada cuando se ejecuta en el modo de plan **estándar** o servicio **Premium** de la aplicación. Ranuras de implementación son aplicaciones web vivo realmente con sus propios nombres de host. Elementos de contenido y configuraciones de la aplicación Web se pueden intercambiar entre dos ranuras de implementación, que incluye una ranura para la producción. Implementar su aplicación en una ranura de implementación tiene las siguientes ventajas:

- Puede validar cambios de la aplicación web en una ranura de implementación provisional antes de intercambio de la ranura de la producción.

- Implementar una aplicación web en una ranura primero e intercambiando producción garantiza que todas las instancias de la ranura se calientan antes de que se intercambia en producción. Esto elimina el downtime al implementar la aplicación web. La redirección de tráfico es perfecta y ninguna solicitud se quita como resultado de operaciones de intercambio. Este flujo de trabajo completo puede ser automatizado mediante la configuración [Automática de intercambio](#configure-auto-swap-for-your-web-app) cuando no es necesaria la validación previa de intercambio.

- Después de un intercambio, la ranura con la aplicación web ensayado previamente tiene ahora la aplicación web de producción anterior. Si los cambios que se intercambian en la ranura de la producción son no tal como esperaba, puede realizar el mismo swap inmediatamente para obtener su "último sitio conocido" Atrás.

Distinto número de ranuras de implementación que admite cada modo de plan de servicio de la aplicación. Para averiguar el número de ranuras es compatible con el modo de su aplicación web, vea [Tarifas de servicio de la aplicación](/pricing/details/app-service/).

- Cuando su aplicación web tiene varias ranuras, no puede cambiar el modo.

- Escala no está disponible para las ranuras que no son de producción.

- Administración de recursos vinculados no se admite para las ranuras que no son de producción. En el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) sólo, puede evitar este impacto potencial en una ranura de producción moviendo temporalmente la ranura no es de producción a otro modo de plan de servicio de la aplicación. Tenga en cuenta que la ranura no es de producción una vez más debe compartir el mismo modo con la ranura de producción antes de que puede intercambiar las dos ranuras.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Agregar una ranura de implementación para una aplicación web ##

La aplicación web debe ejecutarse en el modo **estándar** o **Premium** en orden para poder habilitar varias ranuras de implementación.

1. En el [Portal de Azure](https://portal.azure.com/), abra el módulo de su aplicación web.
2. Haga clic en **configuración**y, a continuación, haga clic en **las ranuras de la implementación**. El blade de **ranuras de implementación** , haga clic en **Agregar ranura**.

    ![Agregar una nueva ranura de implementación][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Si la aplicación web no está en el modo **estándar** o **Premium** , recibirá un mensaje que indica los modos compatibles para habilitar la publicación por fases. En este punto, tiene la opción de seleccionar **Upgrade** y vaya a la ficha de **escala** de la aplicación web antes de continuar.

2. En la hoja de **Agregar una ranura** , asigne un nombre a la ranura y seleccione si desea duplicar la configuración de la aplicación web desde otra ranura de implementación existente. Haga clic en la casilla de verificación para continuar.

    ![Origen de configuración][ConfigurationSource1]

    La primera vez que agrega una ranura, sólo tenemos dos opciones: configuración de clon de la ranura de forma predeterminada en producción o no.

    Después de haber creado varias ranuras, podrá Clonar configuración desde una ranura distinta a la de producción:

    ![Orígenes de configuración][MultipleConfigurationSources]

5. En el módulo de **las ranuras de la implementación** , haga clic en la ranura de implementación para abrir un módulo de la ranura, con un conjunto de métricas y configuración al igual que cualquier otra aplicación web. **Your-Web-App-Name-Deployment-slot-Name** aparecerá en la parte superior de la hoja para recordarle que está viendo la ranura de la implementación de.

    ![Título de la ranura de implementación][StagingTitle]

5. Haga clic en la dirección URL de la aplicación en el módulo de la ranura. Observe la ranura de implementación tiene su propio nombre de host y también es una aplicación de live. Para limitar el acceso del público a la ranura de la implementación, vea [Aplicación servicio Web App: bloquear el acceso a las ranuras de la implementación de producción no web](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

No hay contenido después de la creación de la ranura de implementación. Puede implementar en la ranura de una rama de repositorio diferente o un repositorio totalmente diferente. También puede cambiar la configuración de la ranura. Utilizar las credenciales del perfil o implementación publicar asociadas con la ranura de la implementación de actualizaciones de contenido.  Por ejemplo, puede [publicar en esta ranura con git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Configuración para las ranuras de la implementación ##
Al clonar configuración desde otra ranura de implementación, la configuración clonada es editable. Además, algunos elementos de configuración seguirá el contenido a través de un intercambio (no ranura específico) mientras que otros elementos de configuración permanecerá en la misma ranura después de un intercambio (ranura específica). Las listas siguientes muestran la configuración que se cambiará al intercambiar las ranuras.

**Configuración que se intercambia**:

- Configuración general: como la versión de framework, 32/64 bits, Web sockets
- Configuración de la aplicación (puede configurarse para ajustarse a una ranura)
- Cadenas de conexión (puede configurarse para ajustarse a una ranura)
- Asignaciones de controlador
- Configuración de supervisión y diagnóstico
- Contenido de WebJobs

**Configuración que no se han intercambiado**:

- Extremos de publicación
- Nombres de dominio personalizado
- Enlaces y certificados SSL
- Configuración de escala
- Programadores WebJobs

Para configurar una cadena de conexión o configuración de la aplicación ceñirse a una ranura (no intercambiada), tener acceso a la hoja de **Configuración de la aplicación** para una ranura específica, seleccione el cuadro de **Configuración de la ranura** para los elementos de configuración que deben atenerse a la ranura. Tenga en cuenta que un elemento de configuración marcado como ranura específica tiene el efecto de establecer ese elemento como swap no en todas las ranuras de implementación asociadas con la aplicación web.

![Configuración de ranura][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Para intercambiar las ranuras de la implementación ##

>[AZURE.IMPORTANT] Antes de cambiar de una aplicación web de una ranura de implementación en producción, asegúrese de que todas las configuraciones específicas de ranura no están configuradas exactamente como desea que en el destino de intercambio.

1. Para intercambiar las ranuras de la implementación, haga clic en el botón **Swap** en la barra de comandos de la aplicación web o en la barra de comandos de una ranura de implementación. Asegúrese de que el origen de intercambio y el destino de intercambio establecidos correctamente. Normalmente, el destino de intercambio sería la ranura de la producción.  

    ![Botón intercambiar][SwapButtonBar]

3. Haga clic en **Aceptar** para completar la operación. Al finalizar la operación, se han intercambiado las ranuras de la implementación.

## <a name="configure-auto-swap-for-your-web-app"></a>Configurar el intercambio automático para las aplicaciones web ##

Intercambio automático optimiza los escenarios de DevOps donde desea implementar continuamente su aplicación web con cero arranque en frío y sin downtime para los clientes finales de la aplicación web. Cuando una ranura de implementación se configura para el intercambio automático en producción, cada vez que presione la actualización del código para esa ranura, servicio de aplicación intercambie automáticamente la aplicación web en producción después de que ya haya calentado en la ranura.

>[AZURE.IMPORTANT] Cuando se habilita para una ranura de intercambio automático, asegúrese de que la configuración de la ranura es exactamente la configuración destinada a la ranura de destino (normalmente en la ranura de producción).

Es fácil configurar el intercambio automático de una ranura. Siga los pasos siguientes:

1. En la hoja de **Implementación ranuras** , seleccione un intervalo de no producción, haga clic en **Todas las configuraciones** para el módulo de la ranura.  

    ![][Autoswap1]

2. Haga clic en **configuración de la aplicación**. Seleccione **activado** para el **Intercambio automático**, seleccione la ranura de destino deseada en la **Ranura de intercambio automático**y haga clic en **Guardar** en la barra de comandos. Asegúrese de que la configuración de la ranura es exactamente la configuración destinada a la ranura de destino.

    La ficha **notificaciones** parpadeará en color verde **éxito** una vez completada la operación.

    ![][Autoswap2]

    >[AZURE.NOTE] Para probar el intercambio automático para las aplicaciones web, se puede seleccionar una ranura de destino no es de producción en la **Ranura de intercambio automático** para familiarizarse con la característica.  

3. Ejecutar una inserción de código para esa ranura de implementación. Intercambio automático ocurrirá después de un breve tiempo y la actualización se reflejarán en la dirección URL de la zona de destino.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Utilice el intercambio de múltiples fase para las aplicaciones web ##

Intercambio de múltiples fase está disponible para simplificar la validación en el contexto de elementos de configuración diseñada para ajustarse a una ranura como cadenas de conexión. En estos casos, puede ser útil aplicar estos elementos de configuración del destino de intercambio al origen de intercambio y validar antes de intercambio realmente surta efecto. Una vez que los elementos de configuración de destino de intercambio se aplican al origen de intercambio son realizar el intercambio o revertir a la configuración original del origen de intercambio que también tiene el efecto de cancelar el intercambio de las acciones disponibles. Ejemplos de los cmdlets de PowerShell de Azure disponibles para intercambio de múltiples fase se incluyen en los cmdlets de PowerShell de Azure para la sección de las ranuras de la implementación.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Para deshacer una aplicación de producción después de la permutación ##

Si los errores se identifican en la producción después de un intercambio de ranura, revertir las ranuras a su estado previo intercambio intercambiando las mismas dos posiciones inmediatamente.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Calentamiento personalizado antes de swap ##

Algunas aplicaciones pueden requerir acciones de calentamiento personalizado. El elemento de configuración de applicationInitialization en el archivo web.config permite especificar acciones de inicialización personalizadas para realizar antes de que se reciba una solicitud. La operación de intercambio esperará esta preparación personalizada completar. Aquí es un fragmento de web.config de ejemplo.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Para eliminar una ranura de implementación##

En la hoja para una ranura de implementación, haga clic en **Eliminar** en la barra de comandos.  

![Eliminar una ranura de implementación][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure cmdlets de PowerShell para ranuras de implementación

PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure a través de Windows PowerShell, incluida la compatibilidad con administración de ranuras de implementación de la aplicación web en servicio de la aplicación de Azure.

- Para obtener información sobre la instalación y configuración de PowerShell de Azure y autenticación Azure PowerShell con su suscripción de Azure, consulte [cómo instalar y configurar Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Crear la aplicación web

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Crear una ranura de implementación para una aplicación web

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Iniciar intercambio de múltiples fase y aplicar configuración de destino ranura a ranura de origen

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Revertir la primera fase de intercambio de múltiples fase y restaurar la configuración de orígenes de ranura

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Ranuras de la implementación de intercambio

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Eliminar la ranura de la implementación

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Comandos de interfaz de línea de comandos (CLI Azure) Azure para ranuras de implementación

La CLI de Azure proporciona comandos multiplataforma para trabajar con Azure, incluido el soporte para ranuras de la implementación de la aplicación Web de administración.

- Para obtener instrucciones sobre la instalación y configuración de la CLI de Azure, incluida información sobre cómo conectar el CLI de Azure a su suscripción de Azure, vea [instalar y configurar el CLI de Azure](../xplat-cli-install.md).

-  Para obtener una lista de los comandos disponibles para el servicio de aplicación Azure en el CLI de Azure, llame a `azure site -h`.

----------
### <a name="azure-site-list"></a>lista de sitios de Azure
Para obtener información acerca de las aplicaciones web de la suscripción actual, llame a la **lista de sitios de azure**, como en el ejemplo siguiente.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>Crear sitio de Azure
Para crear una ranura de implementación, llame al **sitio azure crear** y especificar el nombre de una aplicación web existente y el nombre de la ranura a crear, como en el ejemplo siguiente.

`azure site create webappslotstest --slot staging`

Para habilitar el control de origen de la nueva ranura, utilice la opción **--git** , como en el ejemplo siguiente.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>intercambio de sitio de Azure
Para que la implementación actualizada la ranura de la aplicación de producción, utilice el comando **sitio azure de intercambio** para realizar una operación de intercambio, como en el ejemplo siguiente. La aplicación de producción no experimentará ningún período de inactividad ni se someterán a un arranque en frío.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>Eliminar sitio de Azure
Para eliminar una ranura de implementación que ya no es necesario, utilice el comando **Eliminar sitio azure** , como en el ejemplo siguiente.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de contratar una cuenta de Azure, vaya a [Tratar de servicio de la aplicación](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter de corta duración en servicio de la aplicación. No hay tarjetas de crédito requeridos; No hay compromisos.

## <a name="next-steps"></a>Próximos pasos ##
[Azure App servicio Web App: bloquear el acceso web a las ranuras de la implementación de no producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versión de prueba gratuita de Microsoft Azure](/pricing/free-trial/)

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web al servicio de la aplicación: [servicio de aplicación de Azure y su impacto en los servicios existentes de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
