<properties
   pageTitle="Aplicaciones de lógica bucles, ámbitos y Debatching | Microsoft Azure"
   description="Bucle de lógica de la aplicación, el ámbito y conceptos de debatching"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Aplicaciones de lógica bucles, ámbitos y Debatching
  
>[AZURE.NOTE] Esta versión del artículo se aplica al esquema de lógica de 2016 Apps-04-01-vista previa y posterior.  Conceptos son similares para esquemas más antiguos, pero ámbitos sólo están disponibles para este esquema y posterior.
  
## <a name="foreach-loop-and-arrays"></a>Bucle ForEach y arreglos de discos
  
Lógica de aplicaciones le permite recorrer en iteración un conjunto de datos y realizar una acción para cada elemento.  Esto es posible a través de la `foreach` acción.  En el diseñador, se puede especificar para agregar un bucle for each.  Después de seleccionar la matriz que desea recorrer en iteración, puede empezar a agregar acciones.  Actualmente está limitado a sólo una acción por bucle foreach, pero esta restricción se levantarán en las próximas semanas.  Una vez dentro del bucle se puede empezar a especificar qué debe ocurrir en cada valor de la matriz.

Si utiliza la vista código, puede especificar un bucle for each como a continuación.  Éste es un ejemplo de un bucle for each que envía un correo electrónico para cada dirección de correo electrónico que contenga "microsoft.com":

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` acción puede iterar sobre matrices hasta 5.000 filas.  Cada iteración se puede ejecutar en paralelo, por lo que puede ser necesario agregar mensajes a una cola si se requiere control de flujo.
  
## <a name="until-loop"></a>Hasta que el bucle
  
  Puede realizar una acción o serie de acciones hasta que se cumpla una condición.  El escenario más común para este llama a un extremo hasta que obtenga la respuesta que busca.  En el diseñador, puede especificar que se añada un hasta el bucle.  Después de agregar acciones dentro del bucle, puede establecer la condición de salida, así como el bucle de límites.  Hay un retraso de 1 minuto entre ciclos de bucle.
  
  Si utiliza la vista código, puede especificar una hasta que el bucle como las siguientes.  Éste es un ejemplo de una llamada a un extremo HTTP hasta que el cuerpo de la respuesta tiene el valor 'Completada'.  Finalizará cuando ambos 
  
  * Respuesta HTTP tiene el estado de 'Completada'
  * Ha intentado por 1 hora
  * Ha repetido 100 veces
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn y Debatching

A veces, un desencadenador puede recibir una matriz de elementos que desea debatch e iniciar un flujo de trabajo por artículo.  Esto se puede lograr a través de la `spliton` comando.  De forma predeterminada, si su pavoneo desencadenador especifica una carga que es una matriz, un `spliton` se agregará e iniciar una ejecución por artículo.  SplitOn sólo pueden agregarse a un desencadenador.  Esto puede ser manualmente configurado o se reemplaza en la vista código de definición.  Actualmente puede debatch SplitOn matrices hasta 5.000 artículos.  No puede haber un `spliton` así como implementar el patrón de respuesta sincrónica.  Cualquier flujo de trabajo llama a que tiene un `response` acción además `spliton` se ejecuta de forma asincrónica y enviar de forma inmediata `202 Accepted` respuesta.  

SplitOn puede especificarse en la vista código como en el ejemplo siguiente.  Este recibe una matriz de elementos y debatches en cada fila.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Ámbitos

Es posible agrupar una serie de acciones con un ámbito.  Esto es especialmente útil para implementar el control de excepciones.  En el diseñador puede agregar un nuevo ámbito y empezar a agregar todas las acciones en su interior.  Puede definir ámbitos en la vista código como el siguiente:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
