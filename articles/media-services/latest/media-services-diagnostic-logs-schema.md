---
title: Registros de diagnóstico do Azure Media Services - Azure
description: Este artigo demonstra os registros de diagnóstico do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750882"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas de logs de diagnóstico

[O Azure Monitor](../../azure-monitor/overview.md) permite monitorar métricas e registros de diagnóstico que ajudam a entender como seus aplicativos estão se saindo. Você pode monitorar registros de diagnóstico do Media Services e criar alertas e notificações para as métricas e registros coletados. Você pode enviar logs para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para [o Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [o Log Analytics](https://azure.microsoft.com/services/log-analytics/)ou usar serviços de terceiros.

Para obter informações [detalhadas, consulte a Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) e [os registros de diagnóstico do Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).

Este artigo descreve esquemas de registros de diagnóstico do Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de logs de diagnóstico de nível superior

Para obter uma descrição detalhada do esquema de registros de diagnóstico de nível superior, consulte [serviços, esquemas e categorias suportados para logs de diagnóstico do Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Esquema de registro de entrega de chaves

### <a name="properties"></a>Propriedades

Essas propriedades são específicas para o esquema de registro de entrega chave.

|Nome|Descrição|
|---|---|
|keyId|A iD da chave solicitada.|
|keyType|Pode ser um dos seguintes valores: "Claro" (sem criptografia), "FairPlay", "PlayReady" ou "Widevine".|
|policyName|O nome do Gerente de Recursos do Azure da política.|
|Tokentype|O tipo do token.|
|Statusmessage|A mensagem de status.|

### <a name="examples"></a>Exemplos

Propriedades do esquema de solicitações de entrega de chaves.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

[Monitore métricas e registros de diagnóstico do Media Services](media-services-metrics-diagnostic-logs.md)
