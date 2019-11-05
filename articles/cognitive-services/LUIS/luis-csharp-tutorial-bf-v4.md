---
title: 'Tutorial: Bot de Reconhecimento Vocal C# v4'
titleSuffix: Azure Cognitive Services
description: Usando C#, crie um bot de bate-papo integrado com reconhecimento vocal (LUIS). O bot é criado com o Bot Framework versão 4 e o serviço de bot do aplicativo Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 213449a78baf8fc2b7cb6c74709efeaf2a50d5b2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495467"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Tutorial: Use um Bot de aplicativo Web habilitado com Reconhecimento Vocal em C#

Use C# para criar um bot de bate-papo integrado com reconhecimento vocal (LUIS). O bot é criado com o recurso [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) e o [Bot Framework versão](https://github.com/Microsoft/botbuilder-dotnet) V4.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]


**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um bot de aplicativo Web. Esse processo cria um novo aplicativo LUIS para você.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Exibir resultados de enunciado no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot do aplicativo Web

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, pesquise e selecione **Bot do aplicativo Web**. Selecione **Criar**.

1. Em **Serviço de bot**, forneça as informações necessárias:

    |Configuração|Finalidade|Configuração sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Subscription|Assinatura na qual criar o bot.|Sua assinatura primária.
    |Resource group|Grupo lógico de recursos do Azure|Crie um novo grupo para armazenar todos os recursos usados com este bot, dê um nome ao grupo `luis-csharp-bot-resource-group`.|
    |Location|Região do Azure – não precisa ser a mesma que a região de publicação ou criação do LUIS.|`westus`|
    |Tipo de preço|Usado para os limites e a cobrança da solicitação de serviço.|`F0` é a camada gratuita.
    |Nome do aplicativo|O nome é usado como o subdomínio quando seu bot é implantado na nuvem (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Modelo de bot|Configurações do Bot framework – consulte a próxima tabela|
    |Local do aplicativo LUIS|Deve ser o mesmo que a região do recurso do LUIS|`westus`|
    |Local/plano de serviço de aplicativo|Não altere o valor padrão fornecido.|
    |Application Insights|Não altere o valor padrão fornecido.|
    |ID do Aplicativo e senha da Microsoft|Não altere o valor padrão fornecido.|

1. No **Modelo de bot**, selecione o seguinte e escolha o botão **Selecionar** nestas configurações:

    |Configuração|Finalidade|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot framework|**SDK v4**|
    |Linguagem do SDK|Linguagem de programação do bot|**C#**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Isso cria e implanta o serviço de bot no Azure. Parte deste processo cria um aplicativo LUIS chamado `luis-csharp-bot-XXXX`. Este nome é baseado no nome do aplicativo de Serviço de Bot do Azure.

    [![Criar um bot de aplicativo Web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Aguarde o serviço de bot ser criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento Vocal

O processo de criação do serviço de bot também cria um novo aplicativo LUIS com intenções e enunciados de exemplo. O bot fornece mapeamento de intenções para o novo aplicativo LUIS para as seguintes intenções: 

|Intenções do LUIS do bot básico|exemplo de enunciado|
|--|--|
|Reservar voo|`Travel to Paris`|
|Cancelar|`bye`|
|GetWeather|`what's the weather like?`|
|Nenhum|Nada fora do domínio do aplicativo.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot no Webchat

1. Ainda no portal do Azure para o novo bot, selecione **Testar no Webchat**. 
1. Na caixa de texto **Digitar sua mensagem**, digite o texto `Book a flight from Seattle to Berlin tomorrow`. O bot responde com a verificação de que você deseja reservar um voo. 

    ![Captura de tela do portal do Azure, digite o texto "olá".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente seu bot. Para realizar testes mais completos, o que inclui a depuração, baixe o código de bot e use o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para desenvolver o código de bot do aplicativo Web, baixe o código e use em seu computador local. 

1. No portal do Azure, selecione **Build** na seção **Gerenciamento de bot**. 

1. Selecione **Baixar código-fonte do bot**. 

    [![Baixar o código-fonte do bot de aplicativo Web para o bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **Deseja incluir as configurações do aplicativo no arquivo zip baixado?** , selecione **Sim**.

1. Quando o código-fonte for compactado, uma mensagem fornecerá um link para baixar o código. Selecione o link. 

1. Salve o arquivo zip em seu computador local e extraia os arquivos. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar o enunciado para o LUIS e obter resposta

1. Para enviar o enunciado do usuário para o ponto de extremidade de previsão do LUIS, abra o arquivo **FlightBookingRecognizer.cs**. É aí que o enunciado do usuário inserido no bot é enviado para o LUIS. A resposta do LUIS é retornada do método **RecognizeAsync**.  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Abrir **Diálogos -> MainDialog.cs** captura o enunciado e o envia para executeLuisQuery no método actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Iniciar o código do bot no Visual Studio

No Visual Studio 2019, inicie o bot. Uma janela do navegador é aberta com o site do bot do aplicativo Web em `http://localhost:3978/`. Uma página inicial é exibida com informações sobre seu bot.

![Uma página inicial é exibida com informações sobre seu bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

1. Inicie o Emulador do Bot e selecione **Abrir Bot**.
1. Na caixa de diálogo exibida **Abrir um bot**, digite sua URL, como `http://localhost:3978/api/messages`. A rota `/api/messages` é o endereço web do bot.
1. Insira a **ID do aplicativo Microsoft** e a **senha do Microsoft App**, localizada no arquivo **appsettings.json** na raiz do código do bot que você baixou.


1. No emulador do bot, digite `Book a flight from Seattle to Berlin tomorrow` e obtenha a mesma resposta para o bot básico como você recebeu em **Testa no Webchat**.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim**. O bot responde com um resumo das ações dele. 
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON do LUIS para a intenção e as entidades do enunciado.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Próximas etapas

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação. 

> [!div class="nextstepaction"]
> [Crie um aplicativo de Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)
