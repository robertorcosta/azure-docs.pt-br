---
title: Métodos de treinamento de modelos
titleSuffix: Azure Machine Learning
description: Aprenda os diferentes métodos que você pode usar para treinar modelo com o Azure Machine Learning. Os estimadores fornecem uma maneira fácil de trabalhar com estruturas populares como Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Os pipelines de Machine Learning facilitam o agendamento de corridas não assistidas, o uso de ambientes de computação heterogêneos e a reutilização de partes do seu fluxo de trabalho. E as configurações de execução fornecem controle granular sobre os alvos de computação que o processo de treinamento executa.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129775"
---
# <a name="train-models-with-azure-machine-learning"></a>Treinar modelos com Azure Machine Learning

O Azure Machine Learning fornece várias maneiras de treinar seus modelos, desde soluções de código primeiro usando o SDK até soluções de baixo código, como aprendizado automatizado de máquina e designer visual. Use a lista a seguir para determinar qual método de treinamento é certo para você:

+ [Azure Machine Learning SDK for Python](#python-sdk): O Python SDK fornece várias maneiras de treinar modelos, cada um com capacidades diferentes.

    | Método de treinamento | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **maneira genérica de treinar modelos** é usar um script de treinamento e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treinamento usado para treinar seu modelo. Você pode pegar uma configuração de execução, seu script de treinamento e um alvo de computação (o ambiente de treinamento) e executar um trabalho de treinamento. |
    | [Aprendizado de máquina automatizado](#automated-machine-learning) | O aprendizado automatizado de máquina permite **treinar modelos sem amplo conhecimento**de ciência de dados ou programação. Para pessoas com experiência em ciência de dados e programação, ele fornece uma maneira de economizar tempo e recursos automatizando a seleção de algoritmos e a sintonia de hiperparâmetros. Você não precisa se preocupar em definir uma configuração de execução ao usar aprendizado de máquina automatizado. |
    | [Estimadores](#estimators) | As aulas de estimador **facilitam a formação de modelos baseados em estruturas populares de aprendizagem de máquina.** Existem classes de estimadores para **Scikit-learn**, **PyTorch,** **TensorFlow**e **Chainer**. Há também um estimador genérico que pode ser usado com frameworks que ainda não possuem uma classe estimada dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar estimadores. |
    | [Pipeline de aprendizagem de máquina](#machine-learning-pipeline) | Os pipelines não são um método de treinamento diferente, mas uma **maneira de definir um fluxo de trabalho usando etapas modulares e reutilizáveis,** que podem incluir o treinamento como parte do fluxo de trabalho. Os pipelines de aprendizado de máquina suportam o uso automatizado de aprendizado de máquina, estimadores e configuração de execução para treinar modelos. Como os gasodutos não são focados especificamente no treinamento, as razões para o uso de um pipeline são mais variadas do que os outros métodos de treinamento. Geralmente, você pode usar um oleoduto quando:<br>* Você deseja **agendar processos autônomos,** como trabalhos de treinamento de longa duração ou preparação de dados.<br>* Use **várias etapas** coordenadas entre recursos de computação heterogêneos e locais de armazenamento.<br>* Use o pipeline como um **modelo reutilizável** para cenários específicos, como retreinamento ou pontuação em lote.<br>* **Acompanhe e versão as fontes de dados, entradas e saídas para** o seu fluxo de trabalho.<br>* Seu fluxo de trabalho é **implementado por diferentes equipes que trabalham em etapas específicas de forma independente**. As etapas podem então ser unidas em um pipeline para implementar o fluxo de trabalho. |

+ [Azure Machine Learning SDK for Python](#r-sdk): O SDK usa o pacote reticulado para se ligar ao Python SDK do Azure Machine Learning. Isso permite que você tenha acesso a objetos e métodos principais implementados no Python SDK a partir de qualquer ambiente R.

+ **Designer**: O designer de Machine Learning (preview) do Azure fornece um ponto de entrada fácil no aprendizado de máquina para a construção de provas de conceitos, ou para usuários com pouca experiência em codificação. Ele permite que você treine modelos usando uma im suinode e queda na Web. Você pode usar o código Python como parte do design, ou treinar modelos sem escrever nenhum código.

+ **CLI**: A CLI de aprendizado de máquina fornece comandos para tarefas comuns com o Azure Machine Learning, e é frequentemente usada para **scripts e automatização de tarefas**. Por exemplo, uma vez que você criou um script de treinamento ou pipeline, você pode usar o CLI para iniciar uma execução de treinamento em um cronograma ou quando os arquivos de dados usados para treinamento são atualizados. Para modelos de treinamento, fornece comandos que submetem trabalhos de treinamento. Ele pode enviar trabalhos usando configurações de execução ou pipelines.

Cada um desses métodos de treinamento pode usar diferentes tipos de recursos computacionais para treinamento. Coletivamente, esses recursos são chamados de [__metas computacionais.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>SDK do Python

O Azure Machine Learning SDK for Python permite que você construa e execute fluxos de trabalho de aprendizado de máquina com o Azure Machine Learning. Você pode interagir com o serviço a partir de uma sessão Python interativa, Jupyter Notebooks, Visual Studio Code ou outro IDE.

* [O que é o Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configurar um ambiente de desenvolvimento para Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de treinamento genérico com o Azure Machine Learning pode ser definido usando a [Configuração de Execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). A configuração de execução é então usada, juntamente com seus scripts de treinamento para treinar um modelo em um alvo de computação.

Você pode começar com uma configuração de execução para o computador local e, em seguida, mudar para um para um alvo de computação baseado em nuvem, conforme necessário. Ao alterar o destino de computação, você só altera a configuração de execução que você usa. Uma corrida também registra informações sobre o trabalho de treinamento, como entradas, saídas e registros.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Treine seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: Exemplos de Modelos de Treinamento do Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Como: Configurar e usar metas de computação para treinamento de modelos](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizado de Machine Learning

Defina as iterações, configurações do hiperparâmetro, featurização e outras configurações. Durante o treinamento, o Azure Machine Learning tenta diferentes algoritmos e parâmetros em paralelo. O treinamento pára quando atinge os critérios de saída que você definiu. Você não precisa se preocupar em definir uma configuração de execução ao usar estimadores.

> [!TIP]
> Além do Python SDK, você também pode usar o Automated ML através [do estúdio Azure Machine Learning](https://ml.azure.com).

* [O que é o machine learning automatizado?](concept-automated-ml.md)
* [Tutorial: Crie seu primeiro modelo de classificação com aprendizado de máquina automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Use aprendizado de máquina automatizado para prever tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: Exemplos do Jupyter Notebook para aprendizado automatizado de máquina](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: Configurar experimentos ML automatizados em Python](how-to-configure-auto-train.md)
* [Como: Treinar automaticamente um modelo de previsão de séries tempois](how-to-auto-train-forecast.md)
* [Como: Criar, explorar e implantar experimentos automatizados de aprendizado de máquina com o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Estimadores

Os estimadores facilitam o treino de modelos usando estruturas ml populares. Se você estiver usando **Scikit-learn**, **PyTorch**, **TensorFlow**ou **Chainer,** você deve considerar o uso de um estimador para treinamento. Há também um estimador genérico que pode ser usado com frameworks que ainda não possuem uma classe estimada dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar estimadores.

* [O que são estimadores?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: Treinar modelos de classificação de imagem usando dados MNIST e scikit-learn com o Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exemplos: Exemplos de Jupyter Notebook de usar estimadores](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Como: Criar estimadores no treinamento](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline de aprendizagem de máquina

Os pipelines de aprendizado de máquina podem usar os métodos de treinamento mencionados anteriormente (configuração de execução, estimadores e aprendizado automatizado de máquina). Os pipelines são mais sobre a criação de um fluxo de trabalho, por isso englobam mais do que apenas o treinamento de modelos. Em um pipeline, você pode treinar um modelo usando aprendizado de máquina automatizado, estimativas ou configurações de execução.

* [O que são gasodutos ML no Azure Machine Learning?](concept-ml-pipelines.md)
* [Crie e execute pipelines de aprendizado de máquina com o Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Use pipelines de aprendizado de máquina do Azure para pontuação em lote](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: Exemplos de Notebook Jupyter para pipelines de aprendizado de máquina](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: Pipeline com aprendizado de máquina automatizado](https://aka.ms/pl-automl)
* [Exemplos: Pipeline com estimadores](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>SDK do R

O R SDK permite que você use a linguagem R com o Azure Machine Learning. O SDK usa o pacote reticulado para se ligar ao Python SDK do Azure Machine Learning. Isso permite que você tenha acesso a objetos e métodos principais implementados no Python SDK a partir de qualquer ambiente R.

Para obter mais informações, consulte os seguintes artigos:

* [Tutorial: Crie um modelo de regressão logística](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK para referência R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

O designer permite que você treine modelos usando uma interface de arrastar e soltar no seu navegador.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial : Prever o preço do automóvel](tutorial-designer-automobile-price-train-score.md)
+ [Regressão: Prever preço](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificação: Prever renda](how-to-designer-sample-classification-predict-income.md)
+ [Classificação: Prever churn, appetency e up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificação com script R personalizado: Prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
+ [Classificação de texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

A CLI de aprendizado de máquina é uma extensão para o Azure CLI. Ele fornece comandos CLI multiplataforma para trabalhar com o Azure Machine Learning. Normalmente, você usa a CLI para automatizar tarefas, como treinar um modelo de aprendizado de máquina.

* [Use a extensão CLI para Aprendizado de Máquina do Azure](reference-azure-machine-learning-cli.md)
* [MLOps no Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Próximas etapas

Aprenda a [configurar ambientes de treinamento](how-to-set-up-training-targets.md).
