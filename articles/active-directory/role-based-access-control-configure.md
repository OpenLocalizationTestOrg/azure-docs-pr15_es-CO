<properties
    pageTitle="Utilizar control de acceso basado en roles en el portal de Azure | Microsoft Azure"
    description="Comience a trabajar en la administración del acceso con Control de acceso basado en roles en el Portal de Azure. Utilice las asignaciones de funciones para asignar permisos a los recursos."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Utilice las asignaciones de funciones para administrar el acceso a los recursos de suscripción de Azure

> [AZURE.SELECTOR]
- [Administrar el acceso por usuario o grupo](role-based-access-control-manage-assignments.md)
- [Administrar el acceso a recursos](role-based-access-control-configure.md)

Azure Role-Based Access Control (RBAC) permite la administración de acceso específico para Azure. Utilizando RBAC, puede conceder sólo la cantidad de acceso que los usuarios necesitan para realizar sus trabajos. Este artículo le ayuda a poner en funcionamiento con RBAC en el portal de Azure. Si desea obtener más detalles sobre cómo RBAC le ayuda a administrar el acceso, consulte [¿Qué es el Control de acceso basado en funciones](role-based-access-control-what-is.md).

## <a name="view-access"></a>Acceso a vista
Puede ver quién tiene acceso a un recurso, el grupo de recursos o la suscripción de su placa principal en el [portal de Azure](https://portal.azure.com). Por ejemplo, queremos ver quién tiene acceso a uno de nuestros grupos de recursos:

1. Seleccione los **grupos de recursos** en la barra de navegación de la izquierda.  
    ![Grupos de recursos - icono](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Seleccione el nombre del grupo de recursos desde el blade de **grupos de recursos** .
3. Seleccione **el control de acceso (IAM)** desde el menú de la izquierda.  
4. El módulo de control de acceso enumera todos los usuarios, grupos y aplicaciones que se ha concedido acceso al grupo de recursos.  

    ![Blade de usuarios - vs heredados asignado acceso de pantalla](./media/role-based-access-control-configure/view-access.png)

Observe que algunos usuarios eran **asignado** acceso mientras otros **heredados** . Acceso se asigna específicamente al grupo de recursos o heredado de una asignación a la suscripción del principal.

> [AZURE.NOTE] Suscripción clásica y administradores de co se consideran propietarios de la suscripción en el nuevo modelo RBAC.


## <a name="add-access"></a>Agregar acceso
Conceder acceso a desde el recurso, grupo de recursos o suscripción que es el ámbito de la asignación de roles.

1. Seleccione **Agregar** en el módulo de control de acceso.  
2. Seleccione el rol que desea asignar desde el blade de **Seleccionar una función** .
3. Seleccione el usuario, grupo o aplicación en el directorio que desea conceder acceso a. Puede buscar en el directorio con los identificadores de objeto, direcciones de correo electrónico y nombres para mostrar.  

    ![Agregar hoja de usuarios: captura de pantalla de búsqueda](./media/role-based-access-control-configure/grant-access2.png)

4. Seleccione **Aceptar** para crear la asignación. El menú emergente **Agregar usuario** realiza un seguimiento del progreso.  
    ![Agregar barra de progreso de usuario - captura de pantalla](./media/role-based-access-control-configure/addinguser_popup.png)

Después de agregar correctamente una asignación de función, aparecerá en la hoja de **los usuarios** .

## <a name="remove-access"></a>Quitar el acceso

1. Seleccione la asignación de función en el módulo de control de acceso.
2. En la hoja detalles de asignación, seleccione **Quitar** .  
3. Seleccione **Sí** para confirmar la eliminación.  
    ![Placa de usuarios: quitar de la captura de pantalla de papel](./media/role-based-access-control-configure/remove-access1.png)

No se puede quitar las asignaciones heredadas. En la imagen siguiente, observe que el botón Quitar está atenuado. En su lugar, mire el detalle de **Asignado** . Vaya al recurso de la lista para quitar la asignación de roles.

![Placa de usuarios: deshabilita el acceso heredadas quita botón de pantalla](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Otras herramientas para administrar el acceso
Puede asignar funciones y administrar el acceso con los comandos de Azure RBAC en herramientas que no sean el portal de Azure.  Siga los vínculos para obtener más información acerca de los requisitos previos y empezar a trabajar con los comandos de RBAC de Azure.

- [PowerShell de Azure](role-based-access-control-manage-access-powershell.md)
- [Interfaz de línea de comandos de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Próximos pasos
- [Crear un informe de histórico de cambios de acceso](role-based-access-control-access-change-history-report.md)
- Consulte las [funciones integradas de RBAC](role-based-access-built-in-roles.md)
- Definir sus propias [funciones personalizadas en Azure RBAC](role-based-access-control-custom-roles.md)
