---
title: LUIS e QnAMaker - integração de bots
titleSuffix: Azure Cognitive Services
description: À medida que sua base de conhecimento QnA Maker cresce, torna-se difícil mantê-la como um único conjunto monolítico. Divida a base de conhecimento em pedaços menores e lógicos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402717"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Use um bot com QnA Maker e LUIS para distribuir sua base de conhecimento
À medida que sua base de conhecimento QnA Maker cresce, torna-se difícil mantê-la como um único conjunto monolítico. Divida a base de conhecimento em pedaços menores e lógicos.

Embora seja simples criar múltiplas bases de conhecimento no QnA Maker, você precisará de alguma lógica para direcionar a questão de entrada para a base de conhecimento apropriada. Você pode fazer isso usando o LUIS.

Este artigo usa o Bot Framework v3 SDK. Se você estiver interessado na versão Bot Framework v4 SDK dessas informações, consulte [Use vários modelos LUIS e QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Arquitetura

![Arquitetura gráfica mostrando o QnA Maker com a compreensão da linguagem](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

O gráfico anterior mostra que o QnA Maker primeiro obtém a intenção da pergunta recebida de um modelo LUIS. Em seguida, o QnA Maker usa essa intenção para direcionar a questão para a base de conhecimento correta do QnA Maker.

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

1. Faça login no portal [LUIS.](https://www.luis.ai/)
1. [Crie um aplicativo](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Adicione uma intenção](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) para cada base de dados de conhecimento do QnA Maker. As declarações de exemplo devem corresponder às perguntas nas bases de dados de conhecimento do QnA Maker.
1. [Treine o aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publique o aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Na seção **Gerenciar,** anote o iD do aplicativo LUIS, a tecla de ponto final LUIS e [o nome de domínio personalizado](../../cognitive-services-custom-subdomains.md). Você precisará desses valores mais tarde.

## <a name="create-qna-maker-knowledge-bases"></a>Criar bases de dados de conhecimento do QnA Maker

1. Faça login no [QnA Maker](https://qnamaker.ai).
1. [Crie](https://www.qnamaker.ai/Create) uma base de conhecimento para cada intenção no aplicativo LUIS.
1. Teste e publique as bases de dados de conhecimento. Quando você publicar cada um, anote o ID, o nome do recurso (o subdomínio personalizado antes _de .azurewebsites.net/qnamaker)_ e a tecla de ponto final de autorização. Você precisará desses valores mais tarde.

    Este artigo pressupõe que as bases de conhecimento são todas criadas na mesma assinatura do Azure QnA Maker.

    ![Captura de tela do pedido QnA Maker HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot do aplicativo Web

1. [Crie um bot "Básico" com o Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), que inclui automaticamente um aplicativo LUIS. Selecione a linguagem de programação C#.

1. Depois de criar o bot do aplicativo web, no portal Azure, selecione o bot do aplicativo web.
1. Na navegação de serviço de bot do aplicativo web, selecione **Configurações de aplicativo**. Em seguida, role até a seção **Configurações** do aplicativo das configurações disponíveis.
1. Altere o **LuisAppId** para o valor do aplicativo LUIS criado na seção anterior. Em seguida, **selecione Salvar**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Alterar o código no arquivo BasicLuisDialog.cs
1. Na seção **Gerenciamento de Bot** da navegação do bot do aplicativo Web no portal do Azure, selecione **Compilar**.
2. Selecione **Abrir editor de código online**. Uma nova guia do navegador é aberta com o ambiente de edição online.
3. Na seção **WWWROOT,** selecione o diretório **Diálogos** e abra **BasicLuisDialog.cs**.
4. Adicione dependências na parte superior do arquivo **BasicLuisDialog.cs**:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Adicione as seguintes classes para desserializar a resposta do QnA Maker:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Adicione a seguinte classe para fazer uma solicitação HTTP ao serviço do QnA Maker. Observe que **Authorization** o valor do cabeçalho `EndpointKey`de autorização inclui a palavra, com um espaço seguindo a palavra. O resultado json é desserializado nas classes anteriores, e a primeira resposta é devolvida.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modifique `BasicLuisDialog` a classe. Cada intenção do LUIS deve ter um método decorado com **LuisIntent**. O parâmetro para a decoração é o nome real da intenção do LUIS. O nome do método que é decorado _deve_ ser o nome de intenção LUIS para legibilidade e manutenção, mas não precisa ser o mesmo no design ou tempo de execução.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Criar o bot
1. No editor de códigos, clique com o botão direito do mouse **build.cmd**e selecione **Executar a partir do console**.

    ![Captura de tela da opção Executar do Console no editor de código](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A visualização de código é substituída por uma janela de terminal que mostra o progresso e os resultados da compilação.

    ![Captura de tela da compilação do console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testar o bot
No portal do Azure, selecione **Testar no Webchat** para testar o bot. Digite mensagens com intenções diferentes para obter a resposta da base de dados de conhecimento correspondente.

![Captura de tela do teste de bate-papo na Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Integre sua base de conhecimento com um agente em Power Virtual Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
