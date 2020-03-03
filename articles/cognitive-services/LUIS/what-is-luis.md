---
title: O que é Reconhecimento Vocal (LUIS)?
description: O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: c74b842d27146683ef3168a534b9a012ae48fcad
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587032"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

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

## <a name="natural-language-processing"></a>Processamento de idioma natural

Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico. Você pode iniciar o aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio modelo ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

* **Modelo predefinido** LUIS tem muitos modelos de domínio que incluem intenções, expressões e entidades predefinidas. Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. [Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente.

* **O modelo personalizado** LUIS oferece várias maneiras de identificar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades aprendidas por máquina, entidades específicas ou literais e uma combinação de aprendidas por máquina e literal.

## <a name="build-the-luis-model"></a>Criar o modelo LUIS
Criar o modelo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [portal do LUIS](https://www.luis.ai).

O modelo LUIS começa com categorias de intenções de usuário chamadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do usuário. Cada expressão pode fornecer vários dados que precisam ser extraídos.

|Expressão de usuário de exemplo|Intencional|Data da extração|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|Abrir|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|13h, Bob|

## <a name="query-prediction-endpoint"></a>Ponto de extremidade de previsão da consulta

Depois que o aplicativo é treinado e publicado no ponto de extremidade, o aplicativo cliente envia expressões à API de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) da previsão publicada. A API aplica o aplicativo à expressão para análise e responde com os resultados da previsão em um formato JSON.

A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade e o sentimento geral do **Tipo de contato** seguinte.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
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
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Melhorar a previsão do modelo

Depois que o aplicativo LUIS for publicado e receber expressões de usuário real, LUIS fornecerá [aprendizado ativo](luis-concept-review-endpoint-utterances.md) de expressões do ponto de extremidade para melhorar a precisão da previsão.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores do LUIS para integrar ao [ciclo de vida de desenvolvimento completo](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Implementando o LUIS
O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. A lista a seguir contém os principais produtos e serviços da Microsoft usados com o LUIS.

O principal aplicativo cliente para o LUIS é:
* [Bot de aplicativo Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) que cria rapidamente um chatbot habilitado para LUIS para conversar com um usuário por meio de entrada de texto. Usa o [Bot Framework][bot-framework] versão [4.x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência de bot completa.

Ferramentas para usar o LUIS com um bot de modo rápido e fácil:
* [CLI do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece criação e previsão como uma ferramenta de linha de comando autônoma ou como uma importação.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) O LUISGen é uma ferramenta para gerar código-fonte typescript e C# fortemente tipado de um modelo do LUIS exportado.
* [Expedição](https://aka.ms/dispatch-tool) permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) O LUDown é uma ferramenta de linha de comando que ajuda a gerenciar modelos de linguagem para o bot.
* [Bot Framework – Composer](https://github.com/microsoft/BotFramework-Composer) – uma ferramenta de desenvolvimento integrada para desenvolvedores e equipes multidisciplinares destinada a criar bots e experiências de conversa com o Microsoft Bot Framework

Outros Serviços Cognitivos usados com o LUIS:
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [Serviço de Fala](../Speech-Service/overview.md) converte as solicitações de linguagem falada em texto.
* [Aprendiz de conversa](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) permite que você crie conversas de bot mais rapidamente com o LUIS.

Exemplos usando o LUIS:
* Repositório GitHub de [Inteligência Artificial de conversação](https://github.com/Microsoft/AI).
* [Estrutura do bot - Amostras de bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Próximas etapas

* [Novidades](whats-new.md)
* Crie um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md).
* [Consulte o ponto de extremidade de previsão](luis-get-started-get-intent-from-browser.md) de um aplicativo de IoT público.
* [Recursos para desenvolvedores](developer-reference-resource.md) para LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/