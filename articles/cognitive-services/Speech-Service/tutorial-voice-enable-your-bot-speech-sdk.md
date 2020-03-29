---
title: 'Tutorial: Vozes habilitam seu bot usando O Serviço de Fala SDK - Speech'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um Echo Bot usando o Microsoft Bot-Framework, implantará-o no Azure e registrá-lo no canal Bot-Framework Direct Line Speech. Em seguida, você configurará um aplicativo cliente de exemplo para Windows que permite que você fale com seu bot e ouça-o responder de volta a você.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348546"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: Habilitar a voz do seu bot usando o Speech SDK

Agora você pode usar o poder do serviço Speech para ativar facilmente um bot de chat por voz.

Neste tutorial, você criará um Echo Bot usando o Microsoft Bot-Framework, implantará-o no Azure e registrá-lo no canal Bot-Framework Direct Line Speech. Em seguida, você configurará um aplicativo cliente de exemplo para Windows que permite que você fale com seu bot e ouça-o responder de volta a você.

Este tutorial foi projetado para desenvolvedores que estão apenas começando sua jornada com azure, bots Bot-Framework, Direct Line Speech ou o Speech SDK, e querem construir rapidamente um sistema de trabalho com codificação limitada. Não é necessária experiência ou familiaridade com esses serviços.

No final deste exercício, você terá configurado um sistema que funcionará da seguinte forma:

1. O aplicativo cliente de exemplo é configurado para se conectar ao canal Direct Line Speech e ao Echo Bot
1. O áudio é gravado a partir do microfone padrão na prensa de botão (ou continuamente gravado se a palavra-chave personalizada estiver ativada)
1. Opcionalmente, a detecção personalizada de palavras-chave acontece, gating streaming de áudio para a nuvem
1. Usando o Speech SDK, o aplicativo se conecta ao canal Direct Line Speech e transmite áudio
1. Opcionalmente, a verificação de palavras-chave de maior precisão acontece no serviço
1. O áudio é passado para o serviço de reconhecimento de fala e transcrito para texto
1. O texto reconhecido é passado para o Echo-Bot como uma atividade-quadro de bot 
1. O texto de resposta é transformado em áudio pelo serviço Text-to-Speech (TTS) e transmitido de volta para o aplicativo cliente para reprodução

![diagrama-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "O fluxo do Canal de Fala")

> [!NOTE]
> As etapas neste tutorial não exigem um serviço pago. Como um novo usuário do Azure, você poderá usar créditos da sua assinatura de teste gratuita do Azure e da camada gratuita do serviço Speech para completar este tutorial.

Este tutorial abrange:
> [!div class="checklist"]
> * Criar novos recursos do Azure
> * Construa, teste e implante a amostra do Echo Bot em um serviço de aplicativos do Azure
> * Registre seu bot no canal Direct Line Speech
> * Construa e execute o Cliente Assistente de Voz do Windows para interagir com seu Echo Bot
> * Adicionar ativação personalizada de palavras-chave
> * Aprenda a mudar a linguagem da fala reconhecida e falada

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisa para completar este tutorial:

