---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 885dc178c6c0bfed5c49b8d9e88792a1072b0f10
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400591"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem **helloworld.cpp**.

1. Substitua todo o código pelo seguinte snippet de código:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pelo **Identificador de região** da [região](https://aka.ms/speech/sdkregion) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Substitua a cadeia de caracteres `whatstheweatherlike.wav` pelo nome do seu arquivo.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, selecione **Compilar** > **Compilar solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar depuração** (ou pressione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. O arquivo de áudio é transmitido para o serviço de Fala e o primeiro enunciado no arquivo é convertido em texto, que aparece na mesma janela.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]