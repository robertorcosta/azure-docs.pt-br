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
ms.date: 10/02/2020
ms.openlocfilehash: 68143d3ee5df6dca29c43cb090f5873c4b50060f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704683"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Uma instância de computação Azure Machine Learning é uma estação de trabalho gerenciada baseada em nuvem para cientistas de dados.

As instâncias de computação facilitam a introdução ao desenvolvimento do Azure Machine Learning, além de fornecer funcionalidades de gerenciamento e preparação empresarial para os administradores de TI.  

Use uma instância de computação como seu ambiente de desenvolvimento totalmente configurado e gerenciado na nuvem para o aprendizado de máquina. Eles também podem ser usados como um destino de computação para treinamento e inferência para fins de desenvolvimento e teste.  

Para treinamento de modelo de nível de produção, use um [cluster de computação Azure Machine Learning](how-to-create-attach-compute-cluster.md) com recursos de dimensionamento de vários nós. Para a implantação do modelo de nível de produção, use o [cluster do serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Por que usar uma instância de computação?

Uma instância de computação é uma estação de trabalho totalmente gerenciada baseada em nuvem otimizada para seu ambiente de desenvolvimento do Machine Learning. Ele oferece os seguintes benefícios:

|Principais benefícios|Descrição|
|----|----|
|Produtividade|Você pode criar e implantar modelos usando blocos de anotações integrados e as seguintes ferramentas no Azure Machine Learning Studio:<br/>-  Jupyter<br/>-  JupyterLab<br/>-RStudio (versão prévia)<br/>A instância de computação é totalmente integrada com o Azure Machine Learning Workspace e o Studio. Você pode compartilhar blocos de anotações e dados com outros cientistas de dados no espaço de trabalho. Você também pode configurar o desenvolvimento de VS Code remoto usando [SSH](how-to-set-up-vs-code-remote.md) |
|Gerenciado e seguro|Reduza o volume de segurança e adicione conformidade com os requisitos de segurança corporativa. As instâncias de computação fornecem políticas de gerenciamento robustas e configurações de rede seguras, como:<br/><br/>-Provisionamento automático de modelos do Resource Manager ou SDK do Azure Machine Learning<br/>- [Controle de acesso baseado em função do Azure (RBAC do Azure)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para habilitar/desabilitar o acesso SSH<br/>TLS 1,2 habilitado |
|Pré-configurado &nbsp; para &nbsp; ml|Economize tempo nas tarefas de instalação com pacotes ML pré-configurados e atualizados, estruturas de aprendizado profundo, drivers de GPU.|
|Totalmente personalizável|Amplo suporte para tipos de VM do Azure, incluindo GPUs e personalização de baixo nível persistente, como a instalação de pacotes e drivers, torna os cenários avançados muito fáceis. |

Você mesmo pode [criar uma instância de computação](how-to-create-manage-compute-instance.md?tabs=python#create) ou um administrador pode [criar uma instância de computação para você](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

> [!IMPORTANT]
> Os itens marcados (visualização) neste artigo estão atualmente em visualização pública.
> A versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A instância de computação do Azure Machine Learning permite que você crie, treine e implante modelos em uma experiência de notebook totalmente integrada em seu workspace.

Você pode [instalar pacotes](how-to-create-manage-compute-instance.md#install-packages) e [Adicionar kernels](how-to-create-manage-compute-instance.md#add-new-kernels) à sua instância de computação.  

Essas ferramentas e ambientes já estão instalados na instância de computação: 

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
[SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)</br>do PyPI|Inclui a maioria dos pacotes extras do azureml.  Para ver a lista completa, [abra uma janela de terminal em sua instância de computação](how-to-run-jupyter-notebooks.md#terminal) e execute <br/> `conda list -n azureml_py36 azureml*` |
|Outros pacotes do PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizado profundo|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemplos do Python e R e de SDK do Azure Machine Learning||

Os pacotes do Python são todos instalados no ambiente **Python 3.6 – AzureML**.  

## <a name="accessing-files"></a>Acessar arquivos

Os scripts do R e Notebooks são armazenados na conta de armazenamento padrão do seu workspace no compartilhamento de arquivo do Azure.  Esses arquivos estão localizados no diretório "Arquivos de usuário". Esse armazenamento facilita o compartilhamento de notebooks entre instâncias de computação. A conta de armazenamento também mantém seus notebooks preservados com segurança quando você interrompe ou exclui uma instância de computação.

A conta de compartilhamento de arquivo do Azure do seu workspace é montada como uma unidade na instância de computação. Essa unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio. Isso significa que os blocos de anotações e outros arquivos criados em Jupyter, JupyterLab ou RStudio são automaticamente armazenados no compartilhamento de arquivos e também disponíveis para uso em outras instâncias de computação.

Os arquivos no compartilhamento de arquivo podem ser acessados de todas as instâncias de computação no mesmo workspace. Todas as alterações a esses arquivos na instância de computação serão mantidas de modo confiável no compartilhamento de arquivo.

Você também pode clonar os exemplos mais recentes do Azure Machine Learning em sua pasta no diretório arquivos do usuário no compartilhamento de arquivo do workspace.

Gravar arquivos pequenos pode ser mais lento em unidades de rede do que gravar no próprio disco local da instância de computação.  Se você estiver escrevendo muitos arquivos pequenos, tente usar um diretório diretamente na instância de computação, como um diretório `/tmp`. Observe que esses arquivos não estarão acessíveis de outras instâncias de computação. 

Você pode usar o `/tmp` diretório na instância de computação para seus dados temporários.  No entanto, não grave arquivos grandes de dados no disco do sistema operacional da instância de computação.  Em vez disso, use os [repositórios de armazenamento](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Se você tiver instalado a extensão git do JupyterLab, ele também poderá levar a uma lentidão no desempenho da instância de computação.

## <a name="compute-target"></a>Destino de computação

As instâncias de computação podem ser usadas como um [destino de computação de treinamento](concept-compute-target.md#train) semelhante a clusters de treinamento de computação do Azure Machine Learning. 

Uma instância de computação:
* Tem uma fila de trabalhos.
* Executa trabalhos com segurança em um ambiente de rede virtual, sem exigir que as empresas Abram a porta SSH. O trabalho é executado em um ambiente em contêiner e empacota suas dependências de modelo em um contêiner do Docker.
* Pode executar vários trabalhos pequenos em paralelo (visualização).  Dois trabalhos por núcleo podem ser executados em paralelo enquanto o restante dos trabalhos são enfileirados.
* Dá suporte a trabalhos de treinamento distribuído de várias GPU de nó único

Você pode usar a instância de computação como um destino de implantação de inferência local para cenários de teste/depuração.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com a VM do Notebook?

As instâncias de computação estão substituindo a VM do Notebook.  

Todos os arquivos de notebook armazenados no compartilhamento de arquivo do workspace e dados em armazenamentos de dados de workspace estarão acessíveis de uma instância de computação. No entanto, todos os pacotes personalizados instalados anteriormente em uma VM do notebook precisarão ser reinstalados na instância de computação. As limitações de cota, que se aplicam à criação de clusters de computação, também serão aplicadas à criação da instância de computação.

Não é possível criar novas VMs de Notebook. No entanto, você ainda pode acessar e usar as VMs de Notebook que você criou com funcionalidade completa. As instâncias de computação podem ser criadas no mesmo workspace que as VMs de Notebook existentes.


## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar uma instância de computação](how-to-create-manage-compute-instance.md)
* [Tutorial: Treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.
