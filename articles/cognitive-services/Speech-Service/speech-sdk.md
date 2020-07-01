---
title: Sobre o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O SDK (Software Development Kit de fala) expõe muitos dos recursos do serviço de fala, facilitando o desenvolvimento de aplicativos habilitados para fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 5dc0a7fc9797948e834b8b3cb802bb92fce0eb59
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610754"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de Fala

O SDK (Software Development Kit de fala) expõe muitos dos recursos do serviço de fala, para capacitar o desenvolvimento de aplicativos habilitados para fala. O SDK de fala está disponível em muitas linguagens de programação e em todas as plataformas.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Recursos de cenário

O SDK de fala expõe muitos recursos do serviço de fala, mas não todos eles. Os recursos do SDK de fala geralmente são associados a cenários. O SDK de fala é ideal para cenários em tempo real e não em tempo real, usando dispositivos locais, arquivos, armazenamento de BLOBs do Azure e até mesmo fluxos de entrada e saída. Quando um cenário não é atingível com o SDK de fala, procure uma alternativa de API REST.

### <a name="speech-to-text"></a>Conversão de fala em texto

A [conversão de fala em texto](speech-to-text.md) (também conhecida como reconhecimento de *fala*) transcreve fluxos de áudio para o texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](../luis/index.yml) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. Use a [tradução de fala](speech-translation.md) para traduzir a entrada de fala para um idioma diferente com uma única chamada. Para obter mais informações, consulte [noções básicas de conversão de fala em texto](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Conversão de texto em fala

Conversão de [texto em fala](text-to-speech.md) (também conhecida como *síntese de fala*) converte o texto em fala sintetizada semelhante à humana. O texto de entrada é um literal de cadeia de caracteres ou o uso da [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Para obter mais informações sobre vozes padrão ou neural, consulte [linguagem de conversão de texto em fala e suporte de voz](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Assistentes de voz

Os [assistentes de voz](voice-assistants.md) que usam o SDK de fala permitem que os desenvolvedores criem interfaces de conversação naturais e humanas para seus aplicativos e experiências. O serviço assistente de voz fornece uma interação rápida e confiável entre um dispositivo e um assistente. A implementação usa o canal de fala de linha direta da estrutura de bot ou o serviço de comandos personalizados integrados (versão prévia) para a conclusão da tarefa. Além disso, os assistentes de voz podem usar vozes personalizadas criadas no [portal de voz personalizado](https://aka.ms/customvoice) para adicionar uma experiência de saída de voz exclusiva.

#### <a name="keyword-spotting"></a>Palavra-chave parando

O conceito de decepções de [palavra-chave](speech-devices-sdk-create-kws.md) tem suporte no SDK de fala. A palavra-chave que está sendo demarcada é o ato de identificar uma palavra-chave em fala, seguida de uma ação após ouvir a palavra-chave. Por exemplo, "Ei Cortana" ativaria o assistente da Cortana.

### <a name="meeting-scenarios"></a>Cenários de reunião

O SDK de fala é perfeito para transcrever cenários de reunião, seja de um único dispositivo ou de uma conversa de vários dispositivos.

#### <a name="conversation-transcription"></a>Transcrição de conversa

A [transcrição de conversa](conversation-transcription.md) habilita o reconhecimento de fala em tempo real (e assíncrono), a identificação do orador e a atribuição de frase para cada palestrante (também conhecido como *diarization*). É perfeito para transcrição de reuniões presenciais com a capacidade de distinguir os locutores.

#### <a name="multi-device-conversation"></a>Conversa em vários dispositivos

Com a [conversa com vários dispositivos](multi-device-conversation.md), conecte vários dispositivos ou clientes em uma conversa para enviar mensagens baseadas em fala ou em texto, com suporte fácil para transcrição e tradução.

### <a name="custom--agent-scenarios"></a>Cenários de agente/personalizado

O SDK de fala pode ser usado para transcrever cenários de Call Center, onde os dados de telefonia são gerados.

#### <a name="call-center-transcription"></a>Transcrição de call center

A [transcrição do Call Center](call-center-transcription.md) é um cenário comum de conversão de fala em texto para transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como o IVR (resposta interativa de voz). Os modelos de reconhecimento de fala mais recentes do serviço de fala do Excel na transcreveção desses dados de telefonia, mesmo em casos em que os dados são difíceis de entender.

### <a name="codec-compressed-audio-input"></a>Entrada de áudio compactada por codec

Várias das linguagens de programação SDK de fala dão suporte a fluxos de entrada de áudio compactados por codec. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">usar formatos <span class="docon docon-navigate-external x-hidden-focus"></span> de entrada de áudio compactados </a>.

## <a name="rest-api"></a>API REST

Embora o SDK de fala cubra muitos recursos de recurso do serviço de fala, em alguns cenários, talvez você queira usar a API REST.

### <a name="batch-transcription"></a>Transcrição de lote

A [transcrição em lote](batch-transcription.md) permite a transcrição de fala em texto assíncrona de grandes volumes de dados. A transcrição do lote só é possível da API REST. Além de converter áudio de fala em texto, a conversão de texto em lote também permite a análise de diarization e de sentimentos.

## <a name="customization"></a>Personalização

O serviço de fala oferece excelente funcionalidade com seus modelos padrão entre conversão de fala em texto, texto em fala e tradução de fala. Às vezes, talvez você queira aumentar o desempenho da linha de base para funcionar ainda melhor com seu caso de uso exclusivo. O serviço de fala tem uma variedade de ferramentas de personalização sem código que facilitam e permitem que você crie uma vantagem competitiva com modelos personalizados com base em seus próprios dados. Esses modelos só estarão disponíveis para você e para sua organização.

### <a name="custom-speech-to-text"></a>Fala Personalizada para texto

Ao usar a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você pode criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados para resolver o ruído de ambiente ou vocabulário específico do setor. A criação e o gerenciamento de modelos de Fala Personalizada sem código estão disponíveis por meio do [portal de fala personalizada](https://aka.ms/customspeech). Depois que o modelo de Fala Personalizada for publicado, ele poderá ser consumido pelo SDK de fala.

### <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

Conversão de texto em fala personalizada, também conhecida como voz personalizada, é um conjunto de ferramentas online que permitem que você crie uma voz de um tipo reconhecível para sua marca. A criação e o gerenciamento de modelos de voz personalizados sem código estão disponíveis por meio do [portal de voz personalizado](https://aka.ms/customvoice). Depois que o modelo de voz personalizado for publicado, ele poderá ser consumido pelo SDK de fala.

## <a name="get-the-speech-sdk"></a>Obter o SDK de Fala

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Navegador](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
