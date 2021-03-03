---
title: Introdução ao desenvolvimento avançado do Azure Percept na nuvem
description: Introdução ao desenvolvimento avançado na nuvem por meio de notebooks Jupyter e Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658614"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Introdução ao desenvolvimento avançado na nuvem por meio de notebooks Jupyter e Azure Machine Learning

Este artigo explica o processo de configuração de um workspace do Azure Machine Learning, o upload de um notebook Jupyter de exemplo pré-configurado para o workspace, a criação de uma instância de computação e a execução das células do notebook no workspace.

O [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) executa a transferência de aprendizado usando um modelo pré-treinado do TensorFlow (MobileNetSSDV2Lite) no AzureML em Python com um conjunto de dados personalizado para detectar tigelas.

O notebook mostra como começar no [COCO](https://cocodataset.org/#home), filtrá-lo apenas para a classe de interesse (tigelas) e convertê-lo para o formato apropriado. Como alternativa, você pode usar a ferramenta de rotulagem open-source [VoTT 2](https://github.com/microsoft/VoTT) para criar e rotular caixas delimitadoras no formato PASCAL VOC.

Depois de treinar novamente o modelo no conjunto de dados personalizado, o modelo pode ser implantado no Azure Percept DK usando o método de atualização de módulo gêmeo.

Você pode verificar a inferência do modelo exibindo o fluxo RTSP ao vivo do SoM do Azure Percept Vision. O novo treinamento e a implantação do modelo são executados dentro do notebook na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- [Assinatura do Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK com SoM do Azure Percept Vision conectado](./overview-azure-percept-dk.md)
- [Experiência de integração do Azure Percept DK](./quickstart-percept-dk-set-up.md) concluída

## <a name="download-azure-percept-github-repository"></a>Baixar o repositório GitHub do Azure Percept

1. Acesse o [repositório GitHub do Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Clone o repositório ou baixe o arquivo ZIP. Próximo à parte superior da tela, clique em **Código** -> **Baixar ZIP**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Tela de download do GitHub.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Configurar o portal do Azure Machine Learning e o notebook

1. Vá para o [portal do Azure Machine Learning](https://ml.azure.com).

1. Selecione seu diretório, a assinatura do Azure e o workspace do Machine Learning nos menus suspensos e clique em **Introdução**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Tela de introdução do Azure ML.":::

    Se você ainda não tiver um workspace do Azure Machine Learning, clique em **Criar um workspace**. Na nova guia do navegador, faça o seguinte:

    1. Selecione sua assinatura do Azure.
    1. Selecione o grupo de recursos.
    1. insira um nome para o workspace.
    1. Selecione sua região.
    1. Selecione a edição do workspace.
    1. Clique em **Examinar e criar**.
    1. Na próxima página, examine suas seleções e clique em **Criar**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Tela de criação do workspace no Azure ML.":::

    Aguarde alguns minutos para a criação do workspace. Após o workspace ser criado, você verá marcas de seleção verdes ao lado de seus recursos e a mensagem **Sua implantação foi concluída** na parte superior da página de visão geral dos Serviços de Machine Learning.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Confirmação da criação do workspace." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Quando a criação do workspace for concluída, volte para a guia do portal do Machine Learning e clique em **Introdução**.

1. Na home page do workspace do Machine Learning, clique em **Notebooks** no painel esquerdo.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Home page do Azure ML.":::

1. Na guia **Meus arquivos**, clique na seta vertical para carregar o arquivo .ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Home page com o ícone Carregar arquivos destacado.":::

1. Em sua cópia local do repositório GitHub do Azure Percept, navegue até o arquivo [Transferlearningusing_SSDLiteV2 Model.ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) e selecione-o. Clique em **Abrir**. Na janela **Carregar arquivos**, marque a caixa ao lado de **Confiar no conteúdo deste arquivo** e clique em **Carregar**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Tela de seleção de arquivo.":::

1. Na barra de menus superior direita, verifique o status da **Computação**. Se nenhuma computação for encontrada, clique no ícone **+** para criar uma.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Status da computação com ícone + realçado." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Na janela **Nova instância de computação**, insira um **Nome da computação**, escolha um **Tipo de máquina virtual** e selecione um **Tamanho de máquina virtual**. Clique em **Criar**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Tela de criação da instância de computação.":::

    Depois que você clicar em **Criar**, o status da **Computação** exibirá um círculo azul e **\<your compute name> – Criando**

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Status da computação quando a criação dela ainda está em andamento.":::

    O status da **Computação** exibirá um círculo verde e **\<your compute name> – Em execução** após a computação ser criada.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="Status da computação mostrando que a criação dela foi concluída.":::

1. Depois que a computação estiver em execução, selecione o kernel **Python 3.6 – AzureML** no menu suspenso ao lado do ícone **+** .

## <a name="working-with-the-notebook"></a>Trabalhar com o notebook

Agora você está pronto para executar o notebook para treinar o detector de tigelas personalizado e implantá-lo em seu kit de desenvolvimento. Execute cada célula do notebook individualmente, pois algumas delas exigem parâmetros de entrada antes da execução do script. As células que exigem parâmetros de entrada podem ser editadas diretamente no notebook. Para executar uma célula, clique no ícone de reprodução no lado esquerdo da célula:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Notebook com ícone de célula destacado." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de notebooks do Serviço do Azure Machine Learning, acesse este [repositório](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
