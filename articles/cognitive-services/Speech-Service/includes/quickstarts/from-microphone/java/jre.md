---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: eb131f3f53e04809ca21efda7367f03a79081bbc
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446864"
---
## <a name="prerequisites"></a>Prerequisites

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=jre)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pelo **Identificador de região** da [região](https://aka.ms/speech/sdkregion) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.
Os próximos 15 segundos de entrada de fala do microfone serão reconhecidos e registrados na janela do console.

![Captura de tela da saída do console após o reconhecimento com êxito](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
