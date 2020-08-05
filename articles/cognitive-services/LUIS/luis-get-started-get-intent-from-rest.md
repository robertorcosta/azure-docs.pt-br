---
title: 'Início Rápido: Obter a intenção com as APIs REST – LUIS'
description: Neste início rápido da API REST, use um aplicativo LUIS público disponível para determinar a intenção de um usuário com base no texto de conversa.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 0274cf1ac6a6d0cb37d2321f6fd02f98c784318f
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405256"
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
