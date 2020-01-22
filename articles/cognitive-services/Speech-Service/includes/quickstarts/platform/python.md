---
title: 'Início Rápido: Configuração de plataforma do SDK de Fala para Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar Python com o SDK do serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 218d0dca43d126c1318c273603a4980697c465af
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751820"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

- O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
  - Windows: x64 e x86
  - Mac: macOS X versão 10.12 ou mais recente
  - Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 em x64

## <a name="prerequisites"></a>Prerequisites

- As plataformas Linux com suporte deverão ter determinadas bibliotecas instaladas (`libssl` para suporte do protocolo SSL e `libasound2` para um suporte consistente). Consulte a distribuição abaixo para verificar os comandos necessários para instalar as versões corretas dessas bibliotecas.

  - No Ubuntu, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Observe que, ao instalá-los pela primeira vez, pode ser necessário reiniciar o Windows antes de continuar com este guia.
- E, por fim, você precisará do [Python 3.5, 3.6 ou 3.7](https://www.python.org/downloads/). Para verificar a instalação, abra um prompt de comando, digite o comando `python --version` e verifique o resultado. Se estiver instalado corretamente, você receberá uma resposta "Python 3.5.1" ou semelhante.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalar o SDK de Fala usando o Visual Studio Code

1. Baixe e instale a versão mais recente do [Python](https://www.python.org/downloads/) com suporte à sua plataforma, (3.5 ou superior).
   - Os usuários do Windows precisam selecionar "Adicionar Python ao PATH" durante o processo de instalação.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Procure **Python**  e clique em **Instalar**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Além disso, é possível instalar o pacote Python do SDK de Fala de dentro do Visual Studio Code usando a linha de comando integrada:
   1. Abra um terminal (nos menus suspensos, **Terminal** > **Novo Terminal**)
   1. No terminal que abre, insira o comando `python -m pip install azure-cognitiveservices-speech`

Pronto. Você já pode começar a codificar o SDK de Fala no Python e avançar para as [Próximas etapas](#next-steps) abaixo. Se você é novo no Visual Studio Code, consulte a [Documentação mais completa do Visual Studio Code](https://code.visualstudio.com/docs). Para obter mais informações sobre o Visual Studio Code e Python, consulte o [Tutorial do Visual Studio Code para Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Instalar o SDK de Fala usando a linha de comando

Se você não estiver usando o Visual Studio Code, o comando a seguir instalará o pacote do Python de [PyPI](https://pypi.org/) para o SDK de Fala. Usuários do Visual Studio Code podem ir diretamente para a próxima subseção.

```sh
pip install azure-cognitiveservices-speech
```

Se estiver no macOS, talvez seja necessário executar o seguinte comando para fazer o comando `pip` acima funcionar:

```sh
python3 -m pip install --upgrade pip
```

Depois de usar o `pip` com êxito para instalar o `azure-cognitiveservices-speech`, você pode usar o SDK de Fala importando o namespace nos seus projetos do Python. Por exemplo:

```py
import azure.cognitiveservices.speech as speechsdk
```

Isso é mostrado em mais detalhes nos exemplos de código listados nas [Próximas etapas](#next-steps) abaixo.

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Python do SDK de Fala são distribuídas por meio do PyPI e anunciadas no [Notas sobre a versão](~/articles/cognitive-services/speech-service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se estiver faltando um recurso, confira [Support and help options](~/articles/cognitive-services/speech-service/support.md) (Opções de Ajuda e Suporte).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
