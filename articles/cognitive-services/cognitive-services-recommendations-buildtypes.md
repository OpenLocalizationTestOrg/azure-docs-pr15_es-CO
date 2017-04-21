<properties
    pageTitle="Tipos de generación y calidad del modelo: recomendaciones API | Microsoft Azure"
    description="Recomendaciones de aprendizaje máquina Azure: Guía de inicio rápido"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Tipos de generación y calidad del modelo #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Tipos de generación compatibles ##

La API de recomendaciones actualmente admite dos tipos de generación: *FBT*y *recomendación* . Cada uno se crea mediante algoritmos diferentes y cada uno tiene sus puntos fuertes. Este documento describe cada una de estas compilaciones y técnicas para comparar la calidad de los modelos generados.

Si no lo ha hecho ya, se recomienda que complete la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Tipo de generación de recomendación ###

El tipo de generación de recomendación utiliza factorización de matriz para proporcionar recomendaciones. Genera vectores de [característica latente](https://en.wikipedia.org/wiki/Latent_variable) basados en las transacciones para describir cada elemento y, a continuación, utiliza los vectores latentes para comparar elementos similares.

Si entrenar el modelo basado en las compras realizadas en su tienda de electrónica y proporcionar un teléfono 650 Lumia como entrada para el modelo, el modelo devolverá un conjunto de elementos que tienden a ser adquiridos por personas que están probable que un teléfono 650 Lumia de compra. Los elementos no pueden ser complementarios. En este ejemplo, es posible que el modelo devolverá otros teléfonos porque las personas que les gusta la 650 Lumia les gusta otros teléfonos.

La generación de recomendación tiene dos capacidades que lo hacen atractivo:

Colocación de *elemento frío *admite la generación de recomendación ** **

Elementos que no tienen un uso significativo se denominan elementos frío. Por ejemplo, si recibe un envío de un teléfono que nunca ha vendido antes, el sistema no puede deducir recomendaciones para este producto en las transacciones por sí solo. Esto significa que debe aprender el sistema de información sobre el producto en Sí.

Si desea utilizar la ubicación de artículos frío, debe proporcionar información de funciones para cada uno de los elementos del catálogo. Siguiente es las primeras líneas del catálogo pueden aspecto (formato de nota de la clave = valor para las características).

>Pro2 6CX-00001, superficie, superficie, tipo = Hardware, almacenamiento = memoria de 128 GB = 4G, fabricante = Microsoft

>73H-00013, Wake Xbox 360, juegos,, tipo = Software, idioma = inglés, valorar = maduro

>WAH 0F05, Minecraft de Xbox 360, juegos,, * tipo = Software, idioma = español, valorar = juventud

También debe definir los siguientes parámetros de compilación:

| El parámetro de compilación         | Notas
|------------------     |-----------
|*useFeaturesInModel*     | Establezca en **true**.  Indica si las características pueden utilizarse para mejorar el modelo de recomendación.
|*allowColdItemPlacement*   | Establezca en **true**. Indica si la recomendación también debe Empujar elementos frío a través de la similitud de la característica.
| *modelingFeatureList*   | Lista separada por comas de nombres de características que se utilizará en la generación de recomendaciones para mejorar la recomendación. Por ejemplo, "lenguaje, almacenamiento de información" para el ejemplo anterior.

**La generación de recomendación es compatible con las recomendaciones del usuario**

Una compilación de recomendación es compatible con [las recomendaciones del usuario](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Esto significa que puede proporcionar recomendaciones personalizadas para los usuarios según sus historiales de transacciones. Para recomendaciones de usuarios, podría proporcionar el identificador de usuario o de la historia reciente de las transacciones para ese usuario.

Uno es un ejemplo clásico de donde desea aplicar las recomendaciones de usuario al iniciar sesión en la página de bienvenida. Allí puede promover el contenido al que se aplica a un usuario determinado.

También puede aplicar un tipo de generación de recomendaciones cuando el usuario está a desproteger. En ese momento, tiene la lista de elementos que el usuario está a punto de compra, y puede proporcionar recomendaciones basadas en la cesta del mercado actual.

#### <a name="recommendations-build-parameters"></a>Parámetros de generación de recomendaciones

| Nombre  |   Descripción |    Tipo, <br>  valores válidos, <br> (valor predeterminado)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   El número de iteraciones que realiza el modelo viene indicado por el tiempo de cálculo total y la precisión del modelo. Cuanto mayor sea el número, más preciso será el modelo, pero el tiempo de cálculo tarda más.  |   Entero, <br>  de 10 a 50 <br>(40)
| *NumberOfModelDimensions* |   El número de dimensiones se relaciona con el número de características que el modelo intentará encontrar dentro de sus datos. Aumentar el número de dimensiones le permitirá ajustar mejor los resultados en clústeres más pequeños. Sin embargo, demasiadas dimensiones impedirá el modelo encontrar correlaciones entre los elementos. |  Entero, <br> 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |  Define el número mínimo de puntos de uso que debe ser un elemento en para que se considere parte del modelo. |        Entero, <br> 2 o más <br> (2) |
| *ItemCutOffUpperBound* |  Define el número máximo de puntos de uso que debe ser un elemento en para que se considere parte del modelo. |  Entero, <br>2 o más<br> (2147483647) |
|*UserCutOffLowerBound* |   Define el número mínimo de transacciones, que un usuario debe haber realizado para que se considere parte del modelo. | Entero, <br> 2 o más <br> (2)
| *UserCutOffUpperBound* |  Define el número máximo de transacciones que debe haber realizado un usuario para que se considere parte del modelo. | Entero, <br>2 o más <br> (2147483647)|
| *UseFeaturesInModel* |    Indica si las características pueden utilizarse para mejorar el modelo de recomendación. |     Valor booleano<br> Valor predeterminado: True
|*ModelingFeatureList* |    Lista separada por comas de nombres de características que se utilizará en la generación de recomendaciones para mejorar la recomendación. Depende de las características que son importantes. |    Cadena de hasta 512 caracteres
| *AllowColdItemPlacement* |    Indica si la recomendación también debe Empujar elementos frío a través de la similitud de la característica. | Valor booleano <br> Valor predeterminado: False
| *EnableFeatureCorrelation*    | Indica si las características pueden utilizarse en el razonamiento. | Valor booleano <br> Valor predeterminado: False
| *ReasoningFeatureList* |  Lista separada por comas de nombres de características que se utilizará para frases, como explicaciones de recomendación de razonamiento. Depende de las características que son importantes para los clientes. | Cadena de hasta 512 caracteres
| *EnableU2I* | Habilitar recomendaciones personalizadas, también llamadas usuario para recomendaciones (U2I) del artículo. | Valor booleano <br>Valor predeterminado: True
|*EnableModelingInsights* | Define si se debe realizar la evaluación sin conexión para recopilar conocimientos de modelado (es decir, mediciones de precisión y diversidad). Si se establece en true, un subconjunto de los datos no se utilizará para capacitación dado que será necesario reservar para la prueba del modelo. Más información acerca de [las evaluaciones sin conexión](#OfflineEvaluation). | Valor booleano <br> Valor predeterminado: False
| *SplitterStrategy* | Si enable modelado insights está establecido en *true*, se trata cómo se deben dividir los datos para fines de evaluación.  | Cadena, *RandomSplitter* o *LastEventSplitter* <br>Predeterminado: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Tipo de generación FBT ###

La generación de (FBT) junta con frecuencia comprada hace un análisis que cuente el número de veces, dos o tres productos diferentes ocurren juntos. A continuación, ordena los conjuntos basados en una función de similitud (**CO-ocurrencias**, **Jaccard**, **levante**).

Considere **Jaccard** y **levante** como formas de normalizar las apariciones de compañeros.  Esto significa que los elementos se devolverán sólo si ellos donde lo compró junto con el elemento de semillas.

En nuestro ejemplo de teléfono 650 Lumia, teléfono X se devolverá sólo si teléfono X fue adquirido en la misma sesión que el teléfono 650 Lumia. Dado que esto puede ser poco probable, esperaríamos que elementos complementarios a los 650 Lumia va a devolver; Por ejemplo, un protector de pantalla o un adaptador de alimentación para los 650 Lumia.

En la actualidad, dos elementos se supone que para se puede adquirir en la misma sesión si se producen en una transacción con el mismo identificador de usuario y la marca de hora.

Compilaciones FBT no admiten elementos frío, ya que por definición esperan dos elementos a comprar en la misma transacción. Mientras FBT compilaciones pueden devolver conjuntos de elementos (trípticos), no admiten recomendaciones personalizadas porque aceptan un elemento único de semillas como entrada.


#### <a name="fbt-build-parameters"></a>Parámetros de compilación FBT

| Nombre  |   Descripción |       Tipo,  <br> valores válidos, <br> (valor predeterminado)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | El modelo conservador cómo es. Número de apariciones de compañeros de elementos que deben considerarse para el modelado. |  Entero, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limita el número de elementos de un conjunto frecuentes.| Entero  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Puntuación mínima que debe tener un conjunto frecuentes que se incluirán en los resultados devueltos. Cuanto mayor sea, mejor. | Doble <br> 0 y posterior <br> (0)
| *FbtSimilarityFunction* | Define la función de similitud para ser utilizado por la compilación. **Levante** favorece la casualidad, **co-ocurrencia** favorece la previsibilidad y **Jaccard** es un compromiso entre los dos. | Cadena,  <br>  <i>Sperber, elevador, jaccard</i><br> Valor predeterminado: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Generar la evaluación y selección ##

Esta guía puede ayudarle a determinar si debe utilizar un recomendaciones o una compilación FBT pero no proporciona una respuesta definitiva en casos donde podría usar cualquiera de ellas. Además, incluso si sabe que desea utilizar un tipo de generación FBT, podría aún desea elegir **Jaccard** o **levante** como función de similitud.

La mejor forma de seleccionar entre dos generaciones diferentes es probarlos en el mundo real (evaluación en línea) y realizar el seguimiento de una tasa de conversión para las diferentes versiones. La tasa de conversión se podría medir en función de los clics de recomendación, el número real compras desde las recomendaciones que se muestra, o incluso en los importes de compra reales cuando se mostraron diferentes recomendaciones. Puede seleccionar la métrica de tasa de conversión basado en su objetivo de negocio.

En algunos casos, es aconsejable evaluar el modelo sin conexión antes de ponerlo en producción. Evaluación sin conexión, no es un sustituto de la evaluación en línea puede servir como una métrica.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Evaluación sin conexión  ##

El objetivo de una evaluación sin conexión es predecir la precisión (número de usuarios que va a adquirir uno de los elementos recomendados) y la diversidad de recomendaciones (el número de elementos que se recomienda).
Como parte de la evaluación de métricas de precisión y diversidad, el sistema busca una muestra de los usuarios y divide las transacciones para los usuarios en dos grupos: el conjunto de datos de entrenamiento y el conjunto de datos de prueba.

> [AZURE.NOTE]Para utilizar métricas sin conexión, debe tener las marcas de tiempo en los datos de uso.
> Datos de hora son necesarios dividir el uso correctamente entre los conjuntos de datos de entrenamiento y la prueba.

> Además, la evaluación sin conexión no puede producir resultados para archivos pequeños de uso. Para que la evaluación sea exhaustiva, debe haber un mínimo de 1.000 puntos de uso en el conjunto de datos de prueba.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Precisión en k ###
En la siguiente tabla representa el resultado de la evaluación de precisión en k sin conexión.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Porcentaje |   13,75 | 18.04   | 21 |  24.31 | 26.61
|Usuarios de prueba |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuarios considerarlos | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuarios que no se consideran | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
En la tabla anterior, *k* representa el número de recomendaciones que se muestra al cliente. La tabla quedará redactado como sigue: "Si durante el período de prueba, se muestra solamente una recomendación a los clientes, 13,75 sólo de los usuarios habrían comprado esa recomendación". Esta instrucción se basa en la suposición de que el modelo se ha entrenado con datos de compra. Otra forma de decir esto es que la precisión en 1 13,75.

Observe que mientras más elementos se muestran al cliente, la probabilidad de que el cliente compra un artículo recomendado sube. Para el experimento anterior, la probabilidad casi se duplica al 26.61% cuando se recomiendan 5 elementos.

#### <a name="percentage"></a>Porcentaje
Se muestra el porcentaje de usuarios que interactuaban con al menos una de las recomendaciones de *k* . El porcentaje se calcula dividiendo el número de usuarios que interactuaban con al menos una recomendación por el número total de usuarios considerados. Ver usuarios considerados para obtener más información.

#### <a name="users-in-test"></a>Usuarios de prueba
Los datos de esta fila representan el número total de usuarios en el conjunto de datos de prueba.

#### <a name="users-considered"></a>Usuarios considerarlos
Sólo se considera un usuario si el sistema se recomienda al menos elementos *k* se basan en el modelo generado con el conjunto de datos de entrenamiento.

#### <a name="users-not-considered"></a>Usuarios que no se consideran
Los datos de esta fila representan los usuarios que no se consideran. Los usuarios que no se hicieron recibido al menos *k* elementos recomendados.

Usuario no considerada = usuarios de prueba: los usuarios consideran

<a name="Diversity"></a>
### <a name="diversity"></a>Diversidad ###
Métricas de diversidad medir el tipo de elementos que se recomienda. En la siguiente tabla representa el resultado de la evaluación de diversidad sin conexión.

|Bote de percentil |    0-90|  90-99| 99-100
|------------------|--------|-------|---------
|Porcentaje        | 34.258 | 55.127| 10.615


Total de elementos recomendados: 100.000

Elementos exclusivos recomendados: 954

#### <a name="percentile-buckets"></a>Depósitos de percentil
Cada depósito de percentil está representado por un intervalo (valores máximo y mínimo que el intervalo entre 0 y 100). Los elementos cerca de 100 son los elementos más populares y los elementos próximo a 0 son los menos populares. Por ejemplo, si el valor de porcentaje para el depósito de percentil 99-100 es 10.6, lo significa que 10.6 por ciento de las recomendaciones devuelven sólo el uno por ciento artículos más populares. El valor mínimo del depósito de percentil es inclusivo, y el valor máximo es exclusivo, excepto 100.
#### <a name="unique-items-recommended"></a>Elementos exclusivos recomendados
Los elementos únicos recomendados métrica muestra el número de elementos que se han devuelto para su evaluación.
#### <a name="total-items-recommended"></a>Total de artículos recomendado
El total de los artículos se recomienda métrica muestra el número de elementos que se recomienda. Algunos pueden estar duplicados.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Métricas de evaluación sin conexión ###
Las métricas precisión y diversidad sin conexión pueden ser útiles al seleccionar qué compilación que usará. En tiempo de compilación, como parte del FBT o recomendación respectivos parámetros de compilación:

-   Establezca el parámetro de generación de *enableModelingInsights* en **true**.
-   Opcionalmente, seleccione la *splitterStrategy* (ya sea *RandomSplitter* o *LastEventSplitter*).
*RandomSplitter* divide los datos de uso en tren y prueba establece basándose en el porcentaje de pruebas determinado *randomSplitterParameters* y aleatoria valores de semillas.
*LastEventSplitter* divide los datos de uso en tren y probar conjuntos basados en la última transacción para cada usuario.

Esto desencadenará una generación que utiliza sólo un subconjunto de los datos para la formación y el resto de los datos para calcular métricas de evaluación.  Una vez completada la generación, para obtener el resultado de la evaluación, debe llamar a la [API de métricas de generación de Get](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), pasando los respectivos *modelId* y *buildId*.

 Éste es el resultado JSON para la evaluación de la muestra.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
