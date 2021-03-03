---
title: Introdução ao desenvolvimento avançado do Azure Percept local
description: Introdução ao desenvolvimento de machine learning local usando o VS Code + Jupyter Notebooks no Azure ML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658637"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Introdução ao desenvolvimento de machine learning usando o VS Code + Jupyter Notebooks no Azure ML

Este artigo explica o processo de configuração de um Workspace do Azure Machine Learning, a criação de uma instância de computação, a configuração de um ambiente de desenvolvimento do Visual Studio Code no computador local e a execução das células de um Jupyter Notebook de exemplo pré-configurado no VS Code.

O [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) executa a transferência de aprendizado usando um modelo pré-treinado do TensorFlow (MobileNetSSDV2Lite) no Azure ML em Python com um conjunto de dados personalizado para detectar tigelas.

O notebook mostra como começar no [conjunto de dados COCO](https://cocodataset.org/#home), filtrá-lo apenas para a classe de interesse (tigelas) e convertê-lo para o formato apropriado. Como alternativa, você pode usar a ferramenta de rotulagem de software livre [VoTT 2](https://github.com/microsoft/VoTT) para criar e rotular caixas delimitadoras no formato PASCAL VOC.

Depois de treinar novamente o modelo no conjunto de dados personalizado, o modelo pode ser implantado no Azure Percept DK usando o método de atualização de módulo gêmeo. Você pode verificar a inferência do modelo exibindo o fluxo RTSP ao vivo do SoM de pesquisa visual do Azure Percept. O retreinamento e a implantação do modelo são executados no notebook por meio de sua instância de computação remota.

## <a name="prerequisites"></a>Pré-requisitos

- [Assinatura do Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK com SoM do Azure Percept Vision conectado](./overview-azure-percept-dk.md)
- [Experiência de integração do Azure Percept DK](./quickstart-percept-dk-set-up.md) concluída

## <a name="download-azure-percept-github-repository"></a>Baixar o repositório GitHub do Azure Percept

1. Acesse o [repositório GitHub do Azure Percept DK](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Clone o repositório ou baixe o arquivo ZIP. Próximo à parte superior da tela, clique em **Código** -> **Baixar ZIP**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Tela de download do GitHub.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Criar um workspace e uma instância de computação remota do Azure Machine Learning

1. Acesse o [portal do Azure Machine Learning](https://ml.azure.com).
1. Selecione seu diretório, a assinatura do Azure e o workspace do Machine Learning nos menus suspensos e clique em **Introdução**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Tela de introdução do Azure ML.":::

    Se você ainda não tiver um workspace do Azure Machine Learning, clique em **Criar um workspace**. Na nova guia do navegador, faça o seguinte:

    1. Selecione sua assinatura do Azure.
    1. Selecione o grupo de recursos.
    1. insira um nome para o workspace.
    1. Selecione sua região.
    1. Selecione a edição do workspace.
    1. Clique em **Examinar e criar**.
    1. Na próxima página, examine suas seleções e clique em **Criar**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Tela de criação do workspace no Azure ML.":::

    Aguarde alguns minutos para a criação do workspace. Após o workspace ser criado, você verá marcas de seleção verdes ao lado de seus recursos e a mensagem **Sua implantação foi concluída** na parte superior da página de visão geral dos Serviços de Machine Learning.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Confirmação da criação do workspace." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Quando a criação do workspace for concluída, volte para a guia do portal do Machine Learning e clique em **Introdução**.

1. Na home page do Workspace do Machine Learning, clique em **Computação** no painel à esquerda.

1. Se não houver nenhuma instância de computação existente, crie uma computação de CPU ou GPU clicando em **Novo**. Na janela **Nova instância de computação**, insira um **Nome da computação**, escolha um **Tipo de máquina virtual** e selecione um **Tamanho de máquina virtual**. Clique em **Criar**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Tela de criação da instância de computação.":::

    Depois que você clicar em **Criar**, levará alguns minutos para a instância de computação ser criada. Após a **Computação** ser criada, o status dela exibirá um círculo verde e **\<your compute name> – Em execução**. Esta instância de computação executa o servidor Jupyter e será utilizada para este tutorial.

## <a name="visual-studio-code-development-environment-setup"></a>Configuração do ambiente de desenvolvimento do Visual Studio Code

1. Instale as ferramentas necessárias.

    1. Opção 1:

        Use o [Instalador do Pacote de Ferramentas de Desenvolvimento](./dev-tools-installer.md) para instalar os seguintes pacotes:

        - Visual Studio Code
        - Python 3.5, 3.6 ou 3.7
        - Miniconda3
        - Kit de Ferramentas Intel OpenVino 2020.2

        Observação: o Kit de Ferramentas Intel OpenVino está listado como um pacote opcional no Instalador do Pacote de Ferramentas de Desenvolvimento, mas é necessário para trabalhar com o SoM da Pesquisa Visual do Azure Percept do Kit de Desenvolvimento do Azure Percept DK.

    1. Opção 2:

        Se você preferir não usar o Instalador do Pacote de Ferramentas de Desenvolvimento, instale manualmente os seguintes pacotes clicando nos links abaixo e seguindo as diretrizes de download e instalação especificadas:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5, 3.6 ou 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Kit de Ferramentas Intel OpenVino 2020.2](https://docs.openvinotoolkit.org/)

    Observação: se você já tiver a distribuição completa do Anaconda instalada, não será necessário instalar o Miniconda. Como alternativa, se você preferir não usar o Anaconda nem o Miniconda, poderá criar um ambiente virtual do Python e instalar os pacotes necessários para executar seu desenvolvimento de modelo de IA usando pip.

1. Inicie o Visual Studio Code.
1. Configurar um ambiente de ciência de dados:

    - Opção 1– conectar-se a uma instância de computação remota nova ou existente de machine learning que já tem os pacotes de ML coletados. **Essa é a opção que usaremos neste tutorial**.

    - Opção 2 – configurar um ambiente Conda em um computador local.
        1. Abra um prompt de comando do Anaconda ou Miniconda e execute o seguinte comando para criar um ambiente chamado **myenv** com os pacotes especificados:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Para obter informações adicionais sobre como criar e gerenciar ambientes do Anaconda, confira a [documentação do Anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Criar um workspace do VS Code:

    1. Crie uma pasta em uma localização conveniente para servir como seu workspace do Visual Studio Code. Nomeie-o **myworkspace**.
    1. Abra **myworkspace** no Visual Studio Code clicando em **Arquivo** > **Abrir Pasta** > **Selecionar Pasta**.
    1. No Explorador de Arquivos, em sua cópia local do repositório GitHub do Azure Percept DK, navegue até o arquivo [Transferlearningusing_SSDLiteV2 Model.ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) e selecione-o. Copie este arquivo de notebook para a pasta myworkspace.

## <a name="azure-integration-with-visual-studio-code"></a>Integração do Azure com o Visual Studio Code

1. Caso você ainda não tenha uma, inscreva-se para experimentar a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).

1. Instale as seguintes extensões do Azure para VS Code:
    - [Extensão do Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Extensão do Python Insiders. No VS Code, acesse **Exibir** -> **Paleta de Comandos**. Na paleta de comandos, insira e selecione **Python: Switch to Insiders Daily Channel**.
    - [Extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Recarregue sua janela no VS Code quando solicitado.
    - [Extensão do Kit de Ferramentas do Hub IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Entre em sua conta do Azure no Visual Studio Code para provisionar recursos e executar cargas de trabalho no Azure.
    1. No Visual Studio Code, abra a paleta de comandos selecionando **Exibição** > **Paleta de Comandos** na barra de menus.
    1. Insira **Azure: Sign In** na paleta de comandos para iniciar o processo de logon.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Tela de entrada do Azure." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Ative a extensão do Python e a conta do Azure clicando no ícone do Azure no lado esquerdo de VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Ícone do Azure no VS Code.":::

    1. Abra o notebook Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb na pasta **myworkspace**.
    1. Abra a paleta de comandos. Insira e selecione **Python: especificar servidor Jupyter local ou remoto para conexões**.
    1. Você deverá ver uma lista de opções de conexão dentre as quais poderá escolher. Selecione **Instâncias de Computação do Azure ML**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Opções de instância de computação do Azure ML no VS Code.":::

    1. Siga os prompts guiados para escolher uma assinatura, um workspace e uma instância de computação remota. Selecione o workspace e a instância de computação remota criados anteriormente neste tutorial. Você também tem a opção de criar uma outra instância de computação.
    1. Depois de selecionar uma instância de computação, você será solicitado a recarregar sua janela do VS Code. Depois de recarregar, selecione o kernel **Python 3.6 – Azure ML**. Agora você pode executar qualquer uma das células no notebook. A execução de uma célula do notebook criará uma instância da conexão entre o notebook e a instância de computação. A barra de ferramentas do notebook será atualizada para refletir a instância de computação em que você está trabalhando.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Seleção de kernel no VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Trabalhar com o notebook

Agora você está pronto para executar o notebook a fim de treinar o detector de tigelas personalizado no VS Code e implantá-lo em seu devkit. Execute cada célula do notebook individualmente, pois algumas delas exigem parâmetros de entrada antes da execução do script. As células que exigem parâmetros de entrada podem ser editadas diretamente no notebook. Para executar uma célula, clique no ícone de reprodução no lado esquerdo da célula:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Notebook com ícone de célula destacado.":::

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de notebooks do Serviço do Azure Machine Learning, visite este [repositório](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
