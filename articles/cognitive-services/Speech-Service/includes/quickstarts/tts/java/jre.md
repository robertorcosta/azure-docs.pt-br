---
title: 'Início Rápido: Sintetizar fala, Java (Windows, Linux, macOS) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você aprenderá a criar um aplicativo Java simples que captura e sintetiza a fala com base em texto e a reproduz por meio do alto-falante padrão.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: d7ca86092b973d369d37cbeac1bc9f8cfcf9b462
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818434"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/text-to-speech/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.
Insira um texto quando solicitado e você ouvirá o áudio sintetizado reproduzido pelo alto-falante padrão.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
