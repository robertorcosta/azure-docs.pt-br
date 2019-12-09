---
title: 'Início Rápido: Sintetizar fala, C# (Windows) – Serviço de Fala'
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
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818328"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e a substitua pela chave de assinatura do serviço de Fala.

1. Encontre a cadeia de caracteres `YourServiceRegion` e a substitua pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, se você está usando a assinatura de avaliação gratuita, a região é `westus`.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Insira uma frase ou sentença em inglês. O aplicativo transmite o texto para o serviço de Fala, que enviam a fala sintetizada para o aplicativo para reprodução em seu alto-falante.

   ![Interface do usuário de síntese de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
