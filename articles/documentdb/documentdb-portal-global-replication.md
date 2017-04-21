<properties
    pageTitle="Replicación de base de datos global DocumentDB | Microsoft Azure"
    description="Aprenda a administrar la replicación de la cuenta de DocumentDB a través del portal de Azure global."
    services="documentdb"
    keywords="base de datos global, replicación"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Cómo realizar la replicación de la base de datos global de DocumentDB mediante el portal de Azure

Aprenda a usar el portal de Azure para replicar datos en varias áreas para la disponibilidad global de datos en DocumentDB de Azure.

Para obtener información acerca de la base de datos global de cómo funciona la replicación en DocumentDB, vea [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md). Para obtener información acerca de cómo realizar la replicación de la base de datos global mediante programación, vea [desarrollar con cuentas de DocumentDB consta de varias regiones](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribución global de las bases de datos DocumentDB está generalmente disponible y automáticamente habilitada para las cuentas DocumentDB recién creadas. Estamos trabajando para habilitar la distribución global de todas las cuentas existentes, pero mientras tanto, si desea una distribución global habilitada en tu cuenta, por favor [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y se le habilita para ahora.

## <a id="addregion"></a>Agregar regiones de base de datos global

DocumentDB está disponible en la mayoría de las [regiones de Azure] [azureregions]. Después de seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos, puede asociar una o más regiones (dependiendo de las necesidades de distribución global y nivel de coherencia predeterminada).

1. En el [portal de Azure](https://portal.azure.com/), en el Jumpbar, haga clic en **Cuentas de DocumentDB**.
2. En la hoja de **DocumentDB cuenta** , seleccione la cuenta de base de datos para modificar.
3. En la hoja de la cuenta, haga clic en **replicar datos globalmente** en el menú.
4. En el blade de **replicar datos globalmente** , seleccione las regiones para agregar o quitar y, a continuación, haga clic en **Guardar**. Hay un costo para agregar regiones, consulte la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/) o el artículo [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md) para obtener más información.

    ![Haga clic en las regiones en el mapa para agregarlas o quitarlas][1]

### <a name="selecting-global-database-regions"></a>Selección de las zonas de la base de datos global

Al configurar dos o más regiones, se recomienda que las regiones están seleccionadas basado en los pares de la región se describe en el [recuperación ante desastres y continuidad del negocio (BCDR): las regiones emparejados de Azure]  [ bcdr] artículo.

Específicamente, al configurar para varias regiones, asegúrese de seleccionar el mismo número de regiones (+-1 para el par/impar) de cada una de las columnas región emparejados. Por ejemplo, si desea implementar en cuatro regiones de Estados Unidos, selecciona dos regiones de Estados Unidos desde la columna izquierda y dos de la derecha. Por lo tanto, lo siguiente sería un conjunto apropiado: oeste de Estados Unidos, Estados Unidos oriental, zona norte Central de EE y sur Central de EE.

Esta guía es importante seguir cuando hay sólo dos regiones configuradas para escenarios de recuperación ante desastres. De más de dos países, después de esta guía es una buena práctica, pero no críticas como algunas de las regiones seleccionadas se adhieren a este emparejamiento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Próximos pasos

Aprenda a administrar la coherencia de su cuenta global replicado mediante la lectura de [los niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

Para obtener información acerca de la base de datos global de cómo funciona la replicación en DocumentDB, vea [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md). Para obtener información acerca de cómo replicar datos en varias regiones de mediante programación, vea [desarrollar con cuentas de DocumentDB consta de varias regiones](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
