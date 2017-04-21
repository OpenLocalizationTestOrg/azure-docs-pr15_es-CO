<properties
    pageTitle="Pila de Azure App servicio Technical Preview 1 antes de empezar | Microsoft Azure"
    description="Pasos para finalizar antes de implementar aplicaciones Web en pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Antes de empezar a trabajar con aplicaciones de Azure pila Web

> [AZURE.NOTE] La información siguiente sólo se aplica a las implementaciones de Azure pila TP1.

Tendrá algunos elementos para instalar aplicaciones Web de pila de Azure:

- Una implementación completa de [Azure pila Technical Preview 1](azure-stack-run-powershell-script.md)
- Hay suficiente espacio en el sistema de pila de Azure para un pequeño grupo de aplicaciones Web de pila de Azure.  El espacio necesario es aproximadamente 20GB de espacio en disco.
- [Un servidor que está ejecutando SQL Server](#SQL-Server).

>[AZURE.NOTE] Los pasos siguientes, que todos tienen lugar en la máquina cliente virtual.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Antes de implementar aplicaciones Web de pila de Azure

Para implementar un proveedor de recursos, debe ejecutar el Environment(ISE) de Scripting integrado de PowerShell como administrador. Por este motivo, debe permitir las cookies y JavaScript en el perfil de Internet Explorer que usas para iniciar sesión en Active Directory de Azure.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Desactivar la seguridad mejorada de Internet Explorer

1.  Iniciar sesión en el equipo de pila de Azure prueba de concepto (POC) como **AzureStack o administrador**y, a continuación, abra el **Administrador del servidor**.
2.  Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.
3.  Iniciar sesión en el equipo virtual ClientVM.AzureStack.local como administrador y, a continuación, abra el **Administrador del servidor**.
4.  Desactivar **La configuración de seguridad mejorada de Internet Explorer** para administradores y usuarios.

## <a name="enable-cookies"></a>Habilitar las cookies

1.  Seleccione **Inicio**, > **todas las aplicaciones**, > **Accesorios de Windows**. Haga clic en **Internet Explorer** > **más** > **Ejecutar como administrador**.
2.  Si se le pide, seleccione **recomienda utilizar seguridad**y, a continuación, seleccione **Aceptar**.
3.  En Internet Explorer, seleccione **Herramientas** (el icono de engranaje) > **Opciones de Internet** > **Privacy** > **Avanzadas**.
4.  Seleccione **Avanzadas**. Asegúrese de que ambas casillas de verificación de **aceptación** están seleccionados y, a continuación, seleccione **Aceptar** y en **Aceptar** nuevamente.
5.  Cierre Internet Explorer y reinicie PowerShell ISE como administrador.

## <a name="install-the-latest-version-of-azure-powershell"></a>Instale la última versión de PowerShell de Azure

1.  Iniciar sesión como **Administrador/AzureStack**en el equipo de prueba de concepto de pila de Azure.
2.  Utilizar conexión a Escritorio remoto para iniciar sesión en el equipo virtual ClientVM.AzureStack.local como administrador.
3.  Abra el **Panel de Control** y seleccione **desinstalar un programa**. 
4.  Haga clic en **Microsoft Azure PowerShell - noviembre de 2015** y seleccione **desinstalar**.
5.  Termina después de la desinstalación, reinicie el equipo virtual ClientVM.AzureStack.local
6.  Descargue e instale la más reciente [PowerShell de Azure](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>SQL Server

De forma predeterminada, el instalador de aplicaciones Web de pila de Azure está configurado para utilizar el SQL Server que se instala junto con el proveedor de recursos de SQL de pila de Azure. Cuando se instala el proveedor de recursos de SQL Server (SQL RP) asegúrese de que tomar nota del nombre de usuario de administrador de base de datos y contraseña. Necesita tanto al instalar aplicaciones Web de pila de Azure.
Nota: También tendrá la opción de implementar y usar otro servidor para que se ejecute SQL Server. Puede elegir la instancia de SQL Server a utilizar cuando complete las opciones del instalador de aplicaciones Web de pila de Azure.
