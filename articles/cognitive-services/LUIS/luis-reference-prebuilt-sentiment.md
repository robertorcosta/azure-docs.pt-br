---
title: Análise de sentimento – LUIS
titleSuffix: Azure Cognitive Services
description: Se a análise de sentimento estiver configurada, a resposta JSON do LUIS incluirá a análise de sentimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.author: diberry
ms.openlocfilehash: 2d15170e3785d8978b9cb21eae3b94b002f9172e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857170"
---
# <a name="sentiment-analysis"></a>Análise de sentimento
Se a análise de sentimento estiver configurada, a resposta JSON do LUIS incluirá a análise de sentimento. Saiba mais sobre a análise de sentimento na documentação [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

LUIS usa Análise de Texto v2. 

## <a name="resolution-for-sentiment"></a>Resolução de sentimentos

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados.

#### <a name="english-language"></a>[Idioma inglês](#tab/english)

Quando a cultura for `en-us`, a resposta será:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Outros idiomas](#tab/other-languages)

Para todas as outras culturas, a resposta é:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

