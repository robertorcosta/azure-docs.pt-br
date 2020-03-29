---
title: Defina uma tarefa de aprendizado de máquina para uma corrida automatizada de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como definir uma tarefa de aprendizado de máquina para uma corrida automatizada de aprendizado de máquina
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75540523"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizado de máquina 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as tarefas de aprendizado de máquina suportadas e como defini-las para uma execução automatizada de aprendizado de máquina (ML automatizado).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>O que é uma tarefa de aprendizado de máquina?

Uma tarefa de aprendizado de máquina representa o tipo de problema que está sendo resolvido criando um modelo preditivo. O ML automatizado suporta três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de séries tempor.

Tipo de tarefa| Descrição| Exemplo
----|----|----
classificação | Tarefa para prever a categoria de uma linha específica em um conjunto de dados. | Detecção de fraude em um cartão de crédito. A coluna de destino seria **Fraude Detectada** com categorias de *True* ou *False*. Neste caso, estamos classificando cada linha nos dados como verdadeira ou falsa.
Regressão | Tarefa para prever uma saída de quantidade contínua. | Custo do automóvel com base em suas características, a coluna-alvo seria **o preço**.
Previsão |Tarefa para fazer estimativas informadas na determinação do rumo das tendências futuras.| Previsão de demanda de energia para eles nas próximas 48 horas. A coluna-alvo seria **a demanda** e os valores previstos seriam usados para mostrar padrões na demanda de energia.

O ML automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

classificação | Regressão | Previsão de série temporal
-- |-- |--
[Regressão Logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte do C)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Defina o tipo de tarefa
Você pode definir o tipo de tarefa para seus experimentos ml automatizados com o SDK ou o estúdio Azure Machine Learning.

Use `task` o parâmetro `AutoMLConfig` no construtor para especificar seu tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Você pode definir a tarefa como parte da criação automatizada de execução de experiências ML no estúdio Azure Machine Learning. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção de tipo de tarefa](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Saiba mais sobre [o treinamento automático de um modelo de previsão de série sinuosa](how-to-auto-train-forecast.md) no Azure Machine Learning
+ Experimente o tutorial [de classificação automatizada de aprendizado de máquina.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)
+ Experimente o notebook de amostra [de regressão automatizada de aprendizagem de máquina.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)

