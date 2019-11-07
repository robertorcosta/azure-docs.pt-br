---
title: 'Início Rápido: Sintetizar fala, Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em Python usando o SDK de Fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 36e48a5d513daa951c1e92017ba9a2322b009703
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505448"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Crie um recurso do Azure Speech](../../../../get-started.md)
> * [Configure o ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Crie um projeto de amostra vazio](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

ou é possível baixar este tutorial de início rápido como um [Jupyter](https://jupyter.org) Notebook do [repositório de exemplos do SDK de Fala](https://aka.ms/csspeech/samples) e executá-lo como um notebook.

### <a name="sample-code"></a>Exemplo de código

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de Fala com o Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3.5 ou posterior, em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Pesquise **Python**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é fazer isso usando o Windows Explorer.
1. No Visual Studio Code, selecione o ícone **Arquivo**. Em seguida, abra a pasta criada.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, clicando no ícone de novo arquivo.

   ![Criar um arquivo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e salve o [código do Python](#sample-code) para o arquivo recém-criado.
1. Insira as informações de assinatura dos Serviços de Fala.
1. Se já tiver sido selecionado, um interpretador do Python será exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, exiba uma lista de interpretadores do Python disponíveis. Abra a paleta de comandos (Ctrl+Shift+P) e insira **Python: selecionar interpretador**. Escolha um adequado.
1. É possível instalar o pacote Python do SDK de Fala de dentro do Visual Studio Code. Faça isso se ele ainda não foi instalado para o interpretador do Python selecionado.
   Para instalar o pacote do SDK de Fala, abra um terminal. Exiba a paleta de comandos novamente (Ctrl+Shift+P) e insira **Terminal: Criar um Novo Terminal Integrado**.
   No terminal aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para o seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no Terminal**.
   Digite algum texto quando você for solicitado. O áudio sintetizado é reproduzido logo em seguida.

   ![Executar um exemplo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Se houver problemas em seguir estas instruções, consulte o [tutorial do Python do Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) mais amplo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
