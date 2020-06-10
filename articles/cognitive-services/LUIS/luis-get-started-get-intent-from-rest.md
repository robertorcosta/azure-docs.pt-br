---
title: 'Início Rápido: Obter a intenção com as APIs REST – LUIS'
description: Neste início rápido da API REST, use um aplicativo LUIS público disponível para determinar a intenção de um usuário com base no texto de conversa.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 5d43a1aee9e3f355a3cfcab927d87571798677e7
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608546"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Início Rápido: Obter a intenção com as APIs REST

Neste início rápido, você usará um aplicativo LUIS para determinar a intenção do usuário com base no texto de conversa. Envie a intenção do usuário como texto para o ponto de extremidade de previsão de HTTP do aplicativo Pizza. No ponto de extremidade, o LUIS aplica o modelo do aplicativo Pizza para analisar o texto de linguagem natural quanto ao significado, determinando a intenção geral e extraindo dados relevantes para o domínio do assunto do aplicativo.

Para este artigo, você precisará de uma conta gratuita do [LUIS](https://www.luis.ai).

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
