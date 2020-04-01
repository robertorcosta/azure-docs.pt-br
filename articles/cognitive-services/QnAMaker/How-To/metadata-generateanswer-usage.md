---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite adicionar metadados na forma de pares chave-valor aos conjuntos de perguntas/respostas. Você pode filtrar resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474860"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API e metadados do GenerateAnswer

Para obter a resposta prevista para a pergunta de um usuário, use a API Gerar Resposta. Quando você publica uma base de conhecimento, você pode ver informações sobre como usar esta API na página **Publicar.** Você também pode configurar a API para filtrar respostas com base em tags de metadados e testar a base de conhecimento a partir do ponto final com o parâmetro de seqüência de consulta de teste.

O QnA Maker permite adicionar metadados, na forma de pares de chaves e valores, aos seus conjuntos de perguntas e respostas. Em seguida, você pode usar essas informações para filtrar resultados para consultas de usuário e para armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazene perguntas e respostas com uma entidade QnA

É importante entender como o QnA Maker armazena os dados de perguntas e respostas. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar o ID para fazer atualizações para uma entidade qnA específica.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenha previsões de resposta com a API GenerateAnswer

Você usa a [API GerarResposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) em seu bot ou aplicativo para consultar sua base de conhecimento com uma pergunta do usuário, para obter a melhor correspondência dos conjuntos de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter o ponto final do GenerateAnswer

Depois de publicar sua base de conhecimento, seja no [portal QnA Maker,](https://www.qnamaker.ai)ou usando a [API,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)você pode obter os detalhes do seu ponto final do GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Faça login [https://www.qnamaker.ai](https://www.qnamaker.ai)em .
1. Em **Minhas bases de conhecimento,** **selecione 'Exibir código'** para sua base de conhecimento.
    ![Captura de tela das minhas bases de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    ![Captura de tela de detalhes do ponto final](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação do GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

A solicitação DO POST usa:

* Parâmetros [URI necessários](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Propriedade de `Authorization`cabeçalho necessária, para segurança
* [Propriedades corporais necessárias](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

O URL GenerateAnswer tem o seguinte formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir `Authorization` a propriedade http `EndpointKey` header de com um valor da seqüência com um espaço de arrasto, em seguida, a tecla de ponto final encontrada na página **Configurações.**

Um exemplo de corpo JSON se parece com:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Saiba mais sobre [rankerType](../concepts/best-practices.md#choosing-ranker-type).

O JSON anterior solicitou apenas respostas que estão em 30% ou acima da pontuação limite.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Gerar propriedades de resposta do GenerateAnswer

A [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações necessárias para exibir a resposta e a próxima volta na conversa, se disponível.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

O JSON anterior respondeu com uma resposta com uma pontuação de 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Use QnA Maker com um bot em C #

A estrutura bot fornece acesso às propriedades do QnA Maker com a [API getAnswer](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O JSON anterior solicitou apenas respostas que estão em 30% ou acima da pontuação limite.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use QnA Maker com um bot em Node.js

A estrutura bot fornece acesso às propriedades do QnA Maker com a [API getAnswer](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O JSON anterior solicitou apenas respostas que estão em 30% ou acima da pontuação limite.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Use metadados para filtrar respostas por tags de metadados personalizadas

A adição de metadados permite filtrar as respostas por essas marcas de metadados. Adicione a coluna de metadados do menu **'Opções de exibição'.** Adicione metadados à sua base de **+** conhecimento selecionando o ícone de metadados para adicionar um par de metadados. Este par consiste em uma chave e um valor.

![Captura de tela de adicionar metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "Quando esse hotel fecha?", onde a intenção está implícita para o restaurante "Paraíso".

Como os resultados são necessários apenas para o restaurante "Paradise", você pode definir um filtro na chamada GenerateAnswer nos metadados "Nome do Restaurante". O exemplo a seguir mostra o seguinte:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Use resultados de perguntas e respostas para manter o contexto da conversa

A resposta ao GenerateAnswer contém as informações de metadados correspondentes do conjunto de perguntas e respostas combinadas. Você pode usar essas informações no aplicativo do cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Perguntas apenas de correspondência, por texto

Por padrão, o QnA Maker pesquisa através de perguntas e respostas. Se você quiser pesquisar apenas através de perguntas, `RankerType=QuestionOnly` para gerar uma resposta, use o no corpo POST da solicitação GenerateAnswer.

Você pode pesquisar através do `isTest=false`kb publicado, usando `isTest=true`, ou no teste kb usando .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Erros HTTP comuns

|Código|Explicação|
|:--|--|
|2xx|Sucesso|
|400|Os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|O corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Chave inválida|
|403|Proibido – você não tem as permissões corretas|
|404|O KB não existe|
|410|Essa API foi preterida e não está mais disponível|

## <a name="next-steps"></a>Próximas etapas

A página **Publicar** também fornece informações para [gerar uma resposta](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) com carteiro ou cURL.

> [!div class="nextstepaction"]
> [Criar um bot da base de dados de conhecimento](../tutorials/integrate-qnamaker-luis.md)
