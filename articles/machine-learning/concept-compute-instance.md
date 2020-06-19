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
ms.date: 12/13/2019
ms.openlocfilehash: 8c03df8fb0cd8f5f092450ebe4c66266d2ff4293
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816345"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Uma instância de computação do Azure Machine Learning (versão prévia) é uma estação de trabalho baseada em nuvem totalmente gerenciada para cientistas de dados. 

As instâncias de computação facilitam a introdução ao desenvolvimento do Azure Machine Learning, além de fornecer funcionalidades de gerenciamento e preparação empresarial para os administradores de TI.  

Use uma instância de computação como seu ambiente de desenvolvimento totalmente configurado e gerenciado na nuvem.

As instâncias de computação normalmente são usadas como ambientes de desenvolvimento.  Elas também podem ser usadas como um destino de computação para treinamento e inferência para desenvolvimento e teste.  Para tarefas grandes, um [cluster de computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós são uma melhor opção de destino de computação.


## <a name="why-use-a-compute-instance"></a>Por que usar uma instância de computação?

Uma instância de computação é uma estação de trabalho totalmente gerenciada baseada em nuvem otimizada para seu ambiente de desenvolvimento de machine learning. Ele oferece os seguintes benefícios:

|Principais benefícios||
|----|----|
|Produtividade|Os cientistas de dados podem criar e implantar modelos usando notebooks integrados e as seguintes ferramentas em seu navegador da Web:<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|Gerenciado e seguro|Reduza o volume de segurança e adicione conformidade com os requisitos de segurança corporativa. As instâncias de computação fornecem políticas de gerenciamento robustas e configurações de rede seguras, como:<br/><br/>- Provisionamento automático de modelos do Resource Manager ou SDK do Azure Machine Learning<br/>- [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para habilitar/desabilitar o acesso SSH|
|pré-configurado&nbsp;ou&nbsp;ML|Economize tempo nas tarefas de instalação com pacotes ML pré-configurados e atualizados, estruturas de aprendizado profundo, drivers de GPU.|
|Totalmente personalizável|Amplo suporte para tipos de VM do Azure, incluindo GPUs e personalização de baixo nível persistente, como a instalação de pacotes e drivers, torna os cenários avançados muito fáceis. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

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
|RStudio Server Open Source Edition||
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

As instâncias de computação normalmente são usadas como ambientes de desenvolvimento.  Elas também podem ser usadas como um destino de computação para treinamento e inferência para desenvolvimento e teste.  Para tarefas grandes, um [cluster de computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós são uma melhor opção de destino de computação.

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

A conta de compartilhamento de arquivo do Azure do seu workspace é montada como uma unidade na instância de computação. Essa unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio.

Os arquivos no compartilhamento de arquivo podem ser acessados de todas as instâncias de computação no mesmo workspace. Todas as alterações a esses arquivos na instância de computação serão mantidas de modo confiável no compartilhamento de arquivo.

Você também pode clonar os exemplos mais recentes do Azure Machine Learning em sua pasta no diretório arquivos do usuário no compartilhamento de arquivo do workspace.

Gravar arquivos pequenos pode ser mais lento em unidades de rede do que gravar na própria VM.  Se você estiver escrevendo muitos arquivos pequenos, tente usar um diretório diretamente na instância de computação, como um diretório `/tmp`. Observe que esses arquivos não estarão acessíveis de outras instâncias de computação no workspace.

## <a name="managing-a-compute-instance"></a>Como gerenciar uma instância de computação

Em seu workspace no Azure Machine Learning Studio, selecione **Computação** e, em seguida, selecione **Instância de Computação** na parte superior.

![Gerenciar uma instância de computação](./media/concept-compute-instance/manage-compute-instance.png)

Você pode executar as seguintes ações:

* Crie uma instância de computação. Especifique o nome, o tipo de VM do Azure, incluindo GPUs (o tipo de VM não pode ser alterado após a criação), habilite/desabilite o acesso SSH e defina as configurações de rede virtual opcionalmente. Você também pode criar uma instância diretamente de notebooks integrados, portal do Azure, modelo do Resource Manager ou SDK do Azure Machine Learning. A cota de núcleos dedicados por região, que se aplica à criação da instância de computação é unificada e compartilhada com a cota de cluster de computação do Azure Machine Learning.
* Atualizar a guia instâncias de computação
* Inicie, pare e reinicie uma instância de computação. Interrompa a VM quando você não a estiver usando para reduzir o custo. Em seguida, inicie-o novamente quando precisar.
* Excluir uma instância de computação

Para cada instância de computação em seu workspace, você pode:

* Acessar o Jupyter, JupyterLab, RStudio na instância de computação
* Efetuar SSH na instância de computação. O acesso SSH é desabilitado por padrão, mas pode ser habilitado no momento da criação da instância de computação. O acesso SSH é por meio do mecanismo de chave pública/privada. A guia fornecerá detalhes para a conexão SSH, como endereço IP, nome de usuário e número da porta.
* Obtenha detalhes sobre uma instância de computação específica, como endereço IP e região.

O [RBAC](/azure/role-based-access-control/overview) permite controlar quais usuários no workspace podem criar, excluir, iniciar, parar, reiniciar uma instância de computação. Todos os usuários na função colaborador e proprietário do workspace podem criar, excluir, iniciar, parar e reiniciar instâncias de computação no workspace. No entanto, somente o criador de uma instância de computação específica tem permissão para acessar o Jupyter, o JupyterLab e o RStudio nessa instância de computação. O criador da instância de computação tem a instância de computação dedicada a ela, tem acesso à raiz e pode acessar o terminal por meio do Jupyter. A instância de computação terá logon de usuário único do usuário criador e todas as ações usarão a identidade desse usuário para o RBAC e a atribuição de execuções de experimento. O acesso SSH é controlado por meio do mecanismo de chave pública/privada.

Você também pode criar uma instância
* Diretamente da experiência de notebooks integrados
* No Portal do Azure
* No modelo do Azure Resource Manager
* Com o SDK do Azure Machine Learning

A cota de núcleos dedicados por região, que se aplica à criação da instância de computação é unificada e compartilhada com a cota de cluster de treinamento do Azure Machine Learning. 

## <a name="compute-target"></a>Destino de computação

As instâncias de computação podem ser usadas como um [destino de computação de treinamento](concept-compute-target.md#train) semelhante a clusters de treinamento de computação do Azure Machine Learning. Provisione uma VM de várias GPUs para executar trabalhos de treinamento distribuídos usando os estimadores TensorFlow/PyTorch. Você também pode criar uma configuração de execução e usá-la para executar seu experimento na instância de computação. Você pode usar a instância de computação como um destino de implantação de inferência local para cenários de teste/depuração.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com a VM do Notebook?

As instâncias de computação estão substituindo a VM do Notebook.  

Todos os arquivos de notebook armazenados no compartilhamento de arquivo do workspace e dados em armazenamentos de dados de workspace estarão acessíveis de uma instância de computação. No entanto, todos os pacotes personalizados instalados anteriormente em uma VM do Notebook precisarão ser instalados novamente na instância de computação. As limitações de cota que se aplicam à criação de clusters de computação também serão aplicadas à criação da instância de computação. 

Não é possível criar novas VMs de Notebook. No entanto, você ainda pode acessar e usar as VMs de Notebook que você criou com funcionalidade completa. As instâncias de computação podem ser criadas no mesmo workspace que as VMs de Notebook existentes. 


## <a name="next-steps"></a>Próximas etapas

 * [Tutorial: Treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.
