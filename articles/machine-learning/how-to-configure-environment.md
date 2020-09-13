---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning
description: Saiba como configurar seu ambiente de desenvolvimento para Azure Machine Learning. Use ambientes Conda, Crie arquivos de configuração e configure seu próprio servidor de notebook baseado em nuvem, notebooks Jupyter, Azure Databricks, IDEs, editores de código e o Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8ad22abdf17c68c93a6189db839fe357e7acc91e
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650837"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a configurar um ambiente de desenvolvimento para trabalhar com Azure Machine Learning. Azure Machine Learning é independente da plataforma. O único requisito rígido para seu ambiente de desenvolvimento é o Python 3. Um ambiente isolado como Anaconda ou Virtualenv também é recomendado.

A tabela a seguir mostra cada ambiente de desenvolvimento abordado neste artigo, com os prós e contras.

| Ambiente | Vantagens | Desvantagens |
| --- | --- | --- |
| [Instância de computação de Azure Machine Learning baseada em nuvem](#compute-instance) | A maneira mais fácil de começar. O SDK completo já está instalado na VM do seu espaço de trabalho, e os tutoriais do notebook são previamente clonados e prontos para serem executados. | Falta de controle sobre seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para a VM do Linux (a VM pode ser interrompida quando não estiver em uso para evitar cobranças). Consulte [detalhes de preço](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Ambiente local](#local) | Controle total do seu ambiente de desenvolvimento e dependências. Execute com qualquer ferramenta de compilação, ambiente ou IDE de sua escolha. | Leva mais tempo para começar. Os pacotes SDK necessários devem ser instalados e um ambiente também deve ser instalado se você ainda não tiver um. |
| [Azure Databricks](#aml-databricks) | Ideal para executar fluxos de trabalho de aprendizado de máquina com uso intensivo em larga escala na plataforma Apache Spark escalonável. | Um exagero para aprendizado de máquina experimental ou experimentos e fluxos de trabalho de escala menor. Custo adicional incorrido para Azure Databricks. Consulte [detalhes de preço](https://azure.microsoft.com/pricing/details/databricks/). |
| [O Máquina Virtual de Ciência de Dados (DSVM)](#dsvm) | Semelhante à instância de computação baseada em nuvem (o Python e o SDK são pré-instalados), mas com uma ciência de dados popular adicional e ferramentas de aprendizado de máquina pré-instaladas. Fácil de dimensionar e combinar com outras ferramentas e fluxos de trabalho personalizados. | Uma experiência de introdução mais lenta em comparação com a instância de computação baseada em nuvem. |

Este artigo também fornece dicas de uso adicionais para as seguintes ferramentas:

* [Jupyter notebooks](#jupyter): se você já estiver usando o Jupyter notebook, o SDK terá alguns extras que você deve instalar.

* [Visual Studio Code](#vscode): se você usar Visual Studio Code, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) incluirá amplo suporte a idiomas para Python, bem como recursos para tornar o trabalho com os Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md). Um espaço de trabalho é tudo o que você precisa para começar com seu próprio [servidor de bloco de anotações baseado em nuvem](#compute-instance), um [DSVM](#dsvm)ou um [Azure Databricks](#aml-databricks).

Para instalar o ambiente do SDK do [computador local](#local), [Jupyter Notebook Server](#jupyter) ou [Visual Studio Code](#vscode) você também precisa:

- O Gerenciador de pacotes [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) .

- No Linux ou MacOS, será necessário o shell do bash.

    > [!TIP]
    > Se você estiver no Linux ou no macOS e usar um shell diferente do bash (por exemplo, zsh), poderá receber erros ao executar alguns comandos. Para contornar esse problema, use o comando `bash` para iniciar um novo shell do Bash e executar os comandos nele.

- No Windows, você precisa do prompt de comando ou do prompt Anaconda (instalado pelo Anaconda e pelo Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Sua própria instância de computação baseada em nuvem

A [instância de computação](concept-compute-instance.md) Azure Machine Learning é uma estação de trabalho do Azure segura e baseada em nuvem que fornece cientistas de dados com um servidor de notebook Jupyter, JupyterLab e um ambiente de ml totalmente preparado.

Não há nada para instalar ou configurar para uma instância de computação.  Crie uma a qualquer momento em seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo de VM do Azure. Experimente agora com este [tutorial: configurar o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

Para saber mais sobre as instâncias de computação, incluindo como instalar pacotes, consulte [instâncias de computação](concept-compute-instance.md).

Para parar de incorrer em encargos de computação, [interrompa a instância de computação](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Máquina Virtual de Ciência de Dados

A DSVM é uma imagem de VM (máquina virtual) personalizada. Ela foi projetada para trabalhos de ciência de dados pré-configurados com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e o SDK do Azure Machine Learning
  - Ferramentas de ciência de dados populares como o Spark Standalone e Drill
  - Ferramentas do Azure como a CLI do Azure, o AzCopy e o Gerenciador de Armazenamento
  - IDEs (Ambientes de Desenvolvimento Integrados) como o Visual Studio Code e o PyCharm
  - Servidor do Jupyter Notebook

O SDK do Azure Machine Learning funciona na versão da DSVM para Ubuntu ou Windows. Porém, se você também planeja usar a DSVM como um destino de computação, somente o Ubuntu tem suporte.

Para usar o DSVM como um ambiente de desenvolvimento:

1. Crie uma DSVM em um dos seguintes ambientes:

    * Portal do Azure:

        * [Criar um Máquina Virtual de Ciência de Dados Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma Máquina Virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * CLI do Azure:

        > [!IMPORTANT]
        > * Ao usar a CLI do Azure, primeiro você deverá entrar na assinatura do Azure usando o comando `az login`.
        >
        > * Ao usar os comandos nessa etapa, será necessários fornecer um nome do grupo de recursos, um nome para a VM, um nome de usuário e uma senha.

        * Para criar uma Máquina Virtual de Ciência de Dados do Ubuntu, use o comando a seguir:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma Máquina Virtual de Ciência de Dados do Windows, use o comando a seguir:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O SDK do Azure Machine Learning já está instalado na DSVM. Para usar o ambiente do Conda que contém o SDK, use um dos seguintes comandos:

    * Para DSVM do Ubuntu:

        ```bash
        conda activate py36
        ```

    * Para DSVM do Windows:

        ```bash
        conda activate AzureML
        ```

1. Para confirmar que você pode acessar o SDK e verificar a versão, use o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar o DSVM para usar seu espaço de trabalho Azure Machine Learning, consulte a seção [criar um arquivo de configuração de espaço de trabalho](#workspace) .

Para obter mais informações, consulte [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Computador local

Quando você estiver usando um computador local (que também pode ser uma máquina virtual remota), crie um ambiente Anaconda e instale o SDK. Aqui está um exemplo:

1. Baixe e instale o [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3,7 versão) se você ainda não o tiver.

1. Abra um prompt do Anaconda e crie um ambiente com os seguintes comandos:

    Execute o comando a seguir para criar o ambiente.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Em seguida, ative o ambiente.

    ```bash
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando Python 3.7.7, mas qualquer subversão específica pode ser escolhida. A compatibilidade do SDK pode não ser garantida com determinadas versões principais (o 3.5 + é recomendado) e é recomendável tentar uma versão/subversão diferente em seu ambiente Anaconda se você encontrar erros. Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados.

1. Execute os seguintes comandos em seu novo ambiente para habilitar kernels Python específicos do ambiente. Isso garantirá o comportamento esperado de importação de kernel e pacote ao trabalhar com notebooks Jupyter em ambientes Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Em seguida, execute o seguinte comando para criar o kernel:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Use os seguintes comandos para instalar pacotes do:

    Esse comando instala o SDK de Azure Machine Learning base com blocos de anotações e `automl` extras. O `automl` extra é uma instalação grande e pode ser removido dos colchetes se você não pretende executar experimentos de aprendizado de máquina automatizados. O `automl` extra também inclui o Azure Machine Learning SDK de preparação de dados por padrão como uma dependência.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Se você receber uma mensagem informando que o PyYAML não pode ser desinstalado, use o seguinte comando:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A partir do macOS Catalina, o zsh (Z Shell) é o shell de logon padrão e o shell interativo. No zsh, use o comando a seguir que escapa os colchetes com "\\" (barra invertida):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Levará vários minutos para instalar o SDK. Para obter mais informações sobre as opções de instalação, consulte o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)de instalação.

1. Instale outros pacotes para sua experimentação do Machine Learning.

    Use qualquer um dos comandos a seguir e substitua *\<new package>* pelo pacote que você deseja instalar. A instalação de pacotes via `conda install` requer que o pacote faça parte dos canais atuais (novos canais podem ser adicionados no Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    Como alternativa, você pode instalar pacotes via `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação, em que você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. O Jupyter Notebook também é uma ótima maneira de compartilhar seus resultados com outras pessoas, já que é possível salvar a saída das seções de código no documento. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

O procedimento na seção [computador local](#local) instala os componentes necessários para executar blocos de anotações do Jupyter em um ambiente Anaconda.

Para habilitar esses componentes em seu ambiente de Jupyter Notebook:

1. Abra um prompt do Anaconda e ative seu ambiente.

    ```bash
    conda activate myenv
    ```

1. Clone [o repositório do GitHub](https://github.com/Azure/MachineLearningNotebooks) para um conjunto de blocos de anotações de exemplo.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de Jupyter Notebook com o seguinte comando:

    ```bash
    jupyter notebook
    ```

1. Para verificar se Jupyter Notebook pode usar o SDK, crie um **novo** bloco de anotações, selecione **Python 3** como kernel e, em seguida, execute o seguinte comando em uma célula do notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se você encontrar problemas ao importar módulos e receber um `ModuleNotFoundError` , verifique se o kernel do Jupyter está conectado ao caminho correto para o seu ambiente executando o seguinte código em uma célula do bloco de anotações.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o Jupyter Notebook para usar seu espaço de trabalho do Azure Machine Learning, vá para a seção [criar um arquivo de configuração do espaço de trabalho](#workspace) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code é um editor de código de plataforma cruzada muito popular que dá suporte a um amplo conjunto de linguagens de programação e ferramentas por meio de extensões disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instala a [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para codificação em todos os tipos de ambientes do Python (virtual, Anaconda, etc.). Além disso, ele fornece recursos de conveniência para trabalhar com Azure Machine Learning recursos e executar Azure Machine Learning experimentos sem sair Visual Studio Code.

Para usar Visual Studio Code para desenvolvimento:

1. Instale a extensão de Azure Machine Learning para Visual Studio Code, consulte [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, consulte [Usar o Azure Machine Learning para Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Saiba como usar Visual Studio Code para qualquer tipo de desenvolvimento em Python, consulte Introdução [ao Python no VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Para selecionar o ambiente Python do SDK que contém o SDK, abra VS Code e, em seguida, selecione CTRL + SHIFT + P (Linux e Windows) ou Command + Shift + P (Mac).
        - A __paleta de comandos__ é aberta.

    - Insira __Python: selecionar intérprete__e, em seguida, selecione o ambiente apropriado

1. Para validar que você pode usar o SDK, crie um novo arquivo Python (. py) que contenha o seguinte código:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Execute esse código clicando no CodeLens "executar célula" ou simplesmente pressione Shift-Enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks é um ambiente baseado em Apache Spark na nuvem do Azure. Ele fornece um ambiente de colaboração baseado em notebook com CPU ou cluster de computação baseado em GPU.

Como Azure Databricks funciona com Azure Machine Learning:
+ Você pode treinar um modelo usando o Spark MLlib e implantar o modelo em ACI/AKS de dentro Azure Databricks.
+ Você também pode usar recursos [automatizados de aprendizado de máquina](concept-automated-ml.md) em um SDK do Azure ml especial com Azure Databricks.
+ Você pode usar Azure Databricks como um destino de computação de um [pipeline de Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurar o cluster do databricks

Crie um [cluster do databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas configurações se aplicam somente se você instalar o SDK do Machine Learning automatizado no databricks.
**São necessários alguns minutos para criar o cluster.**

Use estas configurações:

| Setting |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |always| nomedoseucluster |
| Databricks Runtime |always|Tempo de execução não ML 6,5 (escala 2,11, Spark 2.4.3) |
| Versão do Python |always| 3 |
| Trabalhos |always| 2 ou superior |
| Tipos de VM do nó de trabalho <br>(determina o número máximo de iterações simultâneas) |ML automatizado<br>rápido| Uma VM otimizada para memória é preferível |
| Habilitar o dimensionamento automático |ML automatizado<br>rápido| Desmarcar |

Aguarde até que o cluster está em execução antes de continuar.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalar o SDK correto em uma biblioteca do databricks
Depois que o cluster estiver em execução, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote SDK do Azure Machine Learning apropriado ao cluster.

1. Clique com o botão direito do mouse na pasta do espaço de trabalho atual onde você deseja armazenar a biblioteca. Selecione **criar**  >  **biblioteca**.

1. Escolha **apenas uma** opção (não há suporte para nenhuma outra instalação do SDK)

   |&nbsp;Extras do pacote do SDK &nbsp;|Fonte|Nome do PyPi &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para databricks| Carregar Python Egg ou PyPI | azureml-sdk[databricks]|
   |Para databricks-com-<br> recursos de ML automatizados| Carregar Python Egg ou PyPI | `azureml-sdk[automl]`|

   > [!Warning]
   > Nenhum outro adicional do SDK pode ser instalado. Escolha apenas uma das opções anteriores [ `databricks` ] ou [ `automl` ].

   * Não selecione **anexar automaticamente a todos os clusters**.
   * Selecione  **anexar** ao lado do nome do cluster.

1. Monitorar erros até que o status seja alterado para **anexado**, o que pode levar vários minutos.  Se esta etapa falhar:

   Tente reiniciar o cluster da:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do cluster.
   1. Na guia **Bibliotecas**, selecione **Reiniciar**.

   Considere também:
   + Na configuração do AutoML, ao usar Azure Databricks adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations``` o é baseado no número de nós de trabalho no cluster.
        2. ```spark_context=sc``` baseia-se no contexto padrão do Spark.
   + Ou, se você tiver uma versão antiga do SDK, desmarque-a do bibliotecas instalado do cluster e mude para Trash. Instale a nova versão do SDK e reinicie o cluster. Se houver um problema após a reinicialização, desanexe e anexe novamente o cluster.

Se a instalação tiver sido bem-sucedida, a biblioteca importada deverá ser semelhante a uma destas:

SDK para databricks **_sem_** o machine learning ![ Azure Machine Learning SDK para databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para databricks **com** o SDK do Machine Learning automatizado ![ com o Machine Learning automatizado instalado no databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Comece a explorar

Experimente:
+ Embora muitos blocos de anotações de exemplo estejam disponíveis, **somente [esses blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com Azure Databricks.**

+ Importe esses exemplos diretamente do seu espaço de trabalho. Veja abaixo: ![ selecionar importar ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ painel importar](./media/how-to-configure-environment/azure-db-import.png)

+ Saiba como [criar um pipeline com o databricks como a computação de treinamento](how-to-create-your-first-pipeline.md).

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Criar um arquivo de configuração de espaço de trabalho

O arquivo de configuração do espaço de trabalho é um arquivo JSON que informa ao SDK como se comunicar com seu espaço de trabalho Azure Machine Learning. O arquivo é nomeado *config.json* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo JSON deve estar na estrutura de diretório que contém os scripts Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório denominado *.azureml* ou em um diretório pai.

Para usar esse arquivo de seu código, use `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e se conecta ao seu workspace.

É possível criar o arquivo de configuração de três maneiras:

* **Use  [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)**: para gravar um *config.jsno* arquivo. O arquivo contém as informações de configuração do workspace. É possível baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Baixe o arquivo**: no [portal do Azure](https://ms.portal.azure.com), selecione  **baixar config.jsna** seção **visão geral** do seu espaço de trabalho.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o arquivo programaticamente**: no trecho de código a seguir, você se conecta a um espaço de trabalho fornecendo a ID da assinatura, o grupo de recursos e o nome do espaço de trabalho. Em seguida, salva a configuração do workspace no arquivo:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Esse código grava o arquivo de configuração para o *. azureml/config.jsno* arquivo.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de MNIST
- Veja a referência do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)
