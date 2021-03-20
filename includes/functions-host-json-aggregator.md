---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92167638"
---
Especifica quantas invocações de função são agregadas ao [calcular métricas para o Application Insights](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriedade |Padrão  | Descrição |
|---------|---------|---------| 
|batchSize|1000|Número máximo de solicitações para agregação.| 
|flushTimeout|00:00:30|Período de tempo máximo para agregação.| 

As invocações de função são agregadas quando o primeiro dos dois limites é atingido.
