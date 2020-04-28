---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 7b268f3efa64c2f1ae78794056fdfda2b27434e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186922"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizado em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, saiba como definir várias definições de configuração de seus experimentos de aprendizado de máquina automatizados com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de Machine Learning automatizados, consulte [tutorial: treinar um modelo de classificação com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [treinar modelos com o aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: classificação, regressão ou previsão de série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se preferir uma experiência sem código, você também poderá [criar experiências automatizadas de aprendizado de máquina no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O Machine Learning automatizado dá suporte a tipos de tarefas de classificação, regressão e previsão. Saiba mais sobre os [tipos de tarefa](how-to-define-task-type.md).

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

> [!NOTE]
> Se você planeja exportar os modelos de ML criados automaticamente para um [modelo ONNX](concept-onnx.md), somente os algoritmos indicados com um * poderão ser convertidos no formato ONNX. Saiba mais sobre como [converter modelos em ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observe também que o ONNX dá suporte apenas a tarefas de classificação e regressão no momento. 

classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)* |[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)*|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[Os vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Os vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação de vetor de suporte (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Descendente estocástico Grading (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressor linear rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[ARIMA automático](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Descendente estocástico Grading (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Regressor descendente de gradiente online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Classificação média de perceptron](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classificador SVM linear](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Use o `task` parâmetro no `AutoMLConfig` Construtor para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um **Dataframe do pandas** ou um **Azure Machine Learning TabularDataset**.  [Saiba mais sobre conjuntos](how-to-create-register-datasets.md)de informações.

Requisitos para dados de treinamento:
- Os dados devem estar no formato de tabela.
- O valor a prever, coluna de destino, deve estar nos dados.

Os exemplos de código a seguir demonstram como armazenar os dados nesses formatos.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Dataframe do Pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Para execuções remotas, os dados de treinamento devem ser acessíveis a partir da computação remota. A classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) no SDK expõe a funcionalidade para:

* Transfira facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho
* disponibilizar seus dados para scripts de treinamento ao executar recursos de computação em nuvem

Consulte o [instruções](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) para obter um exemplo de como usar a `Dataset` classe para montar dados em seu destino de computação.

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar conjuntos de treinamento e de validação separados diretamente `AutoMLConfig` no construtor.

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (subamostra aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, geralmente de data de série temporal ou dados desequilibrados. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
* O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
* Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

* Um cluster Azure Databricks na sua assinatura do Azure. Você pode encontrar mais detalhes aqui- [configurar Azure Databricks cluster para o ml automatizado](how-to-configure-environment.md#azure-databricks)

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para obter exemplos de blocos de anotações com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Teste de classificação usando AUC ponderado como a métrica primária com minutos de tempo limite de experimento definido como 30 minutos e 2 dobras de validação cruzada.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Abaixo está um exemplo de um experimento de regressão definido para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Os três valores `task` de parâmetro diferentes (o terceiro tipo de tarefa `forecasting`é e usam um pool de algoritmos `regression` semelhantes como tarefas) determinam a lista de modelos a serem aplicados. Use os `whitelist` parâmetros `blacklist` ou para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos com suporte pode ser encontrada na [classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([classificação](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [regressão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Para ajudar a evitar falhas de tempo limite de experimento, o serviço de validação `experiment_timeout_minutes` do ml automatizado exigirá que seja definido como um mínimo de 15 minutos ou 60 minutos se a linha por tamanho de coluna exceder 10 milhões.

### <a name="primary-metric"></a>Métrica principal
A métrica primária determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis que você pode selecionar são determinadas pelo tipo de tarefa que você escolher e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

|classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Saiba mais sobre as definições específicas dessas métricas em [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Personalização de dados

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são [dimensionados e normalizados automaticamente](concept-automated-ml.md#preprocess) para ajudar *determinados* algoritmos que são sensíveis a recursos que estão em escalas diferentes.  No entanto, você também pode habilitar personalização adicionais, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-use-automated-ml-for-ml-models.md#featurization).

Ao configurar seus experimentos, você pode habilitar a configuração `featurization`avançada. A tabela a seguir mostra as configurações aceitas para personalização na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuração do personalização | Descrição |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que a etapa personalização personalizada deve ser usada. [Saiba como personalizar o personalização](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Indica que a etapa personalização não deve ser feita automaticamente.|
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [as etapas de guardrails e personalização de dados](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) são executadas automaticamente.|

> [!NOTE]
> As etapas de personalização automatizadas do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de personalização aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="time-series-forecasting"></a>Previsão de série temporal
A tarefa de `forecasting` série temporal requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro obrigatório que define o nome da coluna em seus dados de treinamento que contém uma série temporal válida.
1. `max_horizon`: Define o período de tempo que você deseja prever com base na periodicidade dos dados de treinamento. Por exemplo, se você tiver dados de treinamento com refinamentos diários de tempo, defina a distância em dias que deseja que o modelo treine.
1. `grain_column_names`: Define o nome das colunas que contêm dados de série temporal individuais em seus dados de treinamento. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de armazenamento e marca como suas colunas de refinamento. As diversas séries temporais e as previsões serão criadas para cada granulação/agrupamento. 

Para obter exemplos das configurações usadas abaixo, consulte o [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Configuração do Ensemble

Os modelos de Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução de Machine Learning automatizada. Atualmente, os métodos Ensemble com suporte são votação e empilhamento. A votação é implementada como votação suave usando médias ponderadas e a implementação de empilhamento está usando uma implementação de duas camadas, em que a primeira camada tem os mesmos modelos que o Ensemble de votação, e o segundo modelo de camada é usado para localizar a combinação ideal dos modelos da primeira camada. Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitado, o empilhamento será desabilitado e somente a votação será utilizada.

Há vários argumentos padrão que podem ser fornecidos como `kwargs` em um `AutoMLConfig` objeto para alterar o comportamento padrão da pilha Ensemble.

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-aprendizs padrão `LogisticRegression` são para tarefas de classificação `LogisticRegressionCV` (ou se a validação cruzada estiver `ElasticNet` habilitada) e para tarefas de regressão `ElasticNetCV` /previsão (ou se a validação cruzada estiver habilitada). Esse parâmetro pode ser uma das seguintes cadeias de `LogisticRegression`caracteres `LogisticRegressionCV`: `LightGBMClassifier`, `ElasticNet` `ElasticNetCV` `LightGBMRegressor`,,,, `LinearRegression`ou.
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher treinamento e tipo de validação) a ser reservado para treinar o meta-aprendiz. O valor padrão é `0.2`.
* `stack_meta_learner_kwargs`: parâmetros opcionais a serem passados para o inicializador do meta-aprendiz. Esses parâmetros e tipos de parâmetro espelham os parâmetros e os tipos de parâmetro do construtor de modelo correspondente e são encaminhados para o construtor de modelo.

O código a seguir mostra um exemplo de especificação do comportamento Ensemble personalizado `AutoMLConfig` em um objeto.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

O treinamento do Ensemble é habilitado por padrão, mas pode ser desabilitado usando `enable_voting_ensemble` os `enable_stack_ensemble` parâmetros Boolianos e.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Executar o experimento

Para o ML automatizado, você `Experiment` cria um objeto, que é um objeto nomeado `Workspace` em um usado para executar experimentos.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Envie o experimento para ser executado e para gerar um modelo. Passe o `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez em um novo computador.  Pode levar até dez minutos antes da saída ser exibida.
>Definir `show_output` como `True` faz a saída ser mostrada no console.

### <a name="exit-criteria"></a><a name="exit"></a>Critérios de saída

Há algumas opções que você pode definir para encerrar seu experimento.
1. Nenhum critério: se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Sair após um período de tempo: usar `experiment_timeout_minutes` em suas configurações permite que você defina por quanto tempo em minutos um experimento continuará em execução.
1. Sair depois que uma pontuação for atingida: `experiment_exit_score` usar concluirá o experimento depois que uma pontuação de métrica primária for atingida.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Você pode exibir os resultados de treinamento em um widget ou embutido se estiver em um bloco de anotações. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizados

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia automatizada de recursos `"featurization": 'auto'`(se)
+ Dimensionamento/normalização e algoritmo com valores de hiperparâmetro

Tornamos transparente para obter essas informações da fitted_model saída do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de pré-processamento e [engenharia automatizada de recursos](concept-automated-ml.md#preprocess) que `"featurization": 'auto'`ocorre quando o.

Considere este exemplo:
+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (DateTime)
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputadosdos pela média
+ O recurso DateTime D é destacados em 11 recursos de engenharia diferentes

Use essas duas APIs na primeira etapa do modelo ajustado para entender mais.  Consulte [este bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos com engenharia.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Essa lista inclui todos os nomes de recursos de engenharia.

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

+ API 2: `get_featurization_summary()` retorna o resumo de personalização para todos os recursos de entrada.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

  Saída:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Em que:

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Descartada|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|
   
### <a name="customize-feature-engineering"></a>Personalizar a engenharia de recursos
Para personalizar a engenharia de recursos `"featurization": FeaturizationConfig`, especifique.

A personalização com suporte inclui:

|Personalização|Definição|
|--|--|
|Atualização de finalidade de coluna|Substituir o tipo de recurso para a coluna especificada.|
|Atualização de parâmetro do transformador |Atualize os parâmetros para o transformador especificado. Atualmente, dá suporte a imputer (média & mediana, mais frequente) e HashOneHotEncoder.|
|Remover colunas |As colunas a serem descartadas são destacados.|
|Bloquear transformadores| Bloqueie os transformadores a serem usados no processo personalização.|

Crie o objeto FeaturizationConfig usando chamadas à API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Dimensionamento/normalização e algoritmo com valores de hiperparâmetro:

Para entender os valores de dimensionamento/normalização e de algoritmo/hiperparâmetro para um pipeline, use fitted_model. Steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Aqui está uma amostra de saída:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use esta função auxiliar: 

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(model)
```

A saída de exemplo a seguir é para um pipeline usando um algoritmo específico (LogisticRegression com RobustScalar, nesse caso).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Prever probabilidade de classe

Os modelos produzidos usando o ML automatizado têm objetos wrapper que espelham a funcionalidade de sua classe de origem de código aberto. A maioria dos objetos do wrapper do modelo de classificação retornados `predict_proba()` pelo ml automatizado implementam a função, que aceita uma amostra de dados de matriz esparsa ou com matriz de seus recursos (valores X) e retorna uma matriz n-dimensional de cada amostra e sua respectiva probabilidade de classe.

Supondo que você tenha recuperado o melhor modelo de execução e ajustada usando as mesmas chamadas acima, `predict_proba()` você pode chamar diretamente do modelo ajustado, fornecendo `X_test` um exemplo no formato apropriado dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não oferecer suporte à `predict_proba()` função ou o formato estiver incorreto, uma exceção específica de classe de modelo será lançada. Consulte os documentos de referência do [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e do [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para obter exemplos de como essa função é implementada para diferentes tipos de modelo.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilidade do modelo

A interpretação de modelo permite que você entenda por que seus modelos fizeram previsões e os valores subjacentes de importância do recurso. O SDK inclui vários pacotes para habilitar recursos de interpretação de modelo, tanto no tempo de inferência quanto no treinamento, para modelos locais e implantados.

Consulte o [instruções](how-to-machine-learning-interpretability-automl.md) para obter exemplos de código sobre como habilitar recursos de interpretação especificamente em experiências de Machine Learning automatizadas.

Para obter informações gerais sobre como as explicações de modelo e a importância dos recursos podem ser habilitadas em outras áreas do SDK fora do aprendizado de máquina automatizado, consulte o artigo [conceito](how-to-machine-learning-interpretability.md) sobre interpretação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
