---
title: O que há de novo na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece informações sobre novas versões e recursos para o Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188797"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

A API de análise de texto é atualizada continuamente. Para ficar atualizado com os desenvolvimentos recentes, este artigo fornece informações sobre novos lançamentos e recursos.

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte ao SDK para API v3 Public Preview

Como parte da versão unificada do [Azure SDK,](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)a API v3 SDK do Text Analytics está agora disponível como uma pré-visualização pública para as seguintes linguagens de programação:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Saiba mais sobre o Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Visualização pública nomeada Entity Recognition v3

Outros tipos de entidades estão agora disponíveis no serviço de visualização pública V3 (Named Entity Recognition, reconhecimento de entidades) à medida que expandimos a detecção de entidades de informações gerais e pessoais encontradas no texto. Esta atualização introduz a [versão](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`do modelo, que inclui:

* Reconhecimento dos seguintes tipos de entidades gerais (somente inglês):
    * PersonType
    * Produto
    * Evento
    * Entidade Geopolítica (GPE) como subtipo em Localização
    * Habilidade

* Reconhecimento dos seguintes tipos de entidades de informações pessoais (somente inglês):
    * Person
    * Organização
    * Idade como subtipo em Quantidade
    * Data como um subtipo em DateTime
    * Email 
    * Número de telefone (somente nos EUA)
    * URL
    * Endereço IP

> [!div class="nextstepaction"]
> [Saiba mais sobre o Reconhecimento de Entidade Sélo v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para reconhecer tipos de entidades de informações pessoais (somente inglês)

* Pontos finais separados para [reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [vinculação de entidades.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Versão do](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`modelo, que inclui:
    * Detecção e categorização ampliadas de entidades encontradas no texto. 
    * Reconhecimento dos seguintes novos tipos de entidades:
        * Número de telefone
        * Endereço IP

A vinculação de entidades suporta inglês e espanhol. O suporte à linguagem NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 em versão prévia pública

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) para analisar o sentimento.
* [Versão do](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`modelo, que inclui:

    * Melhorias significativas na precisão e detalhes da categorização e pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos no texto.
    * Análise de sentimento e saída em um nível de documento e sentença. 

Suporta inglês (`en`),`ja`japonês (`zh-Hans`), chinês`zh-Hant`simplificado`fr`( ),`it`chinês`es`( ),`nl`francês`pt`( ),`de`italiano ( ), espanhol ( `Australia East`), `Central Canada` `Central US`português `East Asia` `East US`( `East US 2` `North Europe`), português ( ), e alemão ( ), e está disponível nas seguintes regiões: , , , , , , `Southeast Asia`, , `South Central US` `UK South`, , , `West Europe`, e `West US 2`. 

> [!div class="nextstepaction"]
> [Saiba mais sobre Análise de Sentimentos v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Análise de Texto?](overview.md)  
* [Cenários de usuário de exemplo](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Detecção de idiomas](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidades](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md)
