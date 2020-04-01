---
title: Melhorar a base de dados de conhecimento – QnA Maker
description: Melhore a qualidade de sua base de conhecimento com aprendizado ativo. Revisar, aceitar ou rejeitar, adicionar sem remover ou alterar perguntas existentes.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1eb0ed42f700c14350a5e4f1eff9b7592cbf8ef6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474894"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Aceite questões sugeridas de aprendizagem ativa na base de conhecimento


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

O Aprendizado Ativo altera a Base de Conhecimento ou o Serviço de Pesquisa depois de aprovar a sugestão, em seguida, salvar e treinar. Se você aprovar a sugestão, ela será adicionada como uma pergunta alternativa.

## <a name="turn-on-active-learning"></a>Ativar o aprendizado ativo

Para ver as perguntas sugeridas, você deve [ativar o aprendizado ativo](use-active-learning.md) para o seu recurso QnA Maker.

## <a name="view-suggested-questions"></a>Ver perguntas sugeridas

1. Para ver as perguntas sugeridas, na página base de conhecimento **Editar,** selecione **'Exibir opções'** e selecione **Mostrar sugestões ativas de aprendizagem**.

    [![Na seção Editar do portal, selecione Mostrar Sugestões para ver as novas alternativas de perguntas do aprendizado ativo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtre a base de conhecimento com pares de perguntas e respostas para mostrar apenas sugestões selecionando **Filtro por Sugestões**.

    [![Use o Filtro por sugestões alternadas para visualizar apenas as alternativas de perguntas sugeridas pelo aprendizado ativo.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Cada par de QnA sugere as novas `✔` alternativas de pergunta `x` com uma marca de verificação, para aceitar a pergunta ou rejeitar as sugestões. Selecione a marca de seleção para adicionar a pergunta.

    [![Selecione ou rejeite as alternativas de pergunta sugeridas do aprendizado ativo selecionando a marca de seleção verde ou a marca de exclusão vermelha.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Você pode adicionar ou excluir _todas as sugestões_ selecionando **Adicionar tudo** ou Rejeitar **todos** na barra de ferramentas contextual.

1. Selecione **Salvar e Treinar** para salvar as alterações à base de dados de conhecimento.

1. Selecione **Publicar** para permitir que as alterações estejam disponíveis na [API Gerar Resposta](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, o QnA Maker sugere as perguntas alternativas para você aceitar ou rejeitar.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Fluxo arquitetônico para usar APIs de GerarResposta e Treinar a partir de um bot

Um bot ou outro aplicativo cliente deve usar o seguinte fluxo arquitetônico para usar o aprendizado ativo:

* O Bot [obtém a resposta da base de](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) `top` conhecimento com a API GenerateAnswer, usando a propriedade para obter uma série de respostas.
* O Bot determina o feedback explícito:
    * Usando sua própria [lógica de negócios personalizada,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtre pontuações baixas.
    * No bot ou aplicativo do cliente, exibir lista de possíveis respostas ao usuário e obter a resposta selecionada do usuário.
* O [Bot envia resposta selecionada de volta ao QnA Maker](#bot-framework-sample-code) com a [API do Trem](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Use a propriedade superior na solicitação GerarResposta para obter várias respostas correspondentes

Ao enviar uma pergunta ao QnA Maker `top` para obter uma resposta, a propriedade do corpo JSON define o número de respostas a retornar.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Use a propriedade score junto com a lógica de negócios para obter lista de respostas para mostrar ao usuário

Quando o aplicativo cliente (como um bot de bate-papo) recebe a resposta, as 3 perguntas principais são retornadas. Use `score` a propriedade para analisar a proximidade entre os escores. Essa faixa de proximidade é determinada pela sua própria lógica empresarial.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento do aplicativo cliente quando perguntas têm pontuações semelhantes

Seu aplicativo cliente exibe as perguntas com uma opção para o usuário selecionar _a única pergunta_ que mais representa sua intenção.

Uma vez que o usuário seleciona uma das perguntas existentes, o aplicativo cliente envia a escolha do usuário como feedback usando a API de trem do Fabricante qnA. Este feedback completa o ciclo de feedback de aprendizagem ativa.

## <a name="train-api"></a>API de treinamento

O feedback de aprendizagem ativa é enviado ao QnA Maker com a solicitação train API POST. A assinatura da API é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Propriedade de solicitação HTTP|Nome|Type|Finalidade|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de conhecimento|string|o GUID da base de dados de conhecimento.|
|Subdomínio personalizado|Nome do recurso QnAMaker|string|O nome do recurso é usado como subdomínio personalizado para o seu QnA Maker. Isso está disponível na página Configurações depois de publicar a base de conhecimento. Está listado como `host`o .|
|Cabeçalho|Tipo de conteúdo|string|o tipo de mídia do corpo enviado para a API. O valor padrão é:`application/json`|
|Cabeçalho|Autorização|string|sua chave de ponto de extremidade (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo postal|Objeto JSON|JSON|O feedback do treinamento|

O corpo JSON tem várias configurações:

|Propriedade do corpo JSON|Type|Finalidade|
|--|--|--|--|
|`feedbackRecords`|matriz|Lista de comentários.|
|`userId`|string|A ID do usuário da pessoa que aceita as perguntas sugeridas. O formato de ID do usuário depende de você. Por exemplo, um endereço de e-mail pode ser um ID de usuário válido em sua arquitetura. Opcional.|
|`userQuestion`|string|Texto exato da consulta do usuário. Obrigatórios.|
|`qnaID`|número|ID de pergunta, encontrado na [resposta GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Um exemplo de corpo JSON se parece com:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Uma resposta bem sucedida retorna um status de 204 e nenhum corpo de resposta JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Lote muitos registros de feedback em uma única chamada

No aplicativo do lado do cliente, como um bot, você pode armazenar os dados e, em seguida, enviar muitos registros em um único corpo JSON na `feedbackRecords` matriz.

Um exemplo de corpo JSON se parece com:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Código de amostra de estrutura de bot

Seu código de estrutura de bot precisa chamar a API do Trem, se a consulta do usuário deve ser usada para aprendizado ativo. Há duas peças de código para escrever:

* Determine se a consulta deve ser usada para aprendizado ativo
* Enviar consulta de volta à API de trem do fabricante qnA para aprendizado ativo

Na [amostra do Azure Bot,](https://aka.ms/activelearningsamplebot)ambas as atividades foram programadas.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exemplo c# código para API de trem com Bot Framework 4.x

O código a seguir ilustra como enviar informações de volta ao QnA Maker com a API train.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exemplo código Node.js para API de trem com O Quadro de Bot 4.x

O código a seguir ilustra como enviar informações de volta ao QnA Maker com a API train.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>O aprendizado ativo é salvo na base de conhecimento exportada

Quando seu aplicativo tem aprendizado ativo ativado e `SuggestedQuestions` você exporta o aplicativo, a coluna no arquivo tsv retém os dados de aprendizagem ativos.

A `SuggestedQuestions` coluna é um objeto JSON `autosuggested`de informações `usersuggested` de feedback implícito, e explícito. Um exemplo deste objeto JSON para uma única `help` pergunta enviada pelo usuário é:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Você também pode usar a API de alterações de download para rever essas alterações, usando REST ou qualquer um dos SDKs baseados em idioma:
* [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Quando você reimporta este aplicativo, o aprendizado ativo continua coletando informações e recomendando sugestões para sua base de conhecimento.



## <a name="best-practices"></a>Práticas recomendadas

Para as práticas recomendadas ao usar o aprendizado ativo, veja [Práticas recomendadas](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar metadados com a API do GenerateAnswer](metadata-generateanswer-usage.md)
