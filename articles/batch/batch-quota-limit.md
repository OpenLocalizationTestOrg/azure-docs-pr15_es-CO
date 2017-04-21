<properties
    pageTitle="Cuotas de servicio y límites de lotes | Microsoft Azure"
    description="Obtenga información sobre las restricciones, límites y cuotas de Azure lote predeterminado y cómo solicitar cuota aumenta"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Las cuotas y límites para el servicio de proceso de Azure

Como con otros servicios de Azure, existen límites para determinados recursos asociados con el servicio de proceso por lotes. Muchos de estos límites son cuotas predeterminadas aplicadas por Azure en el nivel de cuenta o suscripción. Este artículo describen los valores predeterminados y cómo puede solicitar cuota aumenta.

Si planea ejecutar cargas de trabajo de producción por lotes, debe aumentar en uno o más de los contingentes anteriormente el valor predeterminado. Si desea elevar una cuota, puede abrir un online de [solicitud de asistencia al cliente](#increase-a-quota) sin cargo alguno.

>[AZURE.NOTE] Una cuota es un límite de crédito, no es una garantía de capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte de Azure.

## <a name="subscription-quotas"></a>Cuotas de suscripción
**Recurso**|**Límite predeterminado**|**Límite máximo**
---|---|---
Cuentas de proceso por lotes por región por suscripción | 1 | 50

## <a name="batch-account-quotas"></a>Cuotas de cuenta por lotes
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Otros límites
**Recurso**|**Límite máximo**
---|---
[Tareas simultáneas](batch-parallel-node-tasks.md) por nodo de cálculo | 4 x número de núcleos de nodo
[Aplicaciones](batch-application-packages.md) por cuenta y por lotes        | 20
Paquetes de aplicación por aplicación  | 40
Tamaño del paquete de aplicación (cada uno)       | Aprox. 195GB<sup>1</sup>

Límite de almacenamiento de azure de <sup>1</sup> para el tamaño de bloque máximo blob

## <a name="view-batch-quotas"></a>Ver las cuotas de lote

Ver las cuotas de cuenta por lotes en el [portal de Azure][portal].

1. Seleccione **las cuentas por lotes** en el portal, seleccione la cuenta de proceso por lotes que le interesa.

2. Seleccione **Propiedades** en la hoja del menú de la cuenta de proceso por lotes

3. La hoja de propiedades muestra las **cuotas** aplicado actualmente a la cuenta de proceso por lotes

    ![Cuotas de cuenta por lotes][account_quotas]

## <a name="increase-a-quota"></a>Aumentar cuotas

Siga los pasos siguientes para solicitar una cuota aumentan mediante el [portal de Azure][portal].

1. Seleccione el azulejo **Ayuda + Ayuda** en su panel de portal o el signo de interrogación (¿**?**) en la esquina superior derecha del portal.

2. Seleccione **nueva solicitud de soporte técnico** > **conceptos básicos**.

3. En la hoja de **conceptos básicos** :

    a. **Emitir tipo** > **cuota**

    b. Active su suscripción.

    c. **Tipo de cuota** > **por lotes**

    d. **Plan de asistencia** > **soporte de cuota - incluido**

    Haga clic en **siguiente**.

4. En la hoja del **problema** :

    a. Seleccione una **severidad** según su [impacto en el negocio]de[support_sev].

    b. En **Detalles**, especifique cada cuota que desea cambiar el nombre de la cuenta de proceso por lotes y el nuevo límite.

    Haga clic en **siguiente**.

5. En la hoja de **información de contacto** :

    a. Seleccione un **método de contacto preferido**.

    b. Compruebe y especifique los detalles de contacto necesarios.

    Haga clic en **crear** para enviar la solicitud de soporte técnico.

Una vez que ha enviado su solicitud de asistencia, soporte de Azure comunicaremos con usted. Tenga en cuenta que la finalización de la solicitud puede tardar hasta 2 días hábiles.

## <a name="related-topics"></a>Temas relacionados

* [Crear una cuenta de Azure lote mediante el portal de Azure](batch-account-create-portal.md)

* [Introducción a las características lote Azure](batch-api-basics.md)

* [Suscripción de Azure y límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
