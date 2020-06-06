---
title: O que há de novo na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece informações sobre novas versões e recursos para os serviços cognitivas do Azure Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: aahi
ms.openlocfilehash: 7ab886b65e665bdd38e2461e49fa051213602f0e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462780"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

O API de Análise de Texto é atualizado em uma base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece informações sobre novas versões e recursos.

## <a name="may-2020"></a>Maio de 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilidade geral do API de Análise de Texto v3

A API de análise de texto v3 já está disponível para o público geral com as seguintes atualizações:

* Versão do modelo`2020-04-01`
* Novos [limites de dados](concepts/data-limits.md) para cada recurso
* [Suporte de idioma](language-support.md) atualizado para o [análise de sentimento (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Ponto de extremidade separado para vinculação de entidade 
* Nova categoria de entidade "address" no [reconhecimento de entidade nomeada (Ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Novas subcategorias no NER V3:
   * Local-geográfico
   * Local-estrutural
   * Organização – troca de ações
   * Organização – médico
   * Esportes da organização
   * Cultura do evento
   * Evento-natural
   * Evento-esportes

As seguintes propriedades na resposta JSON foram adicionadas:
   * `SentenceText`em Análise de Sentimento
   * `Warnings`para cada documento 

Os nomes das seguintes propriedades na resposta JSON foram alterados, quando aplicável:

* `score` foi renomeado para `confidenceScore`
    * `confidenceScore`tem dois pontos decimais de precisão. 
* `type` foi renomeado para `category`
* `subtype` foi renomeado para `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Saiba mais sobre o API de Análise de Texto v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Visualização pública do API de Análise de Texto v 3.1
   * Nova Análise de Sentimento recurso – [mineração de opinião](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Novo [filtro de `PII` domínio pessoal ()](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) para informações de integridade protegidas ( `PHI` ).
   * Novas categorias pessoais ( `PII` ):
      * Classificação internacional de doenças (ICD-9-CM)
      * Classificação internacional de doenças (ICD-10-CM)

> [!div class="nextstepaction"]
> [Saiba mais sobre a versão prévia do API de Análise de Texto v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte do SDK para a visualização pública do API de Análise de Texto v3

Como parte da [versão unificada do SDK do Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), o sdk do API de análise de texto v3 agora está disponível como uma visualização pública para as seguintes linguagens de programação:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node. js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do API de Análise de Texto v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Visualização pública de reconhecimento de entidade nomeada v3

Tipos de entidade adicionais agora estão disponíveis no serviço de visualização pública de reconhecimento de entidade nomeada (NER) v3 à medida que expandimos a detecção de entidades de informações gerais e pessoais encontradas no texto. Esta atualização introduz a [versão do modelo](concepts/model-versioning.md) `2020-02-01` , que inclui:

* Reconhecimento dos seguintes tipos de entidade geral (somente em inglês):
    * Persontype
    * Produto
    * Evento
    * Entidade geopolítica (GPE) como um subtipo no local
    * Habilidade

* Reconhecimento dos seguintes tipos de entidade de informações pessoais (somente em inglês):
    * Person
    * Organização
    * Idade como um subtipo em quantidade
    * Data como um subtipo em DateTime
    * Email 
    * Número de telefone (somente EUA)
    * URL
    * Endereço IP

> [!div class="nextstepaction"]
> [Saiba mais sobre o reconhecimento de entidade nomeada v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

* Um [novo ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para reconhecer tipos de entidade de informações pessoais (somente em inglês)

* Separe pontos de extremidade para [reconhecimento de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [vinculação de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Versão do modelo](concepts/model-versioning.md) `2019-10-01` , que inclui:
    * Detecção expandida e categorização de entidades encontradas no texto. 
    * Reconhecimento dos seguintes novos tipos de entidade:
        * Número do telefone
        * Endereço IP

A vinculação de entidades dá suporte a inglês e espanhol. O suporte ao idioma NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 em versão prévia pública

* Um [novo ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) para análise de sentimentos.
* [Versão do modelo](concepts/model-versioning.md) `2019-10-01` , que inclui:

    * Melhorias significativas na precisão e nos detalhes da categorização e da Pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos no texto.
    * Análise de sentimentos e saída em um nível de documento e frase. 

Ele dá suporte a Inglês ( `en` ), japonês (), `ja` chinês simplificado ( `zh-Hans` ), chinês tradicional ( `zh-Hant` ), francês ( `fr` ), italiano ( `it` ), espanhol (), holandês (), `es` `nl` Português ( `pt` ) e alemão () `de` e está disponível nas seguintes regiões: `Australia East` , `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,, e. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Sentimento v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Próximas etapas

* [O que é o API de Análise de Texto?](overview.md)  
* [Cenários de usuário de exemplo](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Detecção de idioma](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidade](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md)
