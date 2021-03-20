---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 218e98e65c7c78272f32f75a0fdb93e4d87e6948
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92167705"
---
Controla o [recurso de amostragem no Application Insights](../articles/azure-functions/configure-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|isEnabled|true|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limite em que a amostragem começa.| 
