---
title: O que é Serviço de fala?
titleSuffix: Azure Cognitive Services
description: O serviço de Fala é a unificação da conversão de fala em texto, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. Adicione serviços de fala a seus aplicativos, ferramentas e dispositivos usando o SDK de Fala, o SDK de Dispositivos de Fala ou APIs REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401032"
---
# <a name="what-is-the-speech-service"></a>O que é Serviço de fala?

O serviço de Fala é a unificação da conversão de fala em texto, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. É fácil habilitar serviços de fala em seus aplicativos, ferramentas e dispositivos usando o [SDK de Fala](speech-sdk-reference.md), o [SDK de Dispositivos de Fala](https://aka.ms/sdsdk-quickstart) ou [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O serviço de Fala substituiu a API de Fala do Bing e a Tradução de Fala. Confira _Guias de instruções > Migração_ para obter instruções de migração.

Esses recursos compõem o serviço de Fala. Use os links nesta tabela para saber mais sobre os casos de uso comuns de cada recurso ou navegue pela referência de API.

| Serviço | Recurso | Descrição | . | REST |
|---------|---------|-------------|-----|------|
| [Conversão de fala em texto](speech-to-text.md) | Conversão de fala em texto em tempo real | A conversão de fala em texto transcreve ou converte, em tempo real, transmissões de áudio ou arquivos locais em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Conversão de fala em texto em lote](batch-transcription.md) | A conversão de fala em texto em lote permite a transcrição de fala em texto assíncrona de grandes volumes de dados de áudio de fala armazenados no Armazenamento de Blobs do Azure. Além de converter áudio de fala em texto, a conversão de fala em texto em lote também permite a análise de sentimentos e a diarização. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Conversa em vários dispositivos](multi-device-conversation.md) | Conecte vários dispositivos ou clientes em uma conversa para enviar mensagens baseadas em fala ou texto, com suporte fácil para transcrição e tradução| Sim | Não |
| | [Transcrição de Conversas](conversation-transcription-service.md) | Permite o reconhecimento de fala em tempo real, a identificação do locutor e a diarização. É perfeito para transcrição de reuniões presenciais com a capacidade de distinguir os locutores. | Sim | Não |
| | [Criar Modelos de Fala Personalizada](#customize-your-speech-experience) | Se estiver usando a conversão de fala em texto para funcionalidades de reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de idioma e de pronúncia personalizados para lidar com o ruído ambiente ou vocabulário específico do setor. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Conversão de texto em fala](text-to-speech.md) | Conversão de texto em fala | A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana usando [Linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Escolha entre as vozes padrão e vozes neurais (consulte [Suporte para idiomas](language-support.md)). | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar Vozes Personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas exclusivas para sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Fala](speech-translation.md) | Tradução de fala | A tradução de fala possibilita a tradução em tempo real e em vários idiomas de fala para seus aplicativos, suas ferramentas e seus dispositivos. Use esse serviço para tradução de fala em fala e fala em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Assistentes de voz que usam o serviço de Fala capacitam os desenvolvedores a criarem interfaces de conversação natural, semelhante à humana, para seus aplicativos e suas experiências. O serviço de assistente de voz fornece interação rápida e confiável entre um dispositivo e uma implementação de assistente que usa o Canal de Fala de Linha Direta do Bot Framework ou o serviço integrado de Comandos Personalizados (versão prévia) para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Experimentar o serviço de Fala

Nós oferecemos guias de início rápido nas linguagens de programação mais populares, todos eles desenvolvidos para que você executar o código em menos de 10 minutos. Esta tabela contém os guias de início rápido mais populares de cada recurso. Use o painel de navegação esquerdo para explorar plataformas e linguagens adicionais.

| Conversão de fala em texto (SDK) | Conversão de texto em fala (SDK) | Tradução (SDK) |
|----------------------|----------------------|-------------------|
| [Reconhecer uma fala de um arquivo de áudio](quickstarts/speech-to-text-from-file.md) | [Sintetizar fala em um arquivo de áudio](quickstarts/text-to-speech-audio-file.md) | [Converter fala em texto](quickstarts/translate-speech-to-text.md) |
| [Reconhecer fala com um microfone](quickstarts/speech-to-text-from-microphone.md) | [Sintetizar fala para um locutor](quickstarts/text-to-speech.md) | [Traduzir fala para vários idiomas de destino](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconhecer uma fala armazenada no Armazenamento de Blobs](quickstarts/from-blob.md) | [Sintetização assíncrona para áudio de fala contínua](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traduzir fala em fala](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> A conversão de fala em texto e de texto em fala também tem pontos de extremidade REST e inícios rápidos associados.

Depois que tiver a oportunidade de usar o serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

- [Tutorial: Reconhecer intenções de fala com o SDK de Fala e o LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Habilitar por voz o bot com o SDK de Fala, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Criar um aplicativo Flask para traduzir texto, analisar o sentimento e sintetizar a conversão de texto traduzido em fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter código de exemplo

Há um código de exemplo disponível no GitHub para o serviço de Fala. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

- [Amostras de conversão de fala em texto, conversão de texto em fala e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalize sua experiência de fala

O serviço de Fala funciona bem com modelos internos, no entanto, pode ser interessante personalizar e ajustar ainda mais a experiência para seu ambiente ou produto. As opções de personalização vão do ajuste do modelo acústico a fontes de voz exclusivas para sua marca.

| Serviço de Fala | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Conversão de fala em texto | [Fala Personalizada](https://aka.ms/customspeech) | Personalize modelos de reconhecimento de fala de acordo com suas necessidades e com os dados disponíveis. Supere as barreiras do reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo. |
| Conversão de texto em fala | [Voz personalizada](https://aka.ms/customvoice) | Crie uma voz reconhecível e exclusiva para seus aplicativos de Conversão de texto em fala usando seus dados de fala disponíveis. Você pode ajustar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="reference-docs"></a>Documentos de Referência

- [SDK da fala](speech-sdk-reference.md)
- [SDK de Dispositivos de Fala](speech-devices-sdk.md)
- [API REST: conversão de fala em texto](rest-speech-to-text.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)
- [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
