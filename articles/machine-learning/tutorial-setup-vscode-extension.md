---
title: 'Tutorial: Configurar a extensão do Azure Machine Learning para Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Saiba como configurar a extensão do Azure Machine Learning para Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157457"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Configurar a extensão do Azure Machine Learning para Visual Studio Code

Saiba como instalar e executar scripts usando a extensão do Azure Machine Learning para Visual Studio Code.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão do Azure Machine Learning para Visual Studio Code
> * Entrar em sua conta do Azure por meio do Visual Studio Code
> * Usar a extensão do Azure Machine Learning para executar um script de exemplo

## <a name="prerequisites"></a>Prerequisites

- Assinatura do Azure. Caso não tenha uma, inscreva-se para experimentar a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).
- Instale o [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), um editor de código leve multiplataforma. 

## <a name="install-the-extension"></a>Instalar a extensão

1. Abra o Visual Studio Code.
1. Selecione o ícone **Extensões** na **Barra de Atividade** para abrir a exibição Extensões.
1. Na exibição Extensões, pesquise "Azure Machine Learning".
1. Selecione **Instalar**.

> [!NOTE]
> Como alternativa, você pode instalar a extensão do Azure Machine Learning por meio do Visual Studio Marketplace [baixando o instalador diretamente](https://aka.ms/vscodetoolsforai). 

O restante das etapas deste tutorial foi testado com a **versão 0.6.8** da extensão.

## <a name="sign-in-to-your-azure-account"></a>Entrar em sua conta do Azure

Para provisionar recursos e executar cargas de trabalho no Azure, você precisa entrar com suas credenciais de conta do Azure. Para auxiliar no gerenciamento de conta, o Azure Machine Learning instala automaticamente a extensão Conta do Azure. Visite o site a seguir para [saber mais sobre a extensão Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Abra a paleta de comandos selecionando **Exibição > Paleta de Comandos** na barra de menus. 
1. Insira o comando "Azure: Entrar" na caixa de texto para iniciar o processo de entrada.

## <a name="run-a-script-in-azure"></a>Executar um script no Azure

Agora que você entrou no Azure com suas credenciais de conta, use as etapas desta seção para saber como usar a extensão para treinar um modelo de machine learning.

1. Baixe e descompacte o [repositório do VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar no computador.
1. Abra o diretório `mnist-vscode-docs-sample` no Visual Studio Code.
1. Selecione o ícone **Azure** na barra de atividade.
1. Selecione o ícone **Executar Experimento** na parte superior da Exibição do Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Executar o experimento](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando a paleta de comandos for expandida, siga os avisos.

    1. Selecione sua assinatura do Azure.
    1. Selecione **Criar um Workspace do Azure ML**
    1. Selecione o tipo de trabalho **Treinamento de Nó Único do TensorFlow**.
    1. Insira `train.py` como o script a ser treinado. Esse é o arquivo que contém o código para um modelo de machine learning que categoriza imagens de dígitos manuscritos.
    1. Especifique os pacotes a seguir como requisitos para execução.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. Neste ponto, um arquivo de configuração semelhante ao mostrado abaixo é exibido no editor de texto. A configuração contém as informações necessárias para executar o trabalho de treinamento, como o arquivo que contém o código para treinar o modelo e as dependências do Python especificadas na etapa anterior.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Selecione **Enviar experimento** para executar o experimento no Azure. Isso envia o `train.py` e o arquivo de configuração para o Workspace do Azure Machine Learning. O trabalho de treinamento é então iniciado em um recurso de computação no Azure.
1. Após alguns minutos, um diretório chamado `output` é criado localmente, contendo um modelo treinado do TensorFlow.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar e implantar um modelo do TensorFlow de classificação de imagem usando a extensão do Azure Machine Learning para Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
