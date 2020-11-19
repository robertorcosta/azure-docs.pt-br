---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: 0d248fa57d5288cedae8dc46441739575c48c129
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918056"
---
<!--Create a copyAllNonInterleave transform with REST-->

O comando REST do Azure a seguir cria uma transformação usando `copyAllBitrateNonInterleaved` a partir da `#Microsoft.Media.BuiltInStandardEncoderPreset` predefinição. Substitua os valores `subscriptionID` , `resourceGroup` e `accountName` pelos valores com os quais você está trabalhando no momento. Dê um nome à sua transformação Configurando `transformName` . 

Para exibir todas as transformações da API REST que estão disponíveis, consulte [transformações-criar ou atualizar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions).

```REST
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>Corpo

```json
{
    "name": "myTransform",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "description": "Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                    "presetName": "CopyAllBitrateNonInterleaved"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>Resposta de exemplo
```json
{
  "name":"myTransform",
    "id":"/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform","type":"Microsoft.Media/mediaservices/transforms","properties":{
        "created":"2020-11-18T19:19:27.4405209Z",
        "description":"Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets","lastModified":"2020-11-18T19:19:27.4405209Z",
        "outputs":[
            {
                "onError":"StopProcessingJob","relativePriority":"Normal","preset":{
                "@odata.type":"#Microsoft.Media.BuiltInStandardEncoderPreset",
                "presetName":"CopyAllBitrateNonInterleaved"
        }
      }
    ]
  }
}
```
