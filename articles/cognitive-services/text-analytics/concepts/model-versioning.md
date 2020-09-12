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
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: e6763633ce27c537f1384e8e51020d44132c0377
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469840"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Controle de versão de modelo no API de Análise de Texto

A versão 3 do API de Análise de Texto permite que você escolha a versão do modelo que é usada em seus dados. Use o `model-version` parâmetro opcional para selecionar a versão do modelo em suas solicitações de API. Por exemplo: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Se esse parâmetro não for especificado, a API usará como padrão a versão estável mais recente. 

## <a name="available-versions"></a>Versões disponíveis

Use a tabela abaixo para localizar quais versões de modelo têm suporte em cada ponto de extremidade.


| Ponto de extremidade                        | Versões compatíveis                                     | última versão |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Você pode encontrar detalhes sobre as atualizações para esses modelos no [que há de novo](../whats-new.md).

## <a name="text-analytics-for-health"></a>Análise de Texto para integridade

O [análise de texto para o contêiner de integridade](../how-tos/text-analytics-for-health.md) usa o controle de versão de modelo separado do que os pontos de extremidade de API acima.  Observe que apenas uma versão de modelo está disponível por imagem de contêiner.

| Ponto de extremidade                        | Marca de imagem de contêiner                     | Versão do modelo |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`               | `1.1.013150001-amd64-preview` ou mais recente | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
