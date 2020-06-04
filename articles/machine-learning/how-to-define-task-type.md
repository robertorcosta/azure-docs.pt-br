---
title: Definir uma tarefa de aprendizado de máquina para uma execução automatizada do Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como definir uma tarefa de aprendizado de máquina para uma execução automatizada do aprendizado de máquina
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653256"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizado de máquina 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as tarefas de aprendizado de máquina compatíveis e como defini-las para uma execução de teste automatizado do Machine Learning (ML automatizado).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> O que é uma tarefa de aprendizado de máquina?

Uma tarefa de aprendizado de máquina representa o tipo de problema que está sendo resolvido criando um modelo preditivo. O ML automatizado oferece suporte a três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de série temporal.

Tipo de tarefa| Descrição| Exemplo
----|----|----
classificação | Tarefa para prever a categoria de uma linha específica em um conjunto de dados. | Detecção de fraudes em um cartão de crédito. A coluna de destino seria **Fraude Detectada** com as categorias *True* ou *False*. Nesse caso, estamos classificando cada linha dos dados como true ou false.
Regressão | Tarefa para prever uma saída de quantidade contínua. | Custo de automóvel com base em seus recursos, a coluna de destino seria **preço**.
Previsão |Tarefa para fazer estimativas informadas na determinação da direção de tendências futuras.| Previsão da demanda de energia para as próximas 48 horas. A coluna de destino seria **demanda** e os valores previstos seriam usados para mostrar padrões na demanda de energia.

O ML automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

classificação | Regressão | Previsão de série temporal
-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte do C)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Definir o tipo de tarefa
Você pode definir o tipo de tarefa para seus testes de ML automatizados com o SDK ou o Azure Machine Learning Studio.

Use o parâmetro `task` no construtor `AutoMLConfig` para especificar o tipo de teste.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Você pode definir a tarefa como parte da criação de execução do teste de ML automatizados com o Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção de tipo de tarefa](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [ML automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Saiba mais sobre [treinar automaticamente um modelo de previsão de série temporal](how-to-auto-train-forecast.md) no Azure Machine Learning
+ Experimente o tutorial [Classificação automatizada do Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).
+ Experimente o notebook de amostra [Regressão com Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb).

