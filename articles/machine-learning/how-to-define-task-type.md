---
title: Definir uma tarefa de aprendizado de máquina para uma execução automatizada do Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como definir uma tarefa de aprendizado de máquina para uma execução automatizada do Machine Learning
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475509"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizado de máquina 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as tarefas de aprendizado de máquina com suporte e como defini-las para uma execução de teste automatizado do Machine Learning (ML automatizado).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>O que é uma tarefa de aprendizado de máquina?

Uma tarefa de aprendizado de máquina representa o tipo de problema que está sendo resolvido criando um modelo de previsão. O ML automatizado dá suporte a três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de série temporal.

Tipo de tarefa| Descrição| Exemplo
----|----|----
classificação | Tarefa para prever a categoria de uma linha específica em um conjunto de dado. | Detecção de fraudes em um cartão de crédito. A coluna de destino seria **detectada por fraude** com categorias de *verdadeiro* ou *falso*. Nesse caso, estamos classificando cada linha dos dados como true ou false.
Regressão | Tarefa para prever uma saída de quantidade contínua. | Custo de automóvel com base em seus recursos, a coluna de destino seria o **preço**.
Previsão |Tarefa para fazer estimativas informadas na determinação da direção das tendências futuras.| Previsão da demanda de energia para as próximas 48 horas. A coluna de destino seria **demanda** e os valores previstos seriam usados para mostrar padrões na demanda de energia.

O ML automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

classificação | Regressão | Previsão de série temporal
-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
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
Você pode definir o tipo de tarefa para seus experimentos de ML automatizados com o SDK ou o Azure Machine Learning Studio.

Use o `task` parâmetro no `AutoMLConfig` Construtor para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Você pode definir a tarefa como parte de sua criação de execução de experimento do ML automatizado no Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção do tipo de tarefa](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Saiba mais sobre [o treinamento automático de um modelo de previsão de série temporal](how-to-auto-train-forecast.md) no Azure Machine Learning
+ Experimente o tutorial de [classificação de Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) .
+ Experimente o bloco de anotações de amostra de [regressão automatizada de Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) .

