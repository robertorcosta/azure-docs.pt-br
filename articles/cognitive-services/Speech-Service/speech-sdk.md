---
title: Sobre o Speech SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Kit de desenvolvimento de software Speech (SDK) expõe muitos dos recursos de serviço de fala, facilitando o desenvolvimento de aplicativos habilitados para fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f663c9af8c352d0170c633fe76b3fbc5268aad1e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400000"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de Fala

O Kit de desenvolvimento de software Speech (SDK) expõe muitos dos recursos de serviço de fala, para capacitá-lo a desenvolver aplicativos habilitados para fala. O Speech SDK está disponível em muitas linguagens de programação e em todas as plataformas.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Recursos de cenário

O Speech SDK expõe muitos recursos do serviço Speech, mas não todos eles. As capacidades do Speech SDK são frequentemente associadas a cenários. O Speech SDK é ideal para cenários em tempo real e não em tempo real, usando dispositivos locais, arquivos, armazenamento de blob do Azure e até mesmo fluxos de entrada e saída. Quando um cenário não for alcançável com o Speech SDK, procure uma alternativa de API REST.

### <a name="speech-to-text"></a>Conversão de fala em texto

[O speech-to-text](speech-to-text.md) (também conhecido como reconhecimento de *fala)* transcreve fluxos de áudio para textos que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](../luis/index.yml) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. Use [a Tradução de Fala](speech-translation.md) para traduzir a entrada de fala para um idioma diferente com uma única chamada. Para obter mais informações, consulte [os fundamentos de fala para texto](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Conversão de texto em fala

[Texto-para-fala](text-to-speech.md) (também conhecido como *síntese da fala)* converte texto em discurso sintetizado como humano. O texto de entrada é literalde string ou usando o [SSML (Speech Synthesis Markup Language, linguagem](speech-synthesis-markup.md)de marcação de síntese de fala). Para obter mais informações sobre vozes padrão ou neurais, consulte [linguagem text-to-speech e suporte de voz](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Assistentes de voz

Assistentes de voz que usam o Speech SDK permitem que os desenvolvedores criem interfaces de conversação naturais e humanas para seus aplicativos e experiências. O serviço de assistente de voz oferece interação rápida e confiável entre um dispositivo e um assistente. A implementação usa o canal Direct Line Speech do Bot Framework ou o serviço integrado De comandos personalizados (Preview) para conclusão da tarefa. Além disso, assistentes de voz podem ser criados usando o [Portal de Voz Personalizado](https://aka.ms/customvoice) para criar uma experiência de voz única.

#### <a name="keyword-spotting"></a>Localização de palavras-chave

O conceito de localização de [palavras-chave](speech-devices-sdk-create-kws.md) é suportado no Speech SDK. A palavra-chave é o ato de identificar uma palavra-chave na fala, seguida de uma ação ao ouvir a palavra-chave. Por exemplo, "Hey Cortana" ativaria a assistente Cortana.

### <a name="meeting-scenarios"></a>Cenários de reunião

O Speech SDK é perfeito para transcrever cenários de reunião, seja a partir de um único dispositivo ou conversa multi-dispositivo.

#### <a name="conversation-transcription"></a>Transcrição de conversa

[A transcrição da conversa](conversation-transcription.md) permite o reconhecimento de fala em tempo real (e assíncrono), a identificação do orador e a atribuição de sentenças a cada orador (também conhecido como *diarização).* É perfeito para transcrição de reuniões presenciais com a capacidade de distinguir os locutores.

#### <a name="multi-device-conversation"></a>Conversa em vários dispositivos

Com [conversação multidispositivos,](multi-device-conversation.md)conecte vários dispositivos ou clientes em uma conversa para enviar mensagens baseadas em voz ou texto, com suporte fácil para transcrição e tradução.

### <a name="custom--agent-scenarios"></a>Cenários personalizados/agentes

O Speech SDK pode ser usado para transcrever cenários de call center, onde dados de telefonia são gerados.

#### <a name="call-center-transcription"></a>Transcrição de call center

[A transcrição do call center](call-center-transcription.md) é um cenário comum para o discurso-texto para transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como o Interactive Voice Response (IVR). Os modelos mais recentes de reconhecimento de fala do serviço de fala se destacam em transcrever esses dados de telefonia, mesmo nos casos em que os dados são difíceis de entender por um humano.

### <a name="codec-compressed-audio-input"></a>Entrada de áudio compactada codec

Várias das linguagens de programação do Speech SDK suportam fluxos de entrada de áudio compactados codec. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">usar <span class="docon docon-navigate-external x-hidden-focus"> </span>formatos de entrada de áudio compactados </a>.

## <a name="rest-api"></a>API REST

Enquanto o Speech SDK cobre muitos recursos de recursos do Serviço de Fala, para alguns cenários você pode querer usar a API REST.

### <a name="batch-transcription"></a>Transcrição de lote

[A transcrição em lote](batch-transcription.md) permite a transcrição assíncrona de fala para texto de grandes volumes de dados. A transcrição em lote só é possível a partir da API REST. Além de converter o áudio da fala em texto, o grupo de fala em texto também permite a diarização e a análise de sentimentos.

## <a name="customization"></a>Personalização

O Serviço de Fala oferece uma grande funcionalidade com seus modelos padrão entre o discurso-para-texto, texto-para-fala e tradução de fala. Às vezes, você pode querer aumentar o desempenho da linha de base para funcionar ainda melhor com o seu caso de uso exclusivo. O Serviço de Fala tem uma variedade de ferramentas de personalização sem código que facilitam e permitem que você crie uma vantagem competitiva com modelos personalizados com base em seus próprios dados. Esses modelos só estarão disponíveis para você e sua organização.

### <a name="custom-speech-to-text"></a>Discurso personalizado para texto

Ao usar o discurso-para-texto para reconhecimento e transcrição em um ambiente único, você pode criar e treinar modelos personalizados de acústica, linguagem e pronúncia para abordar o ruído ambiente ou o vocabulário específico da indústria. A criação e o gerenciamento de modelos de fala personalizada sem código estão disponíveis através do [Portal de Fala Personalizada](https://aka.ms/customspeech). Uma vez que o modelo de Fala Personalizada é publicado, ele pode ser consumido pelo Speech SDK.

### <a name="custom-text-to-speech"></a>Texto-para-fala personalizado

O text-to-speech personalizado, também conhecido como Custom Voice é um conjunto de ferramentas on-line que permitem criar uma voz reconhecível e única para sua marca. A criação e o gerenciamento de modelos de voz personalizada sem código estão disponíveis através do [Portal de Voz Personalizado](https://aka.ms/customvoice). Uma vez que o modelo de Voz Personalizada é publicado, ele pode ser consumido pelo Speech SDK.

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
