<properties
 pageTitle="Autenticación de salida del programador"
 description="Autenticación de salida del programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Autenticación de salida del programador

Trabajos del programador que necesite llamar a servicios que requieren autenticación. De este modo, un servicio llamado puede determinar si el trabajo del programador puede tener acceso a sus recursos. Algunos de estos servicios incluyen otros servicios de Azure, Salesforce.com, Facebook y segura sitios Web personalizado.

## <a name="adding-and-removing-authentication"></a>Adición y eliminación de autenticación

Agregar autenticación para el trabajo de un programador es simple: agregar un elemento secundario JSON `authentication` a la `request` elemento al crear o actualizar un trabajo. Secretos pasan al servicio Programador en una solicitud PUT, parche o POST – como parte de la `authentication` objeto: nunca se devuelven en las respuestas. En las respuestas, información secreta está establecido en null o tener un token pública que representa la entidad autenticada.

Para quitar la autenticación, coloque o revisión explícitamente, el trabajo de configuración de la `authentication` objeto en null. No verá las propiedades de autenticación en respuesta.

Actualmente, los tipos de autenticación compatibles sólo son el `ClientCertificate` (para el uso de los certificados de cliente SSL/TLS), el `Basic` (para la autenticación básica), el modelo y la `ActiveDirectoryOAuth` modelo (para autenticación OAuth de Active Directory.)

## <a name="request-body-for-clientcertificate-authentication"></a>Solicitar cuerpo para autenticación ClientCertificate

Al agregar la autenticación mediante el `ClientCertificate` de modelo, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.  

|Elemento|Descripción|
|:---|:---|
|_autenticación (elemento primario)_|Objeto de autenticación para el uso de un certificado de cliente SSL.|
|_tipo_|Obligatorio. Tipo de autenticación. Para los certificados de cliente SSL, el valor debe ser `ClientCertificate`.|
|_PFX_|Obligatorio. Contenido codificado en base64 del archivo PFX.|
|_contraseña_|Obligatorio. Contraseña de acceso al archivo PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Cuerpo de la respuesta de autenticación ClientCertificate

Cuando se envía una solicitud con la información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_autenticación (elemento primario)_ |Objeto de autenticación para el uso de un certificado de cliente SSL.|
|_tipo_ |Tipo de autenticación. Para los certificados SSL de cliente, el valor es `ClientCertificate`.|
|_certificateThumbprint_ |La huella digital del certificado.|
|_certificateSubjectName_ |Nombre distintivo del sujeto del certificado.|
|_certificateExpiration_ |La fecha de caducidad del certificado.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Ejemplo de solicitud de descanso para autenticación ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Respuesta del resto de ejemplo para autenticación ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Cuerpo de la solicitud de autenticación básica

Al agregar la autenticación mediante el `Basic` de modelo, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento|Descripción|
|:--|:--|
|_autenticación (elemento primario)_ |Objeto de autenticación para la autenticación básica.|
|_tipo_ |Obligatorio. Tipo de autenticación. Para la autenticación básica, el valor debe ser `Basic`.|
|_nombre de usuario_ |Obligatorio. Nombre de usuario para autenticar.|
|_contraseña_ |Obligatorio. Contraseña para la autenticación.|

## <a name="response-body-for-basic-authentication"></a>Cuerpo de la respuesta para la autenticación básica

Cuando se envía una solicitud con la información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento|Descripción|
|:--|:--|
|_autenticación (elemento primario)_ |Objeto de autenticación para la autenticación básica.|
|_tipo_ |Tipo de autenticación. Para la autenticación básica, el valor es `Basic`.|
|_nombre de usuario_ |El nombre de usuario autenticado.|

## <a name="sample-rest-request-for-basic-authentication"></a>Ejemplo de solicitud de descanso para la autenticación básica

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Respuesta del resto de ejemplo para la autenticación básica

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Solicitar cuerpo de autenticación ActiveDirectoryOAuth

Al agregar la autenticación mediante el `ActiveDirectoryOAuth` de modelo, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento |Descripción |
|:--|:--|
|_autenticación (elemento primario)_ |Objeto de autenticación para la autenticación de ActiveDirectoryOAuth.|
|_tipo_ |Obligatorio. Tipo de autenticación. Para la autenticación de ActiveDirectoryOAuth, el valor debe ser `ActiveDirectoryOAuth`.|
|_inquilinos_ |Obligatorio. El identificador de inquilinos para el arrendatario AD Azure.|
|_audiencia_ |Obligatorio. Esto se establece en https://management.core.windows.net/.|
|_clientId_ |Obligatorio. Proporcionar el identificador de cliente para la aplicación de Azure AD.|
|_secreto_ |Obligatorio. Secreto del cliente que solicita el token.|

### <a name="determining-your-tenant-identifier"></a>Determinar el identificador de inquilinos

Puede encontrar el identificador de inquilinos para el inquilino Azure AD mediante la ejecución de `Get-AzureAccount` de PowerShell de Azure.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Cuerpo de la respuesta de autenticación ActiveDirectoryOAuth

Cuando se envía una solicitud con la información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_autenticación (elemento primario)_ |Objeto de autenticación para la autenticación de ActiveDirectoryOAuth.|
|_tipo_ |Tipo de autenticación. Autenticación de ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`.|
|_inquilinos_ |El identificador de inquilinos para el arrendatario AD Azure. |
|_audiencia_ |Esto se establece en https://management.core.windows.net/.|
|_clientId_ |El identificador de cliente para la aplicación de Azure AD.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Ejemplo de solicitud de descanso para la autenticación de ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Respuesta del resto de ejemplo para la autenticación de ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Vea también


 [¿Qué es el programador?](scheduler-intro.md)

 [La jerarquía de entidad, terminología y conceptos de programador de Azure](scheduler-concepts-terms.md)

 [Comenzar a utilizar a Programador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Límites de programador Azure, valores predeterminados y los códigos de error](scheduler-limits-defaults-errors.md)
