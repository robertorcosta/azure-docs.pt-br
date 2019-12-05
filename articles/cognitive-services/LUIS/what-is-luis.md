---
title: O que é Reconhecimento Vocal (LUIS)?
titleSuffix: Azure Cognitive Services
description: O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456560"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

O Reconhecimento vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural para prever o significado geral e extrair informações detalhadas relevantes. 

Por exemplo, quando um aplicativo cliente envia o texto, `find me a wireless keyboard for $30`, o LUIS responde com o objeto JSON a seguir. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
No exemplo acima, a _**intenção**_ ou o significado geral da frase é que o usuário está tentando localizar um item. As partes detalhadas das informações que o LUIS extrai são chamadas de _**entidades**_ . Nesse caso, as entidades são o nome do item que o usuário está procurando e a quantidade de dinheiro que ele deseja gastar.

Os aplicativos cliente usam o JSON retornado pelo LUIS, a _intenção_ (categoria) e as _entidades_ (informações detalhadas extraídas) para gerar ações no aplicativo cliente. Um aplicativo cliente para LUIS costuma ser um aplicativo de conversa que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala. 

![Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivos (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceitual de três aplicativos cliente que trabalham com Reconhecimento vocal de serviços cognitivos (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Exemplo de uso do LUIS em um chatbot

<a name="Accessing-LUIS"></a>

Um aplicativo cliente envia expressões (texto) para a [API][endpoint-apis] do ponto de extremidade de processamento da linguagem natural do LUIS publicada e recebe os resultados como respostas em JSON. Um aplicativo cliente comum para LUIS é um chatbot.


![Imagens conceituais do LUIS trabalhando com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceituais do LUIS que trabalham com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)")

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _expressão_ do usuário (texto em suas próprias palavras), “Eu quero chamar meu representante de RH”. para o ponto de extremidade do LUIS como uma solicitação HTTP.|
|2|O LUIS aplica modelos de idioma aprendidos pela máquina ao texto de entrada não estruturado do usuário e retornam uma resposta formatada em JSON, com uma das principais intenções, `HRContact`. A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do _Tipo de contato_.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como atender às solicitações do usuário. Essas decisões podem incluir algumas árvores de decisão no bot e chamadas a outros serviços. |

O aplicativo LUIS fornece inteligência, para que o aplicativo cliente possa fazer escolhas inteligentes. O LUIS não oferece essas escolhas. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de idioma natural

Seu aplicativo LUIS contém modelos de linguagem natural específicos de domínio que funcionam juntos. Você pode iniciar o aplicativo LUIS com um ou mais modelos predefinidos, criar seu próprio modelo ou combinar modelos predefinidos com suas próprias informações personalizadas.

* O LUIS de **modelo predefinido** tem muitos domínios pré-criados que incluem modelos de intenção e entidade que funcionam em conjunto para completar cenários de uso comuns. Esses domínios incluem rótulos de declarações que podem ser inspecionados e editados, permitindo que você os personalize. [Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente. Além disso, há entidades predefinidas, como moeda e número, que você pode usar independentemente dos domínios predefinidos.

* O LUIS de **modelo personalizado** oferece várias maneiras de criar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades aprendidas por máquina, entidades de correspondência de padrões e uma combinação de aprendidas por máquina e correspondência de padrões.

## <a name="build-the-luis-app"></a>Criar o aplicativo LUIS
Crie o aplicativo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [portal do LUIS](https://www.luis.ai).

O aplicativo LUIS começa com categorias de texto de entrada chamadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do usuário. Cada expressão pode fornecer vários dados que precisam ser extraídos. 

|Expressão de usuário de exemplo|Intenção|Data da extração|
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

## <a name="iterative-development-lifecycle"></a>Ciclo de vida de desenvolvimento iterativo
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores do LUIS para integrar ao [ciclo de vida de desenvolvimento](luis-concept-app-iteration.md) iterativo completo. 

## <a name="implementing-luis"></a>Implementando o LUIS
O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. A lista a seguir contém os principais produtos e serviços da Microsoft usados com o LUIS.

O principal aplicativo cliente para o LUIS é:
* [Bot de aplicativo Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) que cria rapidamente um chatbot habilitado para LUIS para conversar com um usuário por meio de entrada de texto. Usa o [Bot Framework][bot-framework] versão [4.x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência de bot completa.

Ferramentas para usar o LUIS com um bot de modo rápido e fácil:
* [CLI do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece criação e previsão como uma ferramenta de linha de comando autônoma ou como uma importação. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) O LUISGen é uma ferramenta para gerar código-fonte typescript e C# fortemente tipado de um modelo do LUIS exportado.
* [Expedição](https://aka.ms/dispatch-tool) permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown é uma ferramenta de linha de comando que ajuda a gerenciar modelos de linguagem para o bot.
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
* Criar um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md)
* [Consulte o ponto de extremidade de previsão](luis-get-started-get-intent-from-browser.md) de um aplicativo de IoT público. 
* [Recursos para desenvolvedores](developer-reference-resource.md) para LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
