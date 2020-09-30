---
title: 'Início Rápido: Bibliotecas de clientes do SDK do LUIS (Reconhecimento vocal)'
description: Crie e consulte um aplicativo LUIS com as bibliotecas de clientes do SDK do LUIS com este guia de início rápido usando C#, Python ou JavaScript.
ms.topic: quickstart
ms.date: 09/14/2020
ms.service: cognitive-services
ms.subservice: language-understanding
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, LUIS, azure luis, natural language understanding, ai chatbot, chatbot maker,  understanding natural language
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: c2bc607fb5a5b5b38249453a1d0fce833547191f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327250"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Início Rápido: Bibliotecas de clientes do SDK do LUIS (Reconhecimento vocal) para criar e consultar seu aplicativo LUIS

Crie e consulte um aplicativo de IA (inteligência artificial) do com as bibliotecas de clientes do SDK do Azure LUIS com este guia de início rápido usando C#, Python ou JavaScript.

O LUIS (Reconhecimento Vocal) permite aplicar o NLP (processamento de idioma natural) a um texto de idioma natural de conversa do usuário para prever o significado geral e extrair informações detalhadas relevantes.

* A biblioteca de clientes do **SDK de criação** permite criar, editar, treinar e publicar seu aplicativo LUIS. * A biblioteca de clientes do **SDK do runtime de previsão** permite consultar o aplicativo publicado.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode excluir o aplicativo do [portal do LUIS](https://www.luis.ai) e excluir os recursos do Azure no [portal do Azure](https://portal.azure.com/).

## <a name="troubleshooting"></a>Solução de problemas

* Autenticação na biblioteca de clientes – erros de autenticação geralmente indicam que foram usados a chave e o ponto de extremidade incorretos. Este guia de início rápido usa a chave e o ponto de extremidade de criação para o runtime de previsão, mas só funcionará se você ainda não tiver usado a cota mensal. Se você não puder usar a chave e o ponto de extremidade de criação, precisará usar a chave e o ponto de extremidade do runtime de previsão ao acessar a biblioteca de cliente do SDK do runtime de previsão.
* Criação de entidades – se você receber um erro durante a criação da entidade de machine learning aninhada usada neste tutorial, verifique se você copiou o código e se não o alterou para criar uma entidade diferente.
* Criação de enunciados de exemplo – se você receber um erro durante a criação do enunciado de exemplo rotulado usado neste tutorial, verifique se você copiou o código e não o alterou para criar um exemplo rotulado diferente.
* Treinamento – se você recebe um erro de treinamento, isso geralmente indica um aplicativo vazio (sem intenções com os enunciados de exemplo) ou um aplicativo com tentativas ou entidades malformadas.
* Erros diversos – como o código faz chamadas às bibliotecas do cliente com objetos JSON e de texto, verifique se você não alterou o código.

Outros erros – se você receber um erro não abordado na lista anterior, informe-nos fornecendo comentários na parte inferior desta página. Inclua a linguagem de programação e a versão das bibliotecas de clientes instaladas.

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do LUIS (Reconhecimento Vocal)?](what-is-luis.md)
* [Novidades](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md), [exemplos de enunciados](luis-concept-utterance.md) e [entidades predefinidas](luis-reference-prebuilt-entities.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
* Noções básicas do idioma natural: [NLU (reconhecimento de idioma natural) e NLP (processamento de idioma natural)](artificial-intelligence.md)
* Bots: [Chatbots de IA](luis-csharp-tutorial-bf-v4.md "tutorial do chatbot maker")
