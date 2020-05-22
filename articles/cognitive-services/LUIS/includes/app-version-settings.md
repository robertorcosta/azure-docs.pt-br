---
title: incluir arquivo
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590972"
---
|Nível|Configuração da IU|Configuração da API|Informações|
|--|--|--|--|
|Aplicativo|Tornar pontos de extremidade públicos|`Public`|Qualquer pessoa pode acessar seu aplicativo público se tiver uma chave de previsão e souber a ID do aplicativo. |
|Versão|Usar treinamento não determinístico|`UseAllTrainingData`|O treinamento usa um pequeno percentual de amostragem negativa. Se você quiser usar todos os dados, em vez da pequena amostragem negativa, defina para `true`. |
|Versão|Normalizar diacríticos|`NormalizeDiacritics`|Normalizar diacríticos substitui os caracteres com sinais diacríticos nos enunciados por caracteres normais.|
|Versão|Normalizar pontuação|`NormalizePunctuation`|A normalização da pontuação significa que, antes de seus modelos serem treinados e suas consultas do ponto de extremidade serem previstas, a pontuação será removida dos enunciados.|
|Versão|Normalizar formação das palavras|`NormalizeWordForm`|Ignore a formação das palavras além da raiz.|

Aprenda os [conceitos](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) da normalização e como usar as APIs do [aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) e da [versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para atualizar essas configurações ou use a seção **Gerenciar** do portal do LUIS, página **Configurações do Aplicativo**.