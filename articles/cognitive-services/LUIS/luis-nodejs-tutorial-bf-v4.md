---
title: 'Tutorial: Bot de Reconhecimento vocal Node.js v4'
description: Usando Node.js, crie um chat bot integrado com o LUIS (Reconhecimento vocal) neste tutorial. Este chat bot usa o aplicativo de Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com o Bot Framework versão 4 e o bot de aplicativo Web do Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987826"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Use um Bot de aplicativo Web habilitado com Reconhecimento vocal em Node.js

Use o Node.js para compilar um chat bot integrado com reconhecimento vocal (LUIS). O bot é criado com o recurso [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) e o [Bot Framework versão](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um bot de aplicativo Web. Esse processo cria um novo aplicativo LUIS para você.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Exibir resultados de enunciado no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot do aplicativo Web

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, pesquise e selecione **Bot do aplicativo Web**. Selecione **Criar**.

1. Em **Serviço de bot**, forneça as informações necessárias:

    |Configuração|Finalidade|Configuração sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Subscription|Assinatura na qual criar o bot.|Sua assinatura primária.
    |Resource group|Grupo lógico de recursos do Azure|Crie um novo grupo para armazenar todos os recursos usados com este bot, dê um nome ao grupo `luis-nodejs-bot-resource-group`.|
    |Location|Região do Azure – não precisa ser a mesma que a região de publicação ou criação do LUIS.|`westus`|
    |Tipo de preço|Usado para os limites e a cobrança da solicitação de serviço.|`F0` é a camada gratuita.
    |Nome do aplicativo|O nome é usado como o subdomínio quando seu bot é implantado na nuvem (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Modelo de bot|Configurações do Bot framework – consulte a próxima tabela|
    |Local do aplicativo LUIS|Deve ser o mesmo que a região do recurso do LUIS|`westus`|
    |Local/plano de serviço de aplicativo|Não altere o valor padrão fornecido.|
    |Application Insights|Não altere o valor padrão fornecido.|
    |ID do Aplicativo e senha da Microsoft|Não altere o valor padrão fornecido.|

1. No **Modelo de bot**, selecione o seguinte e escolha o botão **Selecionar** nestas configurações:

    |Configuração|Finalidade|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot framework|**SDK v4**|
    |Linguagem do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|

1. Selecione **Criar**. Isso cria e implanta o serviço de bot no Azure. Parte deste processo cria um aplicativo LUIS chamado `luis-nodejs-bot-XXXX`. Este nome é baseado no nome do aplicativo de Serviço de Bot do Azure.

    > [!div class="mx-imgBorder"]
    > [![Criar um bot de aplicativo Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

1. Quando a caixa de diálogo pop-up perguntar **Deseja incluir as configurações do aplicativo no arquivo zip baixado?** , selecione **Sim**. Isso fornece as configurações do LUIS.

1. Quando o código-fonte for compactado, uma mensagem fornecerá um link para baixar o código. Selecione o link.

1. Salve o arquivo zip em seu computador local e extraia os arquivos. Abra o projeto com o Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar o enunciado para o LUIS e obter resposta

1. Para enviar o enunciado do usuário para o ponto de extremidade de previsão do LUIS, abra o arquivo **diálogos -> flightBookingRecognizer.cs**. É aí que o enunciado do usuário inserido no bot é enviado para o LUIS. A resposta do LUIS é retornada do método **executeLuisQuery**.

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **diálogos -> mainDialog** captura o enunciado e o envia para executeLuisQuery no método actStep.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

Fazer uma pergunta ao bot para a intenção de Reservar Voo.

1. Inicie o Emulador do Bot e selecione **Abrir Bot**.
1. Na caixa de diálogo exibida **Abrir um bot**, digite sua URL, como `http://localhost:3978/api/messages`. A rota `/api/messages` é o endereço web do bot.
1. Insira a **ID do aplicativo Microsoft** e a **senha do Microsoft App**, localizada no arquivo **.env** na raiz do código do bot que você baixou.

1. No emulador do bot, digite `Book a flight from Seattle to Berlin tomorrow` e obtenha a mesma resposta para o bot básico como você recebeu em **Testa no Webchat**.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim** na barra superior. O bot responde com um resumo das ações dele.
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON do LUIS para a intenção e as entidades do enunciado.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Próximas etapas

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação.

> [!div class="nextstepaction"]
> [Crie um aplicativo de Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)