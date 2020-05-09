---
title: Como executar blocos de anotações do Jupyter em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como executar um Jupyter Notebook sem sair do espaço de trabalho no Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783650"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Como executar blocos de anotações do Jupyter em seu espaço de trabalho
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como executar seus blocos de anotações do Jupyter diretamente em seu espaço de trabalho no Azure Machine Learning Studio. Embora seja possível iniciar o [Jupyter](https://jupyter.org/) ou o [JupyterLab](https://jupyterlab.readthedocs.io), você também pode editar e executar seus blocos de anotações sem sair do espaço de trabalho.

Veja como você pode:

* Criar blocos de anotações do Jupyter em seu espaço de trabalho
* Executar um experimento de um notebook
* Alterar o ambiente do bloco de anotações
* Encontre detalhes das instâncias de computação usadas para executar seus blocos de anotações

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Criar blocos de anotações

No espaço de trabalho Azure Machine Learning, crie um novo notebook Jupyter e comece a trabalhar. O notebook recém-criado é armazenado no armazenamento de espaço de trabalho padrão. Este bloco de anotações pode ser compartilhado com qualquer pessoa com acesso ao espaço de trabalho. 

Para criar um novo bloco de anotações: 

1. Abra seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **notebooks**. 
1. Selecione o ícone **criar novo arquivo** acima da lista **arquivos de usuário** na seção **meus arquivos** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar novo arquivo":::

1. Nomeie o arquivo. 
1. Para Jupyter Notebook arquivos, selecione **Notebook Python** como o tipo de arquivo.
1. Selecione um diretório de arquivos.
1. Selecione **Criar**.

> [!TIP]
> Você também pode criar arquivos de texto.  Selecione **texto** como o tipo de arquivo e adicione a extensão ao nome (por exemplo, MyFile.py ou MyFile. txt)  

Você também pode carregar pastas e arquivos, incluindo blocos de anotações, com as ferramentas na parte superior da página blocos de anotações.  Os blocos de anotações e a maioria dos tipos de arquivo de texto são exibidos na seção visualização.  Nenhuma visualização está disponível para a maioria dos outros tipos de arquivo.

### <a name="clone-samples"></a>Clonar amostras

Seu espaço de trabalho contém uma pasta de **exemplos** com blocos de anotações criados para ajudá-lo a explorar o SDK e servir como exemplos para seus próprios projetos de Machine Learning.  Você pode clonar esses blocos de anotações em sua própria pasta no seu contêiner de armazenamento do espaço de trabalho.  

Para obter um exemplo, consulte [tutorial: criar seu primeiro experimento de ml](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">Usar arquivos do git e da versão meus arquivos

Você pode acessar todas as operações do git usando uma janela do terminal. Todos os arquivos e pastas git serão armazenados no sistema de arquivos do espaço de trabalho.

> [!NOTE]
> Adicione seus arquivos e pastas em qualquer lugar na pasta **~/CloudFiles/Code/Users** para que eles fiquem visíveis em todos os seus ambientes Jupyter.

Para acessar o terminal:

1. Abra seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **notebooks**.
1. Selecione qualquer notebook localizado na seção **arquivos do usuário** no lado esquerdo.  Se você não tiver nenhum bloco de anotações, primeiro [crie um bloco de anotações](#create)
1. Selecione um destino de **computação** ou crie um novo e aguarde até que ele esteja em execução.
1. Selecione o ícone **abrir terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Abra o terminal":::

1. Se você não vir o ícone, selecione o **...** à direita do destino de computação e, em seguida, selecione **abrir terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Abrir terminal de...":::


Saiba mais sobre como [clonar repositórios git em seu sistema de arquivos de espaço de trabalho](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Compartilhar blocos de anotações e outros arquivos

Copie e cole a URL para compartilhar um bloco de anotações ou arquivo.  Somente outros usuários do espaço de trabalho poderão acessar essa URL.  Saiba mais sobre como [conceder acesso ao seu espaço de trabalho](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Editar um bloco de anotações

Para editar um bloco de anotações, abra qualquer bloco de anotações localizado na seção **arquivos de usuário** do seu espaço de trabalho. Clique na célula que você deseja editar. 

Quando uma instância de computação em execução estiver em execução, você também poderá usar a conclusão de código, da plataforma [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), em qualquer notebook Python.

Você também pode iniciar o Jupyter ou o JupyterLab na barra de ferramentas do bloco de anotações.  Azure Machine Learning não fornece atualizações e corrige bugs de Jupyter ou JupyterLab, pois são produtos de software livre fora do limite de Suporte da Microsoft.

### <a name="useful-keyboard-shortcuts"></a>Atalhos de teclado úteis

|Keyboard  |Ação  |
|---------|---------|
|Shift+Enter     |  Executar uma célula       |
|CTRL + M (Windows)     |  Habilitar/desabilitar o ajuste de tabulação no bloco de anotações.       |
|Ctrl + Shift + M (Mac & Linux)     |    Habilitar/desabilitar o ajuste de tabulação no bloco de anotações.     |
|Tab (quando a captura de guias estiver habilitada) | Adicionar um caractere ' \t ' (recuo)
|Guia (quando a captura de guias está desabilitada) | Alterar o foco para o próximo item de foco (botão excluir célula, botão Executar etc.)

## <a name="delete-a-notebook"></a>Excluir um notebook

*Não é possível* excluir os blocos de anotações de **exemplos** .  Esses blocos de anotações fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

Você *pode* excluir blocos de anotações de **arquivos de usuário** de qualquer uma destas maneiras:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Certifique-se de usar um navegador com suporte (Microsoft Edge, Chrome ou Firefox).
* Em qualquer barra de ferramentas do bloco de anotações, selecione [**abrir terminal**](#terminal) para acessar a janela do terminal da instância de computação.
* Em Jupyter ou JupyterLab com suas ferramentas.

## <a name="run-an-experiment"></a>Executar um experimento

Para executar um experimento de um notebook, primeiro você se conecta a uma [instância de computação](concept-compute-instance.md)em execução. Se você não tiver uma instância de computação, use estas etapas para criar uma: 

1. Selecione **+** na barra de ferramentas do bloco de anotações. 
2. Nomeie a computação e escolha um **tamanho de máquina virtual**. 
3. Selecione **Criar**.
4. A instância de computação é conectada ao bloco de anotações automaticamente e agora você pode executar as células.

Somente você pode ver e usar as instâncias de computação que criar.  Os **arquivos de usuário** são armazenados separadamente da VM e compartilhados entre todas as instâncias de computação no espaço de trabalho.

### <a name="view-logs-and-output"></a>Exibir logs e saída

Use [widgets de bloco de anotações](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) para exibir o progresso da execução e dos logs. Um widget é assíncrono e fornece atualizações até que o treinamento seja concluído. Azure Machine Learning widgets também têm suporte em Jupyter e JupterLab.

## <a name="change-the-notebook-environment"></a>Alterar o ambiente do bloco de anotações

A barra de ferramentas do bloco de anotações permite que você altere o ambiente no qual seu notebook é executado.  

Essas ações não alterarão o estado do bloco de anotações ou os valores de qualquer variável no bloco de anotações:

|Ação  |Result  |
|---------|---------| --------|
|Parar o kernel     |  Interrompe qualquer célula em execução. A execução de uma célula irá reiniciar automaticamente o kernel. |
|Navegar para outra seção do espaço de trabalho     |     As células em execução são interrompidas. |

Essas ações redefinirão o estado do bloco de anotações e redefinirão todas as variáveis no bloco de anotações.

|Ação  |Result  |
|---------|---------| --------|
| Alterar o kernel | O notebook usa o novo kernel |
| Alternar computação    |     O notebook usa automaticamente a nova computação. |
| Redefinir computação | Inicia novamente quando você tenta executar uma célula |
| Parar computação     |    Nenhuma célula será executada  |
| Abrir o bloco de anotações em Jupyter ou JupyterLab     |    Bloco de anotações aberto em uma nova guia.  |

### <a name="add-new-kernels"></a>Adicionar novos kernels

O notebook localizará automaticamente todos os kernels do Jupyter instalados na instância de computação conectada.  Para adicionar um kernel à instância de computação:

1. Selecione [**abrir terminal**](#terminal) na barra de ferramentas do bloco de anotações.
1. Use a janela do terminal para criar um novo ambiente.
1. Ative o ambiente.  Por exemplo, depois de `newenv`criar:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [kernels Jupyter disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

### <a name="status-indicators"></a>Indicadores de status

Um indicador ao lado da lista suspensa **computação** mostra seu status.  O status também é mostrado no menu suspenso em si.  

|Cor |Status da computação |
|---------|---------| 
| Verde | Execução de computação |
| Vermelho |Falha na computação | 
| Preto | Computação parada |
|  Azul-claro |Computação, criando, iniciando, reiniciando, configurando |
|  Cinza |Computação excluindo, parando |

Um indicador ao lado da lista suspensa **kernel** mostra seu status.

|Cor |Status do kernel |
|---------|---------|
|  Verde |Kernel conectado, ocioso, ocupado|
|  Cinza |Kernel não conectado |

## <a name="find-compute-details"></a>Localizar detalhes de computação 

Encontre detalhes sobre suas instâncias de computação na página **computação** no [estúdio](https://ml.azure.com).

## <a name="next-steps"></a>Próximas etapas

* [Executar seu primeiro experimento](tutorial-1st-experiment-sdk-train.md)
* [Fazer backup do armazenamento de arquivos com instantâneos](../storage/files/storage-snapshots-files.md)
