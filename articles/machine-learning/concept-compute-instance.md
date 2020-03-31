---
title: O que é uma instância de cálculo de machine learning do Azure?
titleSuffix: Azure Machine Learning
description: Conheça a instância de computação do Azure Machine Learning, uma estação de trabalho totalmente gerenciada em nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283921"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de cálculo de machine learning do Azure?

Uma instância de computação de aprendizado de máquina do Azure (preview) é uma estação de trabalho totalmente gerenciada baseada em nuvem para cientistas de dados. 

As instâncias computacionais facilitam o início do desenvolvimento do Azure Machine Learning, bem como fornecem recursos de gerenciamento e prontidão corporativa para os administradores de TI.  

Use uma instância de computação como seu ambiente de desenvolvimento totalmente configurado e gerenciado na nuvem.

As instâncias computacionais são normalmente usadas como ambientes de desenvolvimento.  Eles também podem ser usados como um alvo de computação para treinamento e inferência para desenvolvimento e testes.  Para tarefas grandes, um [cluster de computação azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós é uma melhor escolha de destino computacional.


## <a name="why-use-a-compute-instance"></a>Por que usar uma instância computacional?

Uma instância de computação é uma estação de trabalho totalmente gerenciada baseada em nuvem otimizada para o seu ambiente de desenvolvimento de aprendizado de máquina. Ele oferece os seguintes benefícios:

|Principais benefícios||
|----|----|
|Produtividade|Os cientistas de dados podem construir e implantar modelos usando notebooks integrados e as seguintes ferramentas em seu navegador:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio|
|Gerenciado & seguro|Reduza sua pegada de segurança e adicione conformidade com os requisitos de segurança da empresa. As instâncias computacionais fornecem políticas de gerenciamento robustas e configurações seguras de rede, tais como:<br/><br/>- Provisionamento automático de modelos do Gerenciador de Recursos ou SDK de aprendizado de máquina do Azure<br/>- [RBAC (Role-based Access Control, controle de acesso baseado em função)](/azure/role-based-access-control/overview)<br/>- [Suporte à rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para ativar/desativar acesso SSH|
|&nbsp;Pré-configurado&nbsp;ou ML|Economize tempo em tarefas de configuração com pacotes ML pré-configurados e atualizados, frameworks de aprendizagem profunda, drivers de GPU.|
|Totalmente personalizável|O amplo suporte para tipos de VM do Azure, incluindo GPUs e personalização de baixo nível, como a instalação de pacotes e drivers, torna os cenários avançados uma brisa. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

A instância de computação do Azure Machine Learning permite que você seja autor, treine e implante modelos em uma experiência de notebook totalmente integrada em seu espaço de trabalho.


Essas ferramentas e ambientes são instalados na instância da computação: 

|Ferramentas gerais & ambientes|Detalhes|
|----|:----:|
|Drivers|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteca Intel MPI||
|CLI do Azure ||
|Amostras de Aprendizado de Máquina do Azure ||
|Motor EDAT de aprendizagem de máquina azul ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**Ferramentas R** & ambientes|Detalhes|
|----|:----:|
|RStudio Server Open Source Edition||
|Núcleo R||
|Azure Machine Learning SDK para R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Amostras do SDK|

|**Ferramentas PYTHON** & ambientes|Detalhes|
|----|----|
|Anaconda Python||
|Jupyter e extensões||
|Jupyterlab e extensões||
|Visual Studio Code ||
[Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>da PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Outros pacotes PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizagem profunda|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & amostras de SDK R||

Os pacotes Python estão todos instalados no ambiente **Python 3.6 - AzureML.**  

As instâncias computacionais são normalmente usadas como ambientes de desenvolvimento.  Eles também podem ser usados como um alvo de computação para treinamento e inferência para desenvolvimento e testes.  Para tarefas grandes, um [cluster de computação azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós é uma melhor escolha de destino computacional.

### <a name="installing-packages"></a>Instalar pacotes

Você pode instalar pacotes diretamente em um notebook Jupyter ou Rstudio:

* RStudio Use a guia **Pacotes** no canto inferior direito ou a guia **Console** no canto superior esquerdo.  
* Python: Adicione código de instalação e execute em uma célula de notebook Jupyter.

Ou você pode acessar uma janela de terminal de qualquer uma dessas maneiras:

* RStudio: Selecione a guia **Terminal** no canto superior esquerdo.
* Jupyter Lab: Selecione o azulejo **terminal** a **outra** posição na guia Lançador.
* Jupyter: Selecione **Novo terminal de>** na parte superior direita na guia Arquivos.
* SSH para a máquina.  Em seguida, instale pacotes Python no ambiente **Python 3.6 - AzureML.**  Instale pacotes R no ambiente **R.**

## <a name="accessing-files"></a>Acessar arquivos

Os scripts notebooks e R são armazenados na conta de armazenamento padrão do seu espaço de trabalho no compartilhamento de arquivos Do Azure.  Esses arquivos estão localizados no diretório "Arquivos do usuário". Esse armazenamento facilita o compartilhamento de notebooks entre instâncias computacionais. A conta de armazenamento também mantém seus notebooks preservados com segurança quando você para ou exclui uma instância de computação.

A conta de compartilhamento de arquivos do Azure do seu espaço de trabalho é montada como uma unidade na instância de computação. Esta unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio.

Os arquivos no compartilhamento de arquivos são acessíveis de todas as instâncias de computação no mesmo espaço de trabalho. Quaisquer alterações nesses arquivos na instância de computação serão confiáveis de volta ao compartilhamento de arquivos.

Você também pode clonar as amostras mais recentes do Azure Machine Learning para sua pasta o diretório de arquivos do usuário no compartilhamento de arquivos do espaço de trabalho.

Escrever pequenos arquivos pode ser mais lento em unidades de rede do que escrever para a própria VM.  Se você estiver escrevendo muitos arquivos pequenos, tente usar um diretório `/tmp` diretamente na instância de computação, como um diretório. Observe que esses arquivos não estarão acessíveis a partir de outras instâncias de computação no espaço de trabalho.

## <a name="managing-a-compute-instance"></a>Gerenciamento de uma instância de computação

Em seu espaço de trabalho no estúdio Azure Machine Learning, **selecione Computação**e **selecione 'Exemplo de Computação'** na parte superior.

![Gerenciar uma instância de computação](./media/concept-compute-instance/manage-compute-instance.png)

Você pode executar as seguintes ações:

* Criar uma instância de computação. Especifique o nome, tipo Azure VM incluindo GPUs (por favor, note que o tipo vm não pode ser alterado após a criação), habilite/desative o acesso ao SSH e configure configurações de rede virtual opcionalmente. Você também pode criar uma instância diretamente a partir de notebooks integrados, portal Azure, modelo de Gerenciador de Recursos ou SDK de Aprendizado de Máquina do Azure. A cota dedicada de núcleos por região que se aplica à criação de instâncias de computação é unificada e compartilhada com a cota de cluster de computação do Azure Machine Learning.
* Atualize a guia instâncias de computação
* Inicie, pare e reinicie uma instância de computação
* Exclua uma instância de computação

Para cada instância de computação em seu espaço de trabalho você pode:

* Acesse Jupyter, JupyterLab, RStudio na instância de computação
* SSH em instância de computação. O acesso ao SSH é desativado por padrão, mas pode ser ativado no tempo de criação da instância de computação. O acesso ao SSH é através de mecanismo de chave público/privado. A guia lhe dará detalhes para a conexão SSH, como endereço IP, nome de usuário e número da porta.
* Obtenha detalhes sobre uma instância de computação específica, como endereço IP e região.

[O RBAC](/azure/role-based-access-control/overview) permite controlar quais usuários no espaço de trabalho podem criar, excluir, iniciar, parar, reiniciar uma instância de computação. Todos os usuários na função de contribuinte e proprietário do espaço de trabalho podem criar, excluir, iniciar, parar e reiniciar instâncias de computação em todo o espaço de trabalho. No entanto, apenas o criador de uma instância específica de computação pode acessar jupyter, JupyterLab e RStudio nessa instância de computação. O criador da instância de computação tem a instância de computação dedicada a eles, tem acesso raiz e pode entrar através do Jupyter. A instância de cálculo terá o login de um usuário único do usuário criador e todas as ações usarão a identidade desse usuário para RBAC e atribuição de corridas de experimentos. O acesso ssh é controlado através de mecanismo de chave público/privado.

Você também pode criar uma instância
* Diretamente da experiência dos notebooks integrados
* No Portal do Azure
* Do modelo do Azure Resource Manager
* Com Azure Machine Learning SDK

A cota de núcleos dedicados por região, que se aplica à criação de instâncias de computação, é unificada e compartilhada com a cota de cluster de treinamento de Machine Learning do Azure. 

## <a name="compute-target"></a>Destino de computação

As instâncias de computação podem ser usadas como um [alvo de computação](concept-compute-target.md#train) de treinamento semelhante aos clusters de treinamento de computação do Azure Machine Learning. Provisionar um VM multi-GPU para executar trabalhos de treinamento distribuídos usando estimadores TensorFlow/PyTorch. Você também pode criar uma configuração de execução e usá-la para executar seu experimento na instância de computação. Você pode usar a instância da computação como um alvo de implantação de inferência local para cenários de teste/depuração.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com o Notebook VM?

As instâncias de cálculo estão substituindo a VM do Notebook.  

Quaisquer arquivos de notebook armazenados no compartilhamento de arquivos do espaço de trabalho e dados em armazenamentos de dados do espaço de trabalho estarão acessíveis a partir de uma instância computacional. No entanto, todos os pacotes personalizados previamente instalados em uma VM notebook precisarão ser reinstalados na instância de computação. As limitações de cotas aplicáveis à criação de clusters de computação também se aplicam à criação de instâncias de computação. 

Novas VMs de notebook não podem ser criadas. No entanto, você ainda pode acessar e usar VMs notebook que você criou, com funcionalidade completa. As instâncias de cálculo podem ser criadas no mesmo espaço de trabalho que as VMs de notebook existentes. 


## <a name="next-steps"></a>Próximas etapas

 * [Tutorial: Treinar seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.
