---
title: Deep Learning & estruturas de IA
titleSuffix: Azure Data Science Virtual Machine
description: Estruturas e ferramentas de deep learning disponíveis na Máquina Virtual de Data Science do Azure.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270063"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning e frameworks de IA para o Azure Data Science VM
As estruturas de aprendizagem profunda no DSVM estão listadas abaixo.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado/instalado no DSVM?  | O Caffe é instalado em `/opt/caffe`.   As amostras `/opt/caffe/examples`estão em .|
| Como executá-lo      | use X2Go para fazer login na sua VM e, em seguida, inicie um novo terminal e digite o seguinte:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Uma nova janela do navegador é aberta com blocos de anotações de exemplo. Binários são instalados em /opt/caffe/build/install/bin.<br/><br/>A versão instalada do Caffe requer Python 2.7 e não funcionará com o Python 3.5, que é ativado por padrão. Para mudar para Python 2.7, execute `source activate root` para mudar para o ambiente Anaconda.|    

## <a name="caffe2"></a>[Cafeína2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado/instalado no DSVM?  | O cafeína2 está instalado no ambiente conda [Python 2.7 (raiz] . |
| Como executá-lo      | Terminal: Inicie python e importe Caffe2. <br/> * JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, vá ao diretório Caffe2 para encontrar notebooks de amostra. Alguns blocos de anotações exigem que a raiz Caffe2 seja definida no código Python; insira /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 5.2 |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado/instalado no DSVM?  | O Chainer é instalado em Python 3.5. |
| Como executá-lo      | Terminal: Ative o ambiente Python `python`3.5, execute e, em seguida. `import chainer` <br/> * JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, vá ao diretório Chainer para encontrar notebooks de exemplo.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, driver NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 10.0.130|
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado/instalado no DSVM?  |_nvidia-smi_ está disponível no caminho do sistema.  |
| Como executá-lo      | Abra um prompt de comando (no Windows) ou um terminal (no Linux) e execute _o nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 0.16.1|
| Edições DSVM suportadas      | Linux (Ubuntu)   |
| Como é configurado/instalado no DSVM?  | Horovod é instalado em Python 3.5 |
| Como executá-lo      | Ative o ambiente correto no terminal e execute python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 2.2.4 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado/instalado no DSVM?  | Keras é instalado no Python 3.6 no Windows e no Python 3.5 no Linux |
| Como executá-lo      | Ative o ambiente correto no terminal e execute python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[CNTK (Microsoft Cognitive Toolkit)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 2.5.1 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado/instalado no DSVM?  | CNTK é instalado em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute o Python. <br/>Jupyter: Conecte-se ao [Jupyter](provision-vm.md) ou [ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e abra o diretório CNTK para amostras. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.3.0 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado/instalado no DSVM?  | O MXNet está `C:\dsvm\tools\mxnet` instalado `/dsvm/tools/mxnet` no Windows e no Ubuntu. As ligações Python são instaladas no Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e no Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) As ligações R também estão incluídas no DSVM do Ubuntu. |
| Como executá-lo      | Terminal: Ative o ambiente conda correto e execute `import mxnet`. <br/>Jupyter: Conecte-se ao [Jupyter](provision-vm.md#access-the-dsvm) ou ao `mxnet` [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e abra o diretório para amostras. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.0.1 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado/instalado no DSVM?  | MXNet Model Server é instalado em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: `sudo systemctl stop jupyterhub` Corra para parar o serviço JupyterHub primeiro, porque ambos ouvem na mesma porta. Em seguida, ative o ambiente conda correto e execute`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada |  |
| Edições DSVM suportadas      | Windows e Linux   |
| Para que ele serve? | Ferramenta NVIDIA para consultar a atividade da GPU |
| Como é configurado/instalado no DSVM?  | `nvidia-smi`está no caminho do sistema. |
| Como executá-lo      | Em uma máquina virtual **com GPU's,** abra um prompt de comando (no `nvidia-smi`Windows) ou um terminal (no Linux) e execute . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Edições DSVM suportadas      | Linux |
| Como é configurado/instalado no DSVM?  | Instalado em [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Os cadernos Jupyter de amostra estão incluídos e as amostras estão em /dsvm/samples/pytorch. |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute o Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Conecte-se e abra o diretório PyTorch para amostras.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.13 |
| Edições DSVM suportadas      | Windows, Linux |
| Como é configurado/instalado no DSVM?  | Instalado em Python 3.5 no [Linux](dsvm-tools-languages.md#python-linux-edition) e Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute o Python. <br/> * Jupyter: Conecte-se ao [Jupyter](provision-vm.md) ou [ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e abra o diretório TensorFlow para amostras.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.12 |
| Edições DSVM suportadas      | Linux |
| Como é configurado/instalado no DSVM?  | tensorflow_model_server está disponível no terminal. |
| Como executá-lo      |  Exemplos estão disponíveis [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Versão(s) suportada | 1.0.3 |
| Edições DSVM suportadas      | Linux |
| Como é configurado/instalado no DSVM?  |Theano está instalado no python 2.7 _(raiz),_ e no ambiente Python 3.5 _(py35)._ |
| Como executá-lo      |  Terminal: Ative a versão Python desejada (raiz ou py35), execute python e importe Theano.<br/>* Jupyter: Selecione o kernel Python 2.7 ou 3.5 e, em seguida, importe Theano.  <br/>Para contornar um bug recente da biblioteca de kernel matemático (MKL), você precisa primeiro definir a camada de rosca do MKL da seguinte forma:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |