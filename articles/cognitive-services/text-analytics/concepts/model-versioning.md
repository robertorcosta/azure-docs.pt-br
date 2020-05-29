---
title: Especifique a versão do modelo no Análise de Texto v3
titleSuffix: Azure Cognitive Services
description: Saiba como especificar o modelo de API de Análise de Texto usado em seus dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143299"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Controle de versão de modelo no API de Análise de Texto

A versão 3 do API de Análise de Texto permite que você escolha a versão do modelo que é usada em seus dados. Use o `model-version` parâmetro opcional para selecionar a versão do modelo em suas solicitações de API. Por exemplo: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Se esse parâmetro não for especificado, a API usará como padrão a versão estável mais recente. 

## <a name="available-versions"></a>Versões disponíveis

Use a tabela abaixo para localizar quais versões de modelo têm suporte em cada ponto de extremidade.


| Ponto de Extremidade                        | Versões compatíveis                       | última versão |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Você pode encontrar detalhes sobre as atualizações para esses modelos no [que há de novo](../whats-new.md).

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)