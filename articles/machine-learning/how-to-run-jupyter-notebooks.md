---
title: Como executar Jupyter Notebooks em seu workspace
titleSuffix: Azure Machine Learning
description: Saiba como executar um Jupyter Notebook sem sair do workspace no Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681452"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Como executar Jupyter Notebooks em seu workspace
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como executar seus Jupyter Notebooks diretamente em seu workspace no Azure Machine Learning Studio. Embora seja possível iniciar o [Jupyter](https://jupyter.org/) ou o [JupyterLab](https://jupyterlab.readthedocs.io), você também pode editar e executar seus notebooks sem sair do workspace.

Veja como é possível:

* Criar Jupyter Notebooks em seu workspace
* Executar um experimento de um notebook
* Alterar o ambiente do notebook
* Encontre detalhes das instâncias de computação usadas para executar seus notebooks

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Criar notebooks

No Workspace do Azure Machine Learning, crie um Jupyter Notebook e comece a trabalhar. O notebook recém-criado é armazenado no armazenamento do workspace padrão. Este notebook pode ser compartilhado com qualquer pessoa com acesso ao workspace. 

Para criar um notebook: 

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**. 
1. Selecione o ícone **Criar arquivo** acima da lista **Arquivos do usuário** na seção **Meus arquivos**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar arquivo":::

1. Dê um nome ao arquivo. 
1. Para arquivos do Jupyter Notebook, selecione **Python Notebook** como o tipo de arquivo.
1. Selecione um diretório de arquivos.
1. Selecione **Criar**.

> [!TIP]
> Você também pode criar arquivos de texto.  Selecione **Texto** como o tipo de arquivo e adicione a extensão ao nome (por exemplo, myfile.py ou myfile. txt)  

Você também pode carregar pastas e arquivos, incluindo notebooks, com as ferramentas na parte superior da página Notebooks.  Os notebooks e a maioria dos tipos de arquivo de texto são exibidos na seção de versão prévia.  Nenhuma versão prévia está disponível para a maioria dos outros tipos de arquivo.

### <a name="clone-samples"></a>Clonar amostras

Seu workspace contém uma pasta **Amostras** com notebooks criados para ajudá-lo a explorar o SDK e servir como exemplos para seus projetos de Machine Learning.  Você pode clonar esses notebooks em sua pasta no seu contêiner de armazenamento do workspace.  

Para um exemplo, veja [Tutorial: Criar seu primeiro experimento de ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Usar arquivos do Git e da criar versão de meus arquivos

Você pode acessar todas as operações do git usando uma janela do terminal. Todos os arquivos e pastas do Git serão armazenados no sistema de arquivos do workspace.

> [!NOTE]
> Adicione seus arquivos e pastas em qualquer lugar na pasta **~/cloudfiles/code/Users** para que eles fiquem visíveis em todos os seus ambientes do Jupyter.

Para acessar o terminal:

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**.
1. Selecione qualquer notebook localizado na seção **Arquivos do usuário** no lado esquerdo.  Se você não tiver nenhum notebook, primeiro [crie um notebook](#create)
1. Selecione um destino de **Computação** ou crie um e aguarde até que ele esteja em execução.
1. Selecione o ícone **Abrir terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Abra o terminal":::

1. Se você não vir o ícone, selecione **...** à direita do destino de computação e, em seguida, selecione **Abrir terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Abrir terminal de...":::


Saiba mais sobre [clonar repositórios Git em seu sistema de arquivos do workspace](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Compartilhar notebooks e outros arquivos

Copie e cole o URL para compartilhar um notebook ou arquivo.  Somente outros usuários do workspace poderão acessar essa URL.  Saiba mais sobre [conceder acesso ao seu workspace](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Editar um notebook

Para editar um notebook, abra qualquer notebook localizado na seção **Arquivos de usuário** do workspace. Clique na célula que você deseja editar. 

Quando uma instância de computação em execução estiver em execução, você também poderá usar a conclusão de código, da plataforma [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), em qualquer Python Notebook.

Você também pode iniciar o Jupyter ou o JupyterLab na barra de ferramentas Notebook.  O Azure Machine Learning não fornece atualizações nem corrige bugs do Jupyter ou do JupyterLab, pois são produtos de software livre fora do limite do Suporte da Microsoft.

### <a name="useful-keyboard-shortcuts"></a>Atalhos de teclado úteis

|Teclado  |Ação  |
|---------|---------|
|Shift+Enter     |  Executar uma célula       |
|Ctrl+M(Windows)     |  Habilitar/desabilitar o trapping de tabulação no notebook.       |
|Ctrl+Shift+M(Mac e Linux)     |    Habilitar/desabilitar o trapping de tabulação no notebook.     |
|Tab (quando a captura de guias está habilitada) | Adicionar um caractere '\t' (recuo)
|Guia (quando a captura de guias está desabilitada) | Alterar o foco para o próximo item de foco (botão excluir célula, botão executar etc.)

## <a name="delete-a-notebook"></a>Excluir um notebook

Você *não pode* excluir os notebooks de **Exemplos**.  Esses blocos notebooks fazem parte do estúdio e são atualizados sempre que um novo SDK é publicado.  

Você *pode* excluir notebooks de **arquivos de usuário** de qualquer uma destas maneiras:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Use um navegador compatível (Microsoft Edge, Chrome ou Firefox).
* Em qualquer barra de ferramentas Notebook, selecione [**Abrir terminal**](#terminal) para acessar a janela do terminal da instância de computação.
* No Jupyter ou no JupyterLab com suas ferramentas.

## <a name="run-an-experiment"></a>Executar um experimento

Para executar um experimento de um Notebook, primeiro você se conecta a uma [instância de computação](concept-compute-instance.md) em execução. Se você não tiver uma instância de computação, use estas etapas para criar uma: 

1. Selecione **+** na barra de ferramentas Notebook. 
2. Dê um nome a Computação e escolha um **Tamanho da Máquina Virtual**. 
3. Selecione **Criar**.
4. A instância de computação é conectada ao Notebook automaticamente e agora você pode executar as células.

Somente você pode ver e usar as instâncias de computação que criar.  Seus **Arquivos de usuário** são armazenados separadamente da VM e compartilhados entre todas as instâncias de computação no workspace.

### <a name="view-logs-and-output"></a>Exibir logs e saída

Use [Widgets do Notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) para ver o progresso da execução e dos logs. Um widget é assíncrono e fornece atualizações até que o treinamento seja concluído. Os widgets do Azure Machine Learning também têm suporte no Jupyter e no JupterLab.

## <a name="change-the-notebook-environment"></a>Alterar o ambiente do notebook

A barra de ferramentas do Notebook permite que você altere o ambiente no qual seu Notebook é executado.  

Essas ações não alteraram o estado do notebook ou os valores de nenhuma variável no notebook:

|Ação  |Result  |
|---------|---------| --------|
|Parar o kernel     |  Para qualquer célula em execução. A execução de uma célula reiniciará automaticamente o kernel. |
|Navegar para outra seção do workspace     |     As células em execução são interrompidas. |

Essas ações redefinirão o estado do notebook e redefinirão todas as variáveis no notebook.

|Ação  |Result  |
|---------|---------| --------|
| Altere o Kernel | O Notebook usa o novo kernel |
| Alternar computação    |     O Notebook usa automaticamente a nova computação. |
| Redefinir computação | Inicia novamente quando você tenta executar uma célula |
| Parar a computação     |    Nenhuma célula será executada  |
| Abrir o notebook no Jupyter ou JupyterLab     |    Notebook aberto em uma nova guia.  |

### <a name="add-new-kernels"></a>Adicionar novos kernels

O Notebook localizará automaticamente todos os kernels do Jupyter instalados na instância de computação conectada.  Para adicionar um kernel à instância de computação:

1. Selecione [**Abrir terminal**](#terminal) na barra de ferramentas Notebook.
1. Use a janela do terminal para criar um ambiente.
1. Ative o ambiente.  Por exemplo, depois de criar `newenv`:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [Kernels do Jupyter disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

### <a name="status-indicators"></a>Indicadores de status

Um indicador ao lado do menu suspenso **Computação** mostra o status.  O status também é mostrado no menu suspenso em si.  

|Color |Status da computação |
|---------|---------| 
| Verde | Execução de computação |
| Vermelho |Falha na computação | 
| Preto | Computação parada |
|  Azul-claro |Criar, iniciar, reiniciar, configurar computação |
|  Cinza |Excluir, parar computação |

Um indicador ao lado do menu suspenso **Kernel** mostra seu status.

|Color |Status do kernel |
|---------|---------|
|  Verde |Kernel conectado, ocioso, ocupado|
|  Cinza |Kernel não conectado |

## <a name="find-compute-details"></a>Localizar detalhes da computação 

Encontre detalhes sobre suas instâncias de computação na página **Computação** no [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Próximas etapas

* [Executar seu primeiro experimento](tutorial-1st-experiment-sdk-train.md)
* [Fazer backup do armazenamento de arquivos com instantâneos](../storage/files/storage-snapshots-files.md)
