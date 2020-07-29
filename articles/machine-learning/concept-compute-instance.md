---
title: O que é uma instância de computação do Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a instância de computação do Azure Machine Learning, uma estação de trabalho totalmente gerenciada baseada em nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 4ac95fa81fdbee237cacaa1541e333bb70c370fa
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323291"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Uma instância de computação Azure Machine Learning é uma estação de trabalho gerenciada baseada em nuvem para cientistas de dados.

As instâncias de computação facilitam a introdução ao desenvolvimento do Azure Machine Learning, além de fornecer funcionalidades de gerenciamento e preparação empresarial para os administradores de TI.  

Use uma instância de computação como seu ambiente de desenvolvimento totalmente configurado e gerenciado na nuvem para o aprendizado de máquina. Eles também podem ser usados como um destino de computação para treinamento e inferência para fins de desenvolvimento e teste.  

Para treinamento de modelo de nível de produção, use um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós. Para a implantação do modelo de nível de produção, use o [cluster do serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Por que usar uma instância de computação?

Uma instância de computação é uma estação de trabalho totalmente gerenciada baseada em nuvem otimizada para seu ambiente de desenvolvimento de machine learning. Ele oferece os seguintes benefícios:

|Principais benefícios|Descrição|
|----|----|
|Produtividade|Você pode criar e implantar modelos usando blocos de anotações integrados e as seguintes ferramentas no Azure Machine Learning Studio:<br/>-  Jupyter<br/>-  JupyterLab<br/>-RStudio (versão prévia)<br/>A instância de computação é totalmente integrada com o Azure Machine Learning Workspace e o Studio. Você pode compartilhar blocos de anotações e dados com outros cientistas de dados no espaço de trabalho. Você também pode configurar VS Code desenvolvimento remoto usando [SSH](how-to-set-up-vs-code-remote.md) |
|Gerenciado e seguro|Reduza o volume de segurança e adicione conformidade com os requisitos de segurança corporativa. As instâncias de computação fornecem políticas de gerenciamento robustas e configurações de rede seguras, como:<br/><br/>- Provisionamento automático de modelos do Resource Manager ou SDK do Azure Machine Learning<br/>- [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para habilitar/desabilitar o acesso SSH<br/>TLS 1,2 habilitado |
|Pré-configurado &nbsp; para &nbsp; ml|Economize tempo nas tarefas de instalação com pacotes ML pré-configurados e atualizados, estruturas de aprendizado profundo, drivers de GPU.|
|Totalmente personalizável|Amplo suporte para tipos de VM do Azure, incluindo GPUs e personalização de baixo nível persistente, como a instalação de pacotes e drivers, torna os cenários avançados muito fáceis. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

> [!IMPORTANT]
> As ferramentas marcadas (visualização) abaixo estão atualmente em visualização pública.
> A versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A instância de computação do Azure Machine Learning permite que você crie, treine e implante modelos em uma experiência de notebook totalmente integrada em seu workspace.

Essas ferramentas e esses ambientes são instalados na instância de computação: 

|Ferramentas gerais e ambientes|Detalhes|
|----|:----:|
|Drivers|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteca Intel MPI||
|CLI do Azure ||
|Exemplos do Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|Ambientes e Ferramentas do **R**|Detalhes|
|----|:----:|
|RStudio Server Open Source Edition (versão prévia)||
|Kernel do R||
|SDK do Azure Machine Learning para R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Amostras do SDK|

|Ferramentas e ambientes do **PYTHON**|Detalhes|
|----|----|
|Anaconda Python||
|Jupyter e extensões||
|Jupyterlab e extensões||
[SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>do PyPI|Inclui a maioria dos pacotes extras do azureml.  Para ver a lista completa, [abra uma janela de terminal em sua instância de computação](how-to-run-jupyter-notebooks.md#terminal) e execute <br/> `conda list -n azureml_py36 azureml*` |
|Outros pacotes do PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizado profundo|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemplos do Python e R e de SDK do Azure Machine Learning||

Os pacotes do Python são todos instalados no ambiente **Python 3.6 – AzureML**.  

### <a name="installing-packages"></a>Instalar pacotes

Você pode instalar pacotes diretamente em um Jupyter notebook ou RStudio:

* RStudio Use a guia **Pacotes** no canto inferior direito ou a guia **Console** no canto superior esquerdo.  
* Python: Adicione o código de instalação e execute em uma célula do Jupyter notebook.

Outra opção é acessar uma janela de terminal de qualquer uma das seguintes maneiras:

* RStudio: Selecione a guia **Terminal** no canto superior esquerdo.
* Jupyter Lab:  selecione o bloco **Terminal** no cabeçalho **Outro** na guia Iniciador.
* Jupyter:  Selecione **Novo > Terminal** no canto superior direito na guia Arquivos.
* SSH para o computador.  Em seguida, instale os pacotes do Python no ambiente **Python 3.6 – AzureML**.  Instale os pacotes do R no ambiente do **R**.

## <a name="accessing-files"></a>Acessar arquivos

Os scripts do R e Notebooks são armazenados na conta de armazenamento padrão do seu workspace no compartilhamento de arquivo do Azure.  Esses arquivos estão localizados no diretório "Arquivos de usuário". Esse armazenamento facilita o compartilhamento de notebooks entre instâncias de computação. A conta de armazenamento também mantém seus notebooks preservados com segurança quando você interrompe ou exclui uma instância de computação.

A conta de compartilhamento de arquivo do Azure do seu workspace é montada como uma unidade na instância de computação. Essa unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio. Isso significa que os blocos de anotações e outros arquivos criados em Jupyter, JupyterLab ou RStudio são automaticamente armazenados no compartilhamento de arquivos e também disponíveis para uso em outras instâncias de computação.

Os arquivos no compartilhamento de arquivo podem ser acessados de todas as instâncias de computação no mesmo workspace. Todas as alterações a esses arquivos na instância de computação serão mantidas de modo confiável no compartilhamento de arquivo.

Você também pode clonar os exemplos mais recentes do Azure Machine Learning em sua pasta no diretório arquivos do usuário no compartilhamento de arquivo do workspace.

Gravar arquivos pequenos pode ser mais lento em unidades de rede do que gravar no próprio disco local da instância de computação.  Se você estiver escrevendo muitos arquivos pequenos, tente usar um diretório diretamente na instância de computação, como um diretório `/tmp`. Observe que esses arquivos não estarão acessíveis de outras instâncias de computação. 

Você pode usar o `/tmp` diretório na instância de computação para seus dados temporários.  No entanto, não grave arquivos grandes de dados no disco do sistema operacional da instância de computação.  Em vez disso, use os [repositórios de armazenamento](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Se você instalou a extensão git do JupyterLab, ela também pode levar à lentidão no desempenho da instância de computação.

## <a name="managing-a-compute-instance"></a>Como gerenciar uma instância de computação

Em seu workspace no Azure Machine Learning Studio, selecione **Computação** e, em seguida, selecione **Instância de Computação** na parte superior.

![Gerenciar uma instância de computação](./media/concept-compute-instance/manage-compute-instance.png)

Você pode executar as seguintes ações:

* [Crie uma instância de computação](#create). 
* Atualize a guia instâncias de computação.
* Inicie, pare e reinicie uma instância de computação.  Você paga pela instância sempre que ela estiver em execução. Pare a instância de computação quando você não a estiver usando para reduzir o custo. Parar uma instância de computação a desaloca. Em seguida, inicie-o novamente quando precisar. 
* Excluir uma instância de computação.
* Filtre a lista de instâncias de computação para aquelas que você criou.  Essas são as instâncias de computação que você pode acessar.

Para cada instância de computação em seu espaço de trabalho ao qual você tem acesso, você pode:

* Acessar o Jupyter, JupyterLab, RStudio na instância de computação
* Efetuar SSH na instância de computação. O acesso SSH é desabilitado por padrão, mas pode ser habilitado no momento da criação da instância de computação. O acesso SSH é por meio do mecanismo de chave pública/privada. A guia fornecerá detalhes para a conexão SSH, como endereço IP, nome de usuário e número da porta.
* Obtenha detalhes sobre uma instância de computação específica, como endereço IP e região.

O [RBAC](/azure/role-based-access-control/overview) permite controlar quais usuários no workspace podem criar, excluir, iniciar, parar, reiniciar uma instância de computação. Todos os usuários na função colaborador e proprietário do workspace podem criar, excluir, iniciar, parar e reiniciar instâncias de computação no workspace. No entanto, somente o criador de uma instância de computação específica tem permissão para acessar o Jupyter, o JupyterLab e o RStudio nessa instância de computação. O criador da instância de computação tem a instância de computação dedicada a elas, tem acesso à raiz e pode acessar o terminal por meio de Jupyter/JupyterLab/RStudio. A instância de computação terá logon de usuário único do usuário criador e todas as ações usarão a identidade desse usuário para o RBAC e a atribuição de execuções de experimento. O acesso SSH é controlado por meio do mecanismo de chave pública/privada.

Essas ações podem ser controladas pelo RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft. MachineLearningServices/espaços de trabalho/computações/início/ação*
* *Microsoft. MachineLearningServices/espaços de trabalho/computações/parada/ação*
* *Microsoft. MachineLearningServices/espaços de trabalho/computações/reinicialização/ação*

### <a name="create-a-compute-instance"></a><a name="create"></a>Criar uma instância de computação

Em seu espaço de trabalho no Azure Machine Learning Studio, crie uma nova instância de computação na seção **computação** ou na seção **blocos de anotações** quando estiver pronto para executar um dos seus blocos de anotações.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Criar uma nova instância de computação":::


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é obrigatório e deve ter entre 3 e 24 caracteres.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o **-** caractere.</li><li>O nome deve começar com uma letra</li><li>O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo</li><li>Se o **-** caractere for usado, ele precisará ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual com suporte podem ser restritos em sua região. Verificar a [lista de disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Habilitar/desabilitar o acesso SSH     |   O acesso SSH é desabilitado por padrão.  O acesso SSH não pode ser. alterado após a criação. Certifique-se de habilitar o acesso se você planeja depurar interativamente com [vs Code remoto](how-to-set-up-vs-code-remote.md)   |
|Configurações avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de recursos**, a **rede virtual**e a **sub-rede** para criar a instância de computação dentro de uma vnet (rede virtual) do Azure. Para obter mais informações, consulte estes [requisitos de rede](how-to-enable-virtual-network.md#compute-instance) para vnet.        |

Você também pode criar uma instância
* Diretamente da [experiência de notebooks integrados](tutorial-1st-experiment-sdk-setup.md#azure)
* No Portal do Azure
* Do modelo de Azure Resource Manager. Para obter um modelo de exemplo, consulte [criar um Azure Machine Learning modelo de instância de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Com [Azure Machine Learning SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb)
* Da [extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Os núcleos dedicados por região por cota de família de VM e cota regional total, que se aplica à criação da instância de computação. é unificado e compartilhado com Azure Machine Learning cota de cluster de computação de treinamento. Parar a instância de computação não libera a cota para garantir que você poderá reiniciar a instância de computação.

## <a name="compute-target"></a>Destino de computação

As instâncias de computação podem ser usadas como um [destino de computação de treinamento](concept-compute-target.md#train) semelhante a clusters de treinamento de computação do Azure Machine Learning. 

Uma instância de computação:
* Tem uma fila de trabalhos.
* Executa trabalhos com segurança em um ambiente de rede virtual, sem exigir que as empresas Abram a porta SSH. O trabalho é executado em um ambiente em contêiner e empacota suas dependências de modelo em um contêiner do Docker.
* Pode executar vários trabalhos pequenos em paralelo (visualização).  Dois trabalhos por núcleo podem ser executados em paralelo enquanto o restante dos trabalhos são enfileirados.

Você pode usar a instância de computação como um destino de implantação de inferência local para cenários de teste/depuração.

> [!NOTE]
> Não há suporte para trabalhos de treinamento distribuídos na instância de computação.  Use (clusters de computação] (como fazer a configuração-treinamento-targets. MD # amlcompute) para treinamento distribuído.

Para obter mais detalhes, consulte o notebook [Train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Este notebook também está disponível na pasta de **exemplos** do Studio em *Training/Train-on-computeinstance*.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com a VM do Notebook?

As instâncias de computação estão substituindo a VM do Notebook.  

Todos os arquivos de notebook armazenados no compartilhamento de arquivo do workspace e dados em armazenamentos de dados de workspace estarão acessíveis de uma instância de computação. No entanto, todos os pacotes personalizados instalados anteriormente em uma VM do Notebook precisarão ser instalados novamente na instância de computação. As limitações de cota que se aplicam à criação de clusters de computação também serão aplicadas à criação da instância de computação.

Não é possível criar novas VMs de Notebook. No entanto, você ainda pode acessar e usar as VMs de Notebook que você criou com funcionalidade completa. As instâncias de computação podem ser criadas no mesmo workspace que as VMs de Notebook existentes.


## <a name="next-steps"></a>Próximas etapas

 * [Tutorial: Treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.