- Um PC com Windows 10 com microfone e alto-falantes (ou fones de ouvido)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou superior
- [.NET Core SDK](https://dotnet.microsoft.com/download) versão 2.1 ou posterior
- Uma conta do Azure. [Inscreva-se gratuitamente](https://azure.microsoft.com/free/ai/).
- Uma conta [do GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O aplicativo cliente que você criará neste tutorial usa um punhado de serviços do Azure. Para reduzir o tempo de ida e volta para respostas do seu bot, você vai querer ter certeza de que esses serviços estão localizados na mesma região do Azure. Nesta seção, você criará um grupo de recursos na região **oeste dos EUA.** Esse grupo de recursos será usado ao criar recursos individuais para o Bot-Framework, o canal Direct Line Speech e o serviço Speech.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Crie um grupo de recursos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Você será solicitado a fornecer algumas informações:
   * Definir **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Digite um nome para o seu **grupo de recursos**. Recomendamos **speechEchoBotTutorial-ResourceGroup**.
   * Da **região** para da dada, selecione **Ocidente dos EUA**.
1. Clique em **Examinar e criar**. Você deve ver um banner que lê **Validação aprovada**.
1. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.
1. Assim como com os recursos que você criará mais tarde neste tutorial, é uma boa ideia fixar esse grupo de recursos no seu painel para fácil acesso. Se você quiser fixar este grupo de recursos, clique no ícone de pino no canto superior direito do painel.

### <a name="choosing-an-azure-region"></a>Escolhendo uma região azure

Se você quiser usar uma região diferente para este tutorial, esses fatores podem limitar suas escolhas:

* Certifique-se de que você usa uma [região Azure suportada](regions.md#voice-assistants).
* O canal Direct Line Speech usa o serviço texto-para-fala, que tem vozes padrão e neurais. As vozes neurais são [limitadas a regiões específicas do Azure.](regions.md#standard-and-neural-voices)
* As chaves de teste gratuitas podem ser restritas a uma região específica.

Para obter mais informações sobre as regiões, consulte [as localizações do Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Criar recursos

Agora que você tem um grupo de recursos em uma região apoiada, o próximo passo é criar recursos individuais para cada serviço que você usará neste tutorial.

### <a name="create-a-speech-service-resource"></a>Crie um recurso de serviço de fala

Siga estas instruções para criar um recurso de fala:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Crie um recurso de serviço de fala<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Você será solicitado a fornecer algumas informações:
   * Dê um **nome**ao seu recurso. Recomendamos **speechEchoBotTutorial-Speech**
   * Para **assinatura,** **certifique-se de** que a avaliação gratuita está selecionada.
   * Para **Localização,** selecione **West US**.
   * Para **o nível de preços,** selecione **F0**. Este é o nível livre.
   * Para **o grupo Resource,** selecione **SpeechEchoBotTutorial-ResourceGroup**.
5. Depois de inseri-lo em todas as informações necessárias, clique **em Criar**. Pode levar alguns minutos para criar seu recurso.
6. Mais tarde, neste tutorial, você precisará de chaves de assinatura para este serviço. Você pode acessar essas chaves a qualquer momento a partir da **visão geral** do seu recurso (gerenciar chaves) ou **chaves**.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tem um recurso de fala:

| Nome | Type  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

### <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

O próximo passo é criar um Plano de Serviço de Aplicativo. Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Crie um plano de serviço de aplicativo do Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Você será solicitado a fornecer algumas informações:
   * Definir **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Para **o grupo Resource,** selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **sistema operacional,** selecione **Windows**.
   * Para **Região,** selecione **West US**.
   * Para **o nível de preços,** **certifique-se de** que o Padrão S1 esteja selecionado. Este deve ser o valor padrão. Se não for, certifique-se de definir o **sistema operacional** para **Windows** como descrito acima.
5. Clique em **Examinar e criar**. Você deve ver um banner que lê **Validação aprovada**.
6. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tem dois recursos:

| Nome | Type  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

## <a name="build-an-echo-bot"></a>Construa um Echo Bot

Agora que você criou alguns recursos, vamos construir um bot. Vamos começar com a amostra do Echo Bot, que como o nome indica, ecoa o texto que você inseriu como resposta. Não se preocupe, o código de amostra está pronto para você usar sem alterações. Ele está configurado para funcionar com o canal Direct Line Speech, que nos conectaremos depois de implantarmos o bot no Azure.

> [!NOTE]
> As instruções a seguir, bem como informações adicionais sobre o Echo Bot estão disponíveis no [README da amostra no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Execute a amostra de bot em sua máquina

1. Clone o repositório de amostras:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Inicie o Visual Studio.
3. Na barra de ferramentas, selecione**Projeto/Solução****Aberta de** >  **Arquivo** > e abra a solução do projeto Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Depois que o projeto for carregado, pressione <kbd>F5</kbd> para construir e executar o projeto.
5. Um navegador deve ser lançado e você verá uma tela semelhante a esta.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot em execução no localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Teste a amostra do bot com o Emulador de Estrutura de Bot

O [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é um aplicativo de desktop que permite aos desenvolvedores de bots testar e depurar seus bots local ou remotamente através de um túnel. O Emulador suporta texto digitado como entrada (não voz). O bot responderá com texto. Siga estas etapas para usar o Emulador de Estrutura bot para testar seu Echo Bot em execução local, com entrada de texto e saída de texto. Depois de implantar o bot no Azure, vamos testá-lo com entrada de voz e saída de voz.

1. Instale o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versão 4.3.0 ou superior
2. Inicie o Emulador de Estrutura de Bot e abra seu bot:
   * **Arquivo** -> **Abrir Bot**.
3. Digite a URL do seu bot. Por exemplo: 

   ```
   http://localhost:3978/api/messages
   ```
   e pressione "Conectar".
4. O bot deve cumprimentá-lo imediatamente com "Olá e bem-vindo!" mensagem. Digite qualquer mensagem de texto e confirme se você recebe uma resposta do bot.
5. Isso é o que uma troca de comunicação com uma instância do Echo Bot pode parecer: [ ![emulador de estrutura de bot](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulador de estrutura de bots")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implante seu bot em um serviço de aplicativos do Azure

O próximo passo é implantar o Echo Bot no Azure. Existem algumas maneiras de implantar um bot, mas neste tutorial vamos focar em publicar diretamente do Visual Studio.

> [!NOTE]
> Alternativamente, você pode implantar um bot usando o [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) e [os modelos de implantação](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. No Visual Studio, abra o Echo Bot configurado para uso com o canal Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. No **Solution Explorer**, clique com o botão direito do mouse no projeto **EchoBot** e **selecione Publicar...**
1. Uma nova janela intitulada **Escolha um alvo de publicação** será aberta.
1. Selecione **O Serviço** de Aplicativos da navegação de **serviços do Azure,** selecione **Criar novo**e clique em **Criar perfil**.
1. Quando a janela **Criar serviço de aplicativo** é exibida:
   * Clique em **Adicionar uma conta**e faça login com as credenciais da sua conta do Azure. Se você já estiver conectado, selecione a conta que deseja na lista suspensa.
   * Para o nome do **aplicativo,** você precisará inserir um nome globalmente único para o seu Bot. Este nome é usado para criar uma URL de bot exclusiva. Um valor padrão será preenchido incluindo a data e a hora (Por exemplo: "EchoBot20190805125647"). Você pode usar o nome padrão para este tutorial.
   * Para **assinatura,** configure-o como **teste gratuito**
   * Para **grupo de**recursos, selecione **SpeechEchoBotTutorial-ResourceGroup**
   * Para **plano de hospedagem,** selecione **SpeechEchoBotTutorial-AppServicePlan**
   * Para **insights de aplicativos,** deixe como **Nenhum**
1. Clique **em Criar**
1. Você deve ver uma mensagem de sucesso no Visual Studio que se parece com isso:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Seu navegador padrão deve abrir e exibir uma página que diz: "Seu bot está pronto!".
1. Neste momento, verifique o **SpeechEchoBotTutorial-ResourceGroup** do grupo de recursos no portal Azure e confirme se há três recursos:

| Nome | Type  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

## <a name="enable-web-sockets"></a>Habilitar soquetes Web

Você precisará fazer uma pequena alteração de configuração para que seu bot possa se comunicar com o canal Direct Line Speech usando soquetes da Web. Siga estas etapas para habilitar soquetes web:

1. Navegue até o [portal Azure](https://portal.azure.com)e localize seu Serviço de Aplicativos. O recurso deve ser nomeado semelhante ao **EchoBot20190805125647** (nome do seu aplicativo exclusivo).
2. Na navegação de **serviços do Azure,** em **Configurações,** clique **em Configuração**.
3. Selecione a guia **Configurações** Gerais.
4. Localize o alternador para **soquetes da Web** e configure-o como **On**.
5. Clique em **Salvar**.

> [!TIP]
> Você pode usar os controles na parte superior da página do Azure App Service para interromper ou reiniciar o serviço. Isso pode ser útil na solução de problemas.

## <a name="create-a-channel-registration"></a>Crie um registro de canal

Agora que você criou um Serviço de Aplicativos Azure para hospedar seu bot, o próximo passo é criar um **Registro de Canais de Bot**. Criar um registro de canal é um pré-requisito para registrar seu bot com canais Do Bot-Framework, incluindo o canal Direct Line Speech.

> [!NOTE]
> Se você quiser saber mais sobre como os bots alavancam canais, consulte [Conectar um bot aos canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Crie um registro de canais do Azure Bot<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Você será solicitado a fornecer algumas informações:
   * Para **a alça do Bot,** insira **SpeechEchoBotTutorial-BotRegistration**.
   * Para **assinatura,** selecione **Avaliação gratuita**.
   * Para **o grupo Resource,** selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Para **Localização,** selecione **West US**.
     * Para **o nível de preços,** selecione **F0**.
     * Para **o ponto final de mensagens,** digite a URL do seu aplicativo web com o `/api/messages` caminho anexado no final. Por exemplo: se o seu nome de aplicativo globalmente único fosse **EchoBot20190805125647**, seu ponto final de mensagens seria: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Para **insights do aplicativo,** você pode definir isso como **Desato**. Para obter mais informações, consulte [análises de bots](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorar **auto criar ID de aplicativo e senha**.
5. Na parte inferior da lâmina de registro de canais do **bot,** clique em **Criar**.

Neste ponto, verifique o **SpeechEchoBotTutorial-ResourceGroup** do grupo de recursos no portal Azure. Agora deve mostrar quatro recursos:

| Nome | Type  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-BotRegistration | Registro de canais de bot | global |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

> [!IMPORTANT]
> O recurso Bot Channels Registration mostrará a região Global mesmo que você tenha selecionado o Oeste dos EUA. Isso é esperado.

## <a name="register-the-direct-line-speech-channel"></a>Cadastre-se no canal Direct Line Speech

Agora é hora de registrar seu bot no canal Direct Line Speech. Este canal é o que é usado para criar uma conexão entre seu echo bot e um aplicativo cliente compilado com o Speech SDK.

1. Localize e abra seu recurso **SpeechEchoBotTutorial-BotRegistration** no [portal Azure](https://portal.azure.com).
1. A partir da navegação **de serviços do Azure,** selecione **Canais**.
   * Procure **por mais canais,** localize e clique **em Direct Line Speech**.
   * Revise o texto na página intitulada **Configure Direct line Speech**e expanda o menu suspenso rotulado como "Conta de serviço cognitivo".
   * Selecione o recurso de fala criado anteriormente (por exemplo, **SpeechEchoBotTutorial-Speech**) do menu para associar seu bot à sua chave de assinatura de fala.
   * Clique em **Salvar**.

1. A partir da navegação **de gerenciamento de Bot,** clique **em Configurações**.
   * Verifique a caixa rotulada **Como Ativar ponto final de streaming**. Isso é necessário para habilitar um protocolo de comunicação construído em soquetes web entre seu bot e o canal Direct Line Speech.
   * Clique em **Salvar**.

> [!TIP]
> Se quiser saber mais, consulte [Conecte um bot ao Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Esta página inclui informações adicionais e problemas conhecidos.

## <a name="build-the-windows-voice-assistant-client"></a>Construa o cliente assistente de voz do Windows

Nesta etapa, você vai construir o Cliente Assistente de Voz do Windows. O cliente é um aplicativo da Windows Presentation Foundation (WPF) em C# que usa o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) para gerenciar a comunicação com seu bot usando o canal Direct Line Speech. Use-o para interagir e testar seu bot antes de escrever um aplicativo cliente personalizado.

O Cliente assistente de voz do Windows tem uma interface do usuário simples que permite configurar a conexão com seu bot, visualizar a conversa de texto, visualizar atividades do Bot-Framework no formato JSON e exibir cartões adaptativos. Ele também suporta o uso de palavras-chave personalizadas. Você usará este cliente para falar com seu bot e receber uma resposta de voz.

Antes de seguirem em frente, certifique-se de que seu microfone e alto-falantes estejam habilitados e funcionando.

1. Navegue até o repositório Do GitHub para o [Cliente Assistente de Voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
2. Siga as instruções fornecidas para clonar o repositório, construir o projeto, configurar o cliente e iniciar o cliente.
3. Clique **em Reconectar** e certifique-se de ver a mensagem **Pressione o botão do microfone ou digite para começar a falar com seu bot**.
4. Vamos testá-lo. Clique no botão do microfone e fale algumas palavras em inglês. O texto reconhecido aparecerá enquanto você fala. Quando terminar de falar, o bot responderá com sua própria voz, dizendo "eco" seguido das palavras reconhecidas.
5. Você também pode usar texto para se comunicar com o bot. Basta digitar o texto na barra inferior. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Solucionando problemas no cliente assistente de voz do Windows

Se você receber uma mensagem de erro na janela principal do aplicativo, use esta tabela para identificar e solucionar problemas:

| Erro | O que você deverá fazer? |
|-------|----------------------|
|Falha de autenticação de erro: a atualização do WebSocket falhou com um erro de autenticação (401). Verifique se há chave de assinatura correta (ou token de autorização) e nome da região| Na página Configurações do aplicativo, certifique-se de que você inseriu a tecla Assinatura de Fala e sua região corretamente.<br>Certifique-se de que sua chave de fala e região-chave foram inseridas corretamente. |
|Falha de conexão de erro: a conexão foi fechada pelo host remoto. Código de erro: 1011. Detalhes de erro: Não conseguimos nos conectar ao bot antes de enviar uma mensagem | Certifique-se de que você verificou a caixa ["Ativar ponto final de streaming"](#register-the-direct-line-speech-channel) e/ou [ **soquetes da Web** alternados](#enable-web-sockets) para ativado.<br>Certifique-se de que o Serviço de Aplicativos Do Azure está sendo executado. Se for, tente reiniciar seu Serviço de Aplicativo.|
|Falha de conexão de erro: a conexão foi fechada pelo host remoto. Código de erro: 1011. Detalhes de erro: O código de status de resposta não indica sucesso: 500 (InternalServerError)| Seu bot especificou uma voz neural em seu campo de saída Activity [Speak,](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) mas a região do Azure associada à sua tecla de assinatura Speech não suporta vozes neurais. Veja [vozes padrão e neurais.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)|
|Falha de conexão de erro: a conexão foi fechada pelo host remoto. Código de erro: 1000. Detalhes de erro: Duração máxima da conexão do soquete da Web (> 300000 ms)| Este é um erro esperado quando uma conexão com o canal é deixada aberta e inativa por mais de cinco minutos. |

Se o seu problema não for abordado na tabela, consulte [assistentes de voz: Perguntas frequentes](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Exibir atividades de bot

Cada bot envia e recebe mensagens **de atividade.** Na janela Registro de **atividades** do Cliente assistente de voz do Windows, você verá logs carimbados com cada atividade que o cliente recebeu do bot. Você também pode ver as atividades que [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) o cliente enviou para o bot usando o método. Quando você selecionar um item de registro, ele mostrará os detalhes da atividade associada como JSON.

Aqui está uma amostra json de uma atividade que o cliente recebeu:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para saber mais sobre o que é devolvido na saída JSON, consulte [campos na Atividade](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Para o propósito deste tutorial, você pode se concentrar nos campos [Texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e [Falar.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Exibir o código-fonte do cliente para chamadas para o Speech SDK

O Cliente Assistente de Voz do Windows usa o pacote NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o Speech SDK. Um bom lugar para começar a revisar o código de amostra [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)é o método InitSpeechConnector() no arquivo, que cria esses dois objetos Speech SDK:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- Para configurações de configuração (por exemplo, chave de assinatura de fala, região-chave)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Gerenciar a conexão de canal e os eventos de assinatura do cliente para lidar com respostas reconhecidas de voz e bot.

## <a name="add-custom-keyword-activation"></a>Adicionar ativação personalizada de palavras-chave

O Speech SDK suporta ativação personalizada de palavras-chave. Semelhante ao "Hey Cortana" para assistente da Microsoft, você pode escrever um aplicativo que ouvirá continuamente uma palavra-chave de sua escolha. Tenha em mente que uma palavra-chave pode ser uma palavra única ou uma frase de várias palavras.

> [!NOTE]
> O termo *palavra-chave* é frequentemente usado de forma intercambiável com o termo *palavra de despertar*, e você pode ver ambos usados na documentação da Microsoft.

A detecção de palavras-chave é feita no aplicativo do cliente. Se usar uma palavra-chave, o áudio só será transmitido para o canal Direct Line Speech se a palavra-chave for detectada. O canal Direct Line Speech inclui um componente chamado *verificação de palavras-chave (KWV),* que faz processamentomais complexo na nuvem para verificar se a palavra-chave escolhida está no início do fluxo de áudio. Se a verificação da palavra-chave for bem sucedida, o canal se comunicará com o bot.

Siga estas etapas para criar um modelo de palavra-chave, configure o Cliente Assistente de Voz do Windows para usar este modelo e, finalmente, teste-o com o seu bot.

1. Siga estas instruções para [criar uma palavra-chave personalizada usando o serviço Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Descompacte o arquivo modelo que você baixou na etapa anterior. Deve ser nomeado para sua palavra-chave. Você está procurando um `kws.table`arquivo chamado.
3. No Cliente Assistente de Voz do Windows, localize o menu **Configurações** (procure o ícone de engrenagem no canto superior direito). Localizar **o caminho do arquivo modelo** `kws.table` e digitar o nome do caminho completo para o arquivo a partir da etapa 2.
4. Certifique-se de verificar a caixa rotulada **ativada**. Você deve ver esta mensagem ao lado da caixa de seleção: "Ouvirei a palavra-chave na próxima conexão". Se você forneceu o arquivo errado ou um caminho inválido, você deve ver uma mensagem de erro.
5. Digite a **tecla de assinatura**de fala , **região chave de assinatura**e clique em **OK** para fechar o menu **Configurações.**
6. Clique **em Reconectar**. Você deve ver uma mensagem que diz: "Nova conversa iniciada - digite, pressione o botão do microfone ou diga a palavra-chave". O aplicativo agora está ouvindo continuamente.
7. Fale qualquer frase que comece com sua palavra-chave. Por exemplo: "**{sua palavra-chave}**, que horas são?". Você não precisa parar depois de pronunciar a palavra-chave. Quando terminar, duas coisas acontecem:
   * Você verá uma transcrição do que você falou
   * Pouco depois, você ouvirá a resposta do bot
8. Continue a experimentar com os três tipos de entrada que seu bot suporta:
   * Texto digitado na barra inferior
   * Pressionando o ícone do microfone e falando
   * Dizendo uma frase que começa com sua palavra-chave

### <a name="view-the-source-code-that-enables-keyword"></a>Veja o código-fonte que habilita a palavra-chave

No código fonte do cliente do Assistente de voz do Windows, dê uma olhada nesses arquivos para revisar o código usado para permitir a detecção de palavras-chave:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)inclui uma chamada para o método [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)Speech SDK , que é usado para instanciar o modelo a partir de um arquivo local em disco.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)inclui uma chamada ao método [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)Speech SDK, que ativa a detecção contínua de palavras-chave.

## <a name="optional-change-the-language-and-bot-voice"></a>(Opcional) Alterar o idioma e a voz do bot

O bot que você criou ouvirá e responderá em inglês, com uma voz padrão de texto para voz americano. No entanto, você não está limitado a usar inglês, ou uma voz padrão. Nesta seção, você aprenderá como alterar o idioma que seu bot ouvirá e responderá. Você também aprenderá a selecionar uma voz diferente para esse idioma.

### <a name="change-the-language"></a>Mude a linguagem

Você pode escolher entre qualquer um dos idiomas mencionados na tabela [de fala para texto.](language-support.md#speech-to-text) No exemplo abaixo, mudaremos a língua para o alemão.

1. Abra o aplicativo Cliente assistente de voz do Windows, clique no `de-de` botão de configurações (ícone de engrenagem superior direito) e digite no campo Idioma (este é o valor local mencionado na tabela [fala-para-texto).](language-support.md#speech-to-text) Isso define a língua falada a `en-us`ser reconhecida, substituindo o padrão . Isso também instrui o canal Direct Line Speech a usar uma voz alemã padrão para a resposta do Bot.
2. Feche a página de configurações e clique no botão Reconectar para estabelecer uma nova conexão com seu echo bot.
3. Clique no botão do microfone e diga uma frase em alemão. Você verá o texto reconhecido e o echo bot respondendo com a voz alemã padrão.

### <a name="change-the-default-bot-voice"></a>Alterar a voz padrão do bot

Selecionar a voz de texto para a voz e controlar a pronúncia pode ser feito se o Bot especificar a resposta na forma de um SSML [(Speech Synthesis Markup Language, linguagem](speech-synthesis-markup.md) de marcação de síntese de voz) em vez de texto simples. O echo bot não usa SSML, mas podemos facilmente modificar o código para fazer isso. No exemplo abaixo adicionamos SSML à resposta do robô eco, de modo que a voz alemã Stefan Apollo (uma voz masculina) será usada em vez da voz feminina padrão. Veja a lista de [Vozes Padrão](language-support.md#standard-voices) e [Vozes Neurais](language-support.md#neural-voices) suportadas para o seu idioma.

1. Vamos começar pela `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`abertura.
2. Localize estas duas linhas:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Substitua-os por:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Construa sua solução no Visual Studio e corrija quaisquer erros de compilação.

O segundo argumento no método 'MessageFactory.Text' define o [campo 'Atividade falar na](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) resposta do bot'. Com a alteração acima, ele foi substituído de texto simples para SSML, a fim de especificar uma voz alemã não padrão.

### <a name="redeploy-your-bot"></a>Reimplante seu bot

Agora que você fez a alteração necessária para o bot, o próximo passo é republicá-lo para o seu Serviço de Aplicativos Azure e experimentá-lo:

1. Na janela Solution Explorer, clique com o botão direito do mouse no projeto **EchoBot** e selecione **Publicar**.
2. Sua configuração de implantação anterior já foi carregada como padrão. Basta clicar **em Publicar** ao lado do **EchoBot20190805125647 - Web Deploy**.
3. A mensagem **Publish Succeeded** aparecerá na janela de saída do Visual Studio, e uma página da Web será lançada com a mensagem "Seu bot está pronto!".
4. Abra o aplicativo Cliente assistente de voz do Windows, clique no botão de `de-de` configurações (ícone de engrenagem superior direito) e certifique-se de que você ainda tem no campo Idioma.
5. Siga as instruções em [Build the Windows Voice Assistant Client](#build-the-windows-voice-assistant-client) para se reconectar com seu bot recém-implantado, falar no novo idioma e ouvir você responder nesse idioma com a nova voz.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não vai continuar usando o echo-bot implantado neste tutorial, você pode removê-lo e todos os seus recursos associados do Azure simplesmente excluindo o grupo de recursos do Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. No [portal Azure,](https://portal.azure.com)clique em **Grupos de recursos** da navegação de **serviços do Azure.**
2. Encontre o grupo de recursos chamado **SpeechEchoBotTutorial-ResourceGroup**. Clique nos três outros (...).
3. Selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Construa seu próprio aplicativo cliente com o Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Confira também

* Implantando em uma [região do Azure perto de você](https://azure.microsoft.com/global-infrastructure/locations/) para ver a melhoria do tempo de resposta do bot
* Implantação em uma [região Azure que suporta vozes Neural TTS de alta qualidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Preços associados ao canal Direct Line Speech:
  * [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Construindo e implantando seu próprio bot habilitado para voz:
  * Construa um [bot-framework .](https://dev.botframework.com/) Registre-o no [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e personalize seu bot para [voz](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Explorar [as soluções bot-framework](https://microsoft.github.io/botframework-solutions/index)existentes : Construa um [assistente virtual](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) e [amplie-o ao Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
