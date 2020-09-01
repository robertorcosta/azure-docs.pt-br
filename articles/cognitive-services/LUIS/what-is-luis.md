---
title: O que é Reconhecimento Vocal (LUIS)?
description: 'LUIS (Reconhecimento vocal): um serviço de API baseado em nuvem que usa o machine learning em idioma natural e de conversa para prever o significado e extrair informações.'
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, natural language understanding, nlu, ai conversation, conversational ai, ai chatbot, chatbot maker, LUIS, nlp ai, luis ai, azure luis, understanding natural language
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 22fe99e1552a9612adfbc455d60852f1591a1a54
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752155"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes.

Um aplicativo cliente para LUIS é qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.

![Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivos (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceitual de três aplicativos cliente que trabalham com Reconhecimento vocal de serviços cognitivos (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Usar o LUIS em um chatbot

<a name="Accessing-LUIS"></a>

Depois que o aplicativo LUIS for publicado, um aplicativo cliente envia expressões (texto) para a [API][endpoint-apis] do ponto de extremidade de processamento da linguagem natural do LUIS e recebe os resultados como respostas em JSON. Um aplicativo cliente comum para LUIS é um chatbot.


![Imagens conceituais do LUIS trabalhando com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceituais do LUIS que trabalham com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)")

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _expressão_ do usuário (texto em suas próprias palavras), “Eu quero chamar meu representante de RH”. para o ponto de extremidade do LUIS como uma solicitação HTTP.|
|2|O LUIS permite que você crie seus modelos de linguagem personalizados para adicionar inteligência ao seu aplicativo. Os modelos de idioma aprendidos pela máquina pegam o texto de entrada não estruturado do usuário e retornam uma resposta formatada em JSON, com uma das principais intenções, `HRContact`. A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do _Tipo de contato_.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como atender às solicitações do usuário. Essas decisões podem incluir algumas árvores de decisão no código do Bot Framework e chamadas para outros serviços. |

O aplicativo LUIS fornece inteligência, para que o aplicativo cliente possa fazer escolhas inteligentes. O LUIS não oferece essas escolhas.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>NLU (Reconhecimento de idioma natural)

O [LUIS fornece IA (inteligência artificial)](artificial-intelligence.md) na forma de NLU, um subconjunto do NLP (processamento de idioma natural).

Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico. Você pode iniciar o aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio modelo ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

* **Modelo predefinido** LUIS tem muitos modelos de domínio que incluem intenções, expressões e entidades predefinidas. Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. [Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente.

* **O modelo personalizado** LUIS oferece várias maneiras de identificar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades de aprendizado de máquina, entidades específicas ou literais e uma combinação de aprendizado de máquina e literal.

Saiba mais sobre o [NLP](artificial-intelligence.md) e a área específica do LUIS de NLU.

## <a name="step-1-design-and-build-your-model"></a>Etapa 1: Projetar e criar seu modelo

Projete seu modelo com as categorias de intenções de usuário chamadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do usuário. Cada enunciado pode fornecer dados que precisam ser extraídos com [entidades de machine learning](luis-concept-entity-types.md#effective-machine-learned-entities).

|Expressão de usuário de exemplo|Intencional|Data da extração|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|Abrir|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13h, Bob|

Crie o modelo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087), o [**portal do LUIS**](https://www.luis.ai) ou ambos. Saiba mais sobre como criar um modelo com o [portal](get-started-portal-build-app.md) e as [bibliotecas de clientes do SDK](quickstart-sdk.md).

## <a name="step-2-get-the-query-prediction"></a>Etapa 2: Obter a previsão da consulta

Depois que o modelo do aplicativo é treinado e publicado no ponto de extremidade, um aplicativo cliente (como um chatbot) envia os enunciados à API do [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) de previsão. A API aplica o modelo ao enunciado para análise e responde com os resultados da previsão em um formato JSON.

A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade e o sentimento geral do **Tipo de contato** seguinte.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Etapa 3: Melhorar a previsão do modelo

Depois que o aplicativo LUIS for publicado e receber expressões de usuário real, LUIS fornecerá [aprendizado ativo](luis-concept-review-endpoint-utterances.md) de expressões do ponto de extremidade para melhorar a precisão da previsão. Examine essas sugestões como parte do seu trabalho de manutenção regular no ciclo de vida de desenvolvimento.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Ciclo de vida de desenvolvimento e ferramentas
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores do LUIS para integrar ao [ciclo de vida de desenvolvimento completo](luis-concept-app-iteration.md).

O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. O LUIS também fornece SDKs (bibliotecas de clientes) para várias das principais linguagens de programação. Saiba mais sobre os [recursos para desenvolvedor](developer-reference-resource.md) fornecidos.

Ferramentas para usar o LUIS com um bot de modo rápido e fácil:
* [CLI do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece criação e previsão como uma ferramenta de linha de comando autônoma ou como uma importação.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) O LUISGen é uma ferramenta para gerar código-fonte typescript e C# fortemente tipado de um modelo do LUIS exportado.
* [Expedição](https://aka.ms/dispatch-tool) permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) O LUDown é uma ferramenta de linha de comando que ajuda a gerenciar modelos de linguagem para o bot.

## <a name="integrate-with-a-bot"></a>Integração a um bot

Use o [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) com o [Microsoft Bot Framework](https://dev.botframework.com/) para criar e implantar um chatbot. Projete e desenvolva bots com a ferramenta de interface gráfica [Composer](https://docs.microsoft.com/composer/) ou os [exemplos de bots funcionais](https://github.com/microsoft/BotBuilder-Samples) projetados para os principais cenários de bots.

## <a name="integrate-with-other-cognitive-services"></a>Integração a outros Serviços Cognitivos

Outros Serviços Cognitivos usados com o LUIS:
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [Serviço de Fala](../Speech-Service/overview.md) converte as solicitações de linguagem falada em texto.

O LUIS fornece a funcionalidade de Análise de Texto como parte dos recursos existentes. Essa funcionalidade inclui a [Análise de Sentimento](luis-how-to-publish-app.md#configuring-publish-settings) e a [Extração de Frases-chave](luis-reference-prebuilt-keyphrase.md) com a entidade keyPhrase predefinida.

## <a name="learn-with-the-quickstarts"></a>Saiba mais com os guias de início rápido

Saiba mais sobre o LUIS com guias de início rápido práticos usando o [portal](get-started-portal-build-app.md) e as [bibliotecas de clientes do SDK](quickstart-sdk.md).


## <a name="next-steps"></a>Próximas etapas

* [Novidades](whats-new.md) do serviço e da documentação
* [Planeje seu aplicativo](luis-how-plan-your-app.md) com [intenções](luis-concept-intent.md) e [entidades](luis-concept-entity-types.md).
* [Consulte o ponto de extremidade de previsão](luis-get-started-get-intent-from-browser.md).
* [Recursos para desenvolvedores](developer-reference-resource.md) para LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
