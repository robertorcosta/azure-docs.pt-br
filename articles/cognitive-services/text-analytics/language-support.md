---
title: Idiomas compatíveis – API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista dos idiomas naturais compatíveis com a API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimento, extração de frases-chave, detecção de idioma e reconhecimento de entidade.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219275"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Idiomas e regiões compatíveis com a API de Análise de Texto

Este artigo explica quais idiomas são suportados para cada operação: análise de sentimentos, extração de frases-chave, detecção de linguagem e reconhecimento de entidade nomeada.

## <a name="language-detection"></a>Detecção de Idioma

A API text analytics pode detectar uma ampla gama de idiomas, variantes, dialetos e algumas línguas regionais/culturais.  A detecção de idioma retorna o "script" de um idioma. Por exemplo, a frase "Eu tenho um cachorro" retornará `en`, em vez de `en-US`. O único especial é chinês, em que a funcionalidade de detecção de idioma retornará `zh_CHS` ou `zh_CHT` se ela puder determinar o script fornecido pelo texto fornecido. Em situações em que um script específico não pode ser identificado para um documento em chinês, ela retornará simplesmente `zh`.

Não publicamos a lista exata de idiomas para esse recurso, mas ele pode detectar uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Análise de sentimentos, extração de frases-chave e reconhecimento de entidade nomeada

Para análise de sentimento, extração de frases-chave e reconhecimento de entidade, a lista de idiomas compatíveis é mais seletiva, uma vez que os analisadores são refinados para acomodar as regras linguísticas de idiomas adicionais. Em Reconhecimento de entidade nomeado v2, o suporte para o conjunto completo de tipos de [entidades](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) está atualmente limitado aos seguintes idiomas: 
* Inglês
* Chinês simplificado
* Francês
* Alemão
* Espanhol

Apenas `Person`as `Location` `Organization` entidades e denominadas são devolvidas para as outras línguas.

## <a name="language-list-and-status"></a>Status e lista de idiomas

O suporte a idiomas é implantado inicialmente em versão prévia, passando para o status GA (disponível ao público em geral), de modo independente um do outro e do serviço de Análise de Texto como um todo. É possível que idiomas permaneçam em versão prévia mesmo quando a API de Análise de Texto passar para disponível ao público em geral.

> [!NOTE]
> Para obter suporte detalhado ao idioma para a pré-visualização pública v3 do Reconhecimento de Entidade (NER) nomeado, consulte [Tipos de entidade nomeados](named-entity-types.md).

| Idioma              | Código de idioma | Sentimento | Frases principais | Reconhecimento de Entidade Nomeada | Vinculação de entidade |       Observações        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Árabe                |     `ar`      |           |             |           ✔\*           |                |                    |
| Tcheco                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Chinês simplificado    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh` também é aceito                   |
| Chinês tradicional   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Dinamarquês                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Holandês                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Inglês               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finlandês               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francês                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Alemão                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Grego                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Húngaro             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italiano               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japonês              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Coreano                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norueguês (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb` também é aceito                   |
| Polonês                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Português (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt` também é aceito |
| Português (Brasil)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Russo               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Espanhol               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Sueco               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turco               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*O suporte ao idioma está na pré-visualização

\** Também disponível na [Análise de Sentimento v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) e/ou [Reconhecimento de Entidade saqueada v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) visualizações públicas.

## <a name="see-also"></a>Confira também

[Página de Documentação de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)   
[Página do Produto de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
