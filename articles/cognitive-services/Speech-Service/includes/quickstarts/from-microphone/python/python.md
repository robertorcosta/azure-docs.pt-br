---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838950"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de Fala do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código-fonte

Crie um arquivo chamado *quickstart.py* e cole o código Python a seguir nele.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Criar e executar um aplicativo

Agora você está pronto para testar o reconhecimento de fala usando o serviço de Fala. 

Se você estiver executando isso no macOS e ele for o primeiro aplicativo Python criado que usa um microfone, provavelmente, você precisará fornecer acesso de terminal ao microfone. Abra **Configurações do Sistema** e selecione **Segurança e Privacidade**. Em seguida, selecione **Privacidade** e localize **Microfone** na lista. Por fim, selecione **Terminal** e salve a seleção. 

1. **Iniciar seu aplicativo** – Na linha de comando, digite:
    ```bash
    python quickstart.py
    ```
2. **Iniciar o reconhecimento** – ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao Serviço de Fala, transcrita como texto e renderizada no console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
