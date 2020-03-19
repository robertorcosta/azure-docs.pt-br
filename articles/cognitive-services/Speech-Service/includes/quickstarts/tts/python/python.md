---
title: 'Início Rápido: Sintetizar fala, Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em Python usando o SDK de Fala
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 19/14/2020
ms.author: dapine
ms.openlocfilehash: 58f2b3693b924197dda1e396ae71192776ca276e
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925068"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Python do SDK de Fala são distribuídas por meio do PyPI e anunciadas no [Notas sobre a versão](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se estiver faltando um recurso, confira [Support and help options](~/articles/cognitive-services/Speech-Service/support.md) (Opções de Ajuda e Suporte).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar um aplicativo Python que usa o SDK de Fala

### <a name="run-the-sample"></a>Execute o exemplo

É possível copiar o [código de exemplo](#sample-code) neste início rápido para um arquivo de origem `quickstart.py` e executá-lo no IDE ou no console:

```sh
python quickstart.py
```

ou é possível baixar este tutorial de início rápido como um [Jupyter](https://jupyter.org) Notebook do [repositório de exemplos do SDK de Fala](https://aka.ms/csspeech/samples) e executá-lo como um notebook.

### <a name="sample-code"></a>Código de exemplo

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de Fala com o Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3.5 a 3.8, em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Pesquise **Python**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é fazer isso usando o Windows Explorer.
1. No Visual Studio Code, selecione o ícone **Arquivo**. Em seguida, abra a pasta criada.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, clicando no ícone de novo arquivo.

   ![Criar um arquivo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e salve o [código do Python](#sample-code) para o arquivo recém-criado.
1. Insira as informações de assinatura do Serviço de Fala.
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

## <a name="see-also"></a>Confira também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
