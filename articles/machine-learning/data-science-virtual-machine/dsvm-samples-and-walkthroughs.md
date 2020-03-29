---
title: Programas de amostra & passo a passo do ML
titleSuffix: Azure Data Science Virtual Machine
description: Através dessas amostras e passo a passo, aprenda a lidar com tarefas e cenários comuns com a Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900041"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Amostras em máquinas virtuais de data science do Azure

As Máquinas Virtuais de Data Science (DSVMs) do Azure incluem um conjunto abrangente de código de amostra. Essas amostras incluem cadernos Jupyter e scripts em linguagens como Python e R.
> [!NOTE]
> Para obter mais informações sobre como executar notebooks Jupyter em suas máquinas virtuais de ciência de dados, consulte a seção [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Pré-requisitos

Para executar essas amostras, você deve ter provisionado uma Máquina Virtual de Ciência de Dados. Veja as partidas rápidas para [Windows](./provision-vm.md) e [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Exemplos disponíveis
| Categoria de exemplos | Descrição | Locais |
| ------------- | ------------- | ------------- |
| Linguagem R  | As amostras ilustram cenários como como se conectar com os armazenamentos de dados em nuvem baseados no Azure e como comparar o R de código aberto e o Microsoft Machine Learning Server. Eles também explicam como operacionalizar modelos no Microsoft Machine Learning Server e no SQL Server. <br/> [Língua R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | As amostras explicam cenários como como se conectar com os armazenamentos de dados em nuvem baseados no Azure e como trabalhar com o Azure Machine Learning.  <br/> [Linguagem python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem Julia  | Fornece uma descrição detalhada de plotagem e aprendizado profundo em Julia. Também explica como chamar C e Python de Julia. <br/> [Língua julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustra como construir modelos de aprendizado de máquina e aprendizagem profunda com Machine Learning. Implante modelos em qualquer lugar. Use aprendizado de máquina automatizado e ajuste de hiperparâmetro inteligente. Use também o gerenciamento de modelos e treinamento distribuído. <br/> [Aprendizado de Máquina](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooks do PyTorch  | Amostras de aprendizagem profunda que usam redes neurais baseadas em PyTorch. Os notebooks vão de cenários iniciantes a avançados.  <br/> [Notebooks do PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Uma variedade de amostras e técnicas de rede neural implementadas usando a estrutura TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Kit de Ferramentas Cognitivas da Microsoft <br/>   | Amostras de aprendizagem profunda publicadas pela equipe do Cognitive Toolkit na Microsoft.  <br/> [Kit de Ferramentas Cognitivas](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Amostras de aprendizagem profunda que usam redes neurais baseadas em Cafeína. Vários notebooks familiarizam os usuários com o Caffe2 e como usá-lo de forma eficaz. Exemplos incluem pré-processamento de imagens e criação de conjunto de dados. Eles também incluem regressão e como usar modelos pré-treinados. <br/> [Cafeína2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Amostras baseadas em Python que usam H2O para cenários de problemas do mundo real. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguagem SparkML  | Amostras que usam recursos do kit de ferramentas Apache Spark MLLib através de pySpark e MMLSpark: Microsoft Machine Learning para Apache Spark no Apache Spark 2.x.  <br/> [Língua SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Amostras padrão de aprendizado de máquina no XGBoost para cenários como classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acessar o Jupyter 

Para acessar o Jupyter, selecione o ícone **Jupyter** no menu desktop ou aplicativo. Você também pode acessar o Jupyter em uma edição Linux de um DSVM. Para acessar remotamente a partir `https://<Full Domain Name or IP Address of the DSVM>:8000` de um navegador da Web, vá para o Ubuntu.

Para adicionar exceções e tornar o acesso jupyter disponível em um navegador, use a seguinte orientação:


![Habilitar exceção do Jupyter](./media/ubuntu-jupyter-exception.png)


Faça login com a mesma senha que você usa para fazer login na Máquina Virtual de Ciência de Dados.
<br/>

**Jupyter casa**
<br/>![Página inicial do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Exemplos em Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguagem Julia 
<br/>![Exemplos de Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Amostras de Aprendizado de Máquina do Azure](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemplos do PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos de TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Exemplos do CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

