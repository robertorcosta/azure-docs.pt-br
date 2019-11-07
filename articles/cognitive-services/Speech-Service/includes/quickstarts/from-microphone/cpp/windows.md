---
title: 'Início Rápido: Reconhecer a fala de um microfone, C++ (Windows) - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C++ no Windows Desktop usando o SDK de Fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 1a08a5413ae41ef8c17e817a01be9e9efcb7ff99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505712"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Crie um recurso do Azure Speech](../../../../get-started.md)
> * [Configure o ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Crie um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem **helloworld.cpp**.

1. Substitua todo o código pelo seguinte snippet de código:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, selecione **Compilar** > **Compilar solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar depuração** (ou pressione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Fale uma frase ou expressão em inglês. O aplicativo transmite sua fala para os Serviços de Fala, que transcrevem o conteúdo para texto e enviam de volta ao aplicativo para exibição.

   ![Saída do console após o reconhecimento com êxito](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]