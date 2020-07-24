---
title: Criar palavras-chave personalizadas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.openlocfilehash: cef47d1c6598e73d2c17c6799f3de90458206a8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095748"
---
# <a name="custom-keyword-basics"></a>Noções básicas de palavras-chave personalizadas

Neste artigo, você aprende as noções básicas de como trabalhar com palavras-chave personalizadas usando o Speech Studio e o SDK de fala. Uma palavra-chave é uma palavra ou frase curta que permite que seu produto seja ativado por voz. Você cria modelos de palavra-chave no Speech Studio e, em seguida, exporta um arquivo de modelo que você usa com o SDK de fala em seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

As etapas neste artigo exigem uma assinatura de fala e o SDK de fala. Se você ainda não tiver uma assinatura, [Experimente o serviço de fala gratuitamente](get-started.md). Para obter o SDK, consulte o [Guia de instalação](quickstarts/setup-platform.md) da sua plataforma.

## <a name="create-a-keyword-in-speech-studio"></a>Criar uma palavra-chave no Speech Studio

Antes de usar uma palavra-chave personalizada, você precisa criar uma palavra-chave usando a página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um `.table` arquivo que você pode usar com o SDK de fala.

> [!IMPORTANT]
> Os modelos de palavra-chave personalizados e os arquivos resultantes `.table` podem ser criados **somente** no Speech Studio.
> Você não pode criar palavras-chave personalizadas do SDK ou com chamadas REST.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , crie um **novo projeto**. 

1. Insira um **nome**, uma **Descrição**opcional, e selecione o idioma. Você precisa de um projeto por idioma e o suporte está atualmente limitado ao `en-US` idioma.

    ![Descrever seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o projeto na lista. 

    ![Selecione seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para criar um novo modelo de palavra-chave, clique em **treinar modelo**.

1. Insira um **nome** para o modelo, uma **Descrição**opcional e a **palavra-chave** de sua escolha e clique em **Avançar**. Consulte as [diretrizes](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) sobre como escolher uma palavra-chave efetiva.

    ![Insira sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. O portal cria pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, clique em **treinar** para começar a gerar o modelo de palavra-chave. 

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até trinta minutos para que o modelo seja gerado. A lista de palavras-chave mudará de **processamento** para com **êxito** quando o modelo for concluído. Em seguida, você pode baixar o arquivo.

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. O arquivo baixado é um `.zip` arquivo morto. Extraia o arquivo morto e você verá um arquivo com a `.table` extensão. Esse é o arquivo que você usa com o SDK na próxima seção, portanto, certifique-se de anotar seu caminho. o nome do arquivo espelha seu nome de palavra-chave, por exemplo, uma palavra-chave de **ativação de dispositivo** tem o nome do arquivo `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Usar um modelo de palavra-chave com o SDK

Primeiro, carregue o arquivo de modelo de palavra-chave usando a `FromFile()` função estática, que retorna um `KeywordRecognitionModel` . Use o caminho para o `.table` arquivo que você baixou do Speech Studio. Além disso, você cria um `AudioConfig` usando o microfone padrão e, em seguida, instancia um novo `KeywordRecognizer` usando a configuração de áudio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Em seguida, a execução de reconhecimento de palavra-chave é feita com uma chamada para `RecognizeOnceAsync()` passando o objeto de modelo. Isso inicia uma sessão de reconhecimento de palavra-chave que dura até que a palavra-chave seja reconhecida. Portanto, você geralmente usa esse padrão de design em aplicativos multi-threaded, ou em casos de uso em que você pode estar aguardando uma palavra de ativação indefinidamente.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> O exemplo mostrado aqui usa o reconhecimento de palavra-chave local, pois não requer um `SpeechConfig` objeto para o contexto de autenticação e não entra em contato com o back-end. No entanto, você pode executar o reconhecimento de palavra-chave e [a verificação utilizando uma conexão de back-end contínua](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Próximas etapas

Teste sua palavra-chave personalizada com o guia de [início rápido do SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
