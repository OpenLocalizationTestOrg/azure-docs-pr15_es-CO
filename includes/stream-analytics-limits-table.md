<properties 
   pageTitle="Tabla de límites de análisis de secuencia"
   description="Describe los límites del sistema y tamaños recomendados para los componentes de análisis de secuencia y las conexiones."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificador de límite | Límite de       | Comentarios |
|----------------- | ------------|--------- |
| Número máximo de unidades de transmisión por suscripción por región | 50 | Una solicitud para aumentar unidades de transmisión para su suscripción más allá de los 50 se puede realizar por ponerse en contacto con el [Soporte técnico de Microsoft](https://support.microsoft.com/en-us). |
| Rendimiento máximo de una unidad de transmisión por secuencias | 1MB / s * | Máximo rendimiento por SU depende del escenario. El rendimiento real puede ser inferior y depende de la complejidad de la consulta y creación de particiones. Encontrará más detalles en el artículo de [trabajos de análisis de secuencia de Azure de escala para aumentar el rendimiento](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Número máximo de entradas por trabajo | 60 | Hay un límite máximo de 60 entradas por trabajo de análisis de secuencia. |
| Número máximo de resultados por trabajo | 60 | Hay un límite máximo de 60 salidas por trabajo de análisis de secuencia. |
| Número máximo de funciones por puesto de trabajo | 60 | Hay un límite máximo de 60 funciones por puesto de trabajo de análisis de secuencia. |
| Número máximo de trabajos por región | 1500 | Cada suscripción puede tener hasta 1500 trabajos por región geográfica. |