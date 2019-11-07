---
title: 'Início Rápido: Sintetizar fala, C# (Windows) - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode sintetizar fala do texto e ouvir a fala no seu alto-falante em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: af101862e824d6527c08c232812913565536bb4a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505152"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Crie um recurso do Azure Speech](../../../../get-started.md)
> * [Configure o ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Crie um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e a substitua pela chave de assinatura dos Serviços de Fala.

1. Encontre a cadeia de caracteres `YourServiceRegion` e a substitua pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, se você está usando a assinatura de avaliação gratuita, a região é `westus`.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Insira uma frase ou sentença em inglês. O aplicativo transmite o texto para os Serviços de Fala, que enviam a fala sintetizada para o aplicativo para reprodução em seu alto-falante.

   ![Interface do usuário de síntese de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
