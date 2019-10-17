---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 04753ca4c9b14d7ccc265cfcf971b3fd63c861ae
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72384154"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizados em Python

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

Se preferir uma experiência sem código, você também poderá [criar experiências automatizadas de aprendizado de máquina em portal do Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O aprendizado de máquina automatizado dá suporte a tipos de tarefa de classificação, regressão e previsão.

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

Classificação | regressão | Previsão de série temporal
|-- |-- |--
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
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Use o parâmetro `task` no Construtor `AutoMLConfig` para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um dataframe do pandas ou em um Azure Machine Learning DataSet. Os exemplos de código a seguir demonstram como armazenar os dados nesses formatos. [Saiba mais sobre o datatsets](https://github.com/MicrosoftDocs/azure-docs-pr/pull/how-to-create-register-datasets.md).

* TabularDataset
* Dataframe do Pandas

>[!Important]
> Requisitos para dados de treinamento:
>* Os dados devem estar no formato de tabela.
>* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

Exemplos:

* TabularDataset
```python
    from azureml.core.dataset import Dataset

    tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
    train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
    label = "Label"
```

*   Dataframe do Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Para execuções remotas, os dados de treinamento devem ser acessíveis a partir da computação remota. A classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) no SDK expõe a funcionalidade para:

* Transfira facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho
* disponibilizar seus dados para scripts de treinamento ao executar recursos de computação em nuvem

Consulte o [instruções](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) para obter um exemplo de como usar a classe `Dataset` para montar dados em seu destino de computação.

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar conjuntos de treinamento e de validação separados diretamente no Construtor `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (subamostra aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, geralmente de data de série temporal ou dados desequilibrados. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
*   O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
*   Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

Confira o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

*   Um cluster Azure Databricks na sua assinatura do Azure. Você pode encontrar mais detalhes aqui- [configurar Azure Databricks cluster para o ml automatizado](how-to-configure-environment.md#azure-databricks)

Consulte o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver os notebooks de exemplo com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1.  Teste de classificação usando AUC ponderado como a métrica primária com um tempo máximo de 12.000 segundos por iteração, com o experimento para terminar depois de 50 iterações e 2 dobras de validação cruzada.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Veja abaixo um exemplo de experimento de regressão programado para terminar após 100 iterações, em que cada iteração dura até 600 segundos com cinco partições de validação cruzada.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

Os três valores de parâmetro `task` diferentes (o terceiro tipo de tarefa é `forecasting` e usa o mesmo pool de algoritmos que as tarefas de `regression`) determinam a lista de modelos a serem aplicados. Use os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos com suporte pode ser encontrada na [classe SupportedModels](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py).

### <a name="primary-metric"></a>Métrica principal
A métrica primária determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis que você pode selecionar são determinadas pelo tipo de tarefa que você escolher e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

|Classificação | regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Saiba mais sobre as definições específicas deles em [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

### <a name="data-preprocessing--featurization"></a>Pré-processamento de dados & personalização

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são [dimensionados e normalizados automaticamente](concept-automated-ml.md#preprocess) para ajudar *determinados* algoritmos que são sensíveis a recursos que estão em escalas diferentes.  No entanto, você também pode habilitar o pré-processamento/personalização adicional, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-create-portal-experiments.md#preprocess).

Para habilitar esse personalização, especifique `"preprocess": True` para a [classe `AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="time-series-forecasting"></a>Previsão de série temporal
A tarefa série temporal `forecasting` requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: parâmetro obrigatório que define o nome da coluna em seus dados de treinamento que contém uma série temporal válida.
1. `max_horizon`: define o período de tempo que você deseja prever com base na periodicidade dos dados de treinamento. Por exemplo, se você tiver dados de treinamento com refinamentos diários de tempo, defina a distância em dias que deseja que o modelo treine.
1. `grain_column_names`: define o nome das colunas que contêm dados de série temporal individuais em seus dados de treinamento. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de armazenamento e marca como suas colunas de refinamento. As diversas séries temporais e as previsões serão criadas para cada granulação/agrupamento. 

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
                             iterations=10,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Configuração do Ensemble

Os modelos de Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução de Machine Learning automatizada. Atualmente, os métodos Ensemble com suporte são votação e empilhamento. A votação é implementada como votação suave usando médias ponderadas e a implementação de empilhamento está usando uma implementação de 2 camadas, em que a primeira camada tem os mesmos modelos que o Ensemble de votação, e o segundo modelo de camada é usado para encontrar a combinação ideal do modelos da primeira camada. Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitado, o empilhamento será desabilitado e somente a votação será utilizada.

Há vários argumentos padrão que podem ser fornecidos como `kwargs` em um objeto `AutoMLConfig` para alterar o comportamento de Ensemble da pilha padrão.

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-aprendizs padrão são `LogisticRegression` para tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver habilitada) e `ElasticNet` para tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver habilitada). Esse parâmetro pode ser uma das seguintes cadeias de caracteres: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher treinamento e tipo de validação, para ser reservado para treinar o meta-aprendiz. O valor padrão é `0.2`.
* `stack_meta_learner_kwargs`: parâmetros opcionais a serem passados para o inicializador do meta-aprendiz. Esses parâmetros e tipos de parâmetro espelham aqueles do construtor de modelo correspondente e são encaminhados para o construtor de modelo.

O código a seguir mostra um exemplo de especificação do comportamento Ensemble personalizado em um objeto `AutoMLConfig`.

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
        iterations=20,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

O treinamento do Ensemble é habilitado por padrão, mas pode ser desabilitado usando os parâmetros boolianos `enable_voting_ensemble` e `enable_stack_ensemble`.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Executar o experimento

Para o ML automatizado, você cria um objeto `Experiment`, que é um objeto nomeado em um `Workspace` usado para executar experimentos.

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

### <a name="exit-criteria"></a>Critérios de saída
Há algumas opções que você pode definir para encerrar seu experimento.
1. Nenhum critério: se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Número de iterações: você define o número de iterações para a execução do experimento. Opcionalmente, você pode adicionar `iteration_timeout_minutes` para definir um limite de tempo em minutos por cada iteração.
1. Sair após um período de tempo: usar `experiment_timeout_minutes` em suas configurações permite que você defina por quanto tempo em minutos um experimento continuará em execução.
1. Sair depois que uma pontuação for atingida: usar `experiment_exit_score` concluirá o experimento depois que uma pontuação de métrica primária for atingida.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Você pode exibir os resultados de treinamento em um widget ou embutido se estiver em um bloco de anotações. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizados

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia automatizada de recursos (se pré-processar = true)
+ Dimensionamento/normalização e algoritmo com valores de hiperparâmetro

Tornamos transparente para obter essas informações da saída do fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de pré-processamento e [engenharia de recursos automatizada](concept-automated-ml.md#preprocess) que ocorre quando pré-processamento = verdadeiro.

Considere este exemplo:
+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (DateTime)
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputados por média
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

   Onde:

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Passou|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Dimensionamento/normalização e algoritmo com valores de hiperparâmetro:

Para entender os valores de dimensionamento/normalização e de algoritmo/hiperparâmetro para um pipeline, use fitted_model. Steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Aqui está uma amostra de saída:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use essa função auxiliar mostrada neste [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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


print_model(fitted_model)
```

Veja a seguir um exemplo de saída para um pipeline usando um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Explicar o modelo (interpretabilidade)

O aprendizado de máquina automatizado permite que você entenda a importância do recurso.  Durante o processo de treinamento, é possível obter a importância global do recurso para o modelo.  Para cenários de classificação, também é possível obter a importância do recurso no nível da classe.  Você deve fornecer um conjunto de validation_data (DataSet de validação) para obter a importância do recurso.

Há duas maneiras de gerar a importância do recurso.

*   Quando um experimento é concluído, é possível usar o método `explain_model` em qualquer iteração.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, train_data, test_data)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Para exibir a importância do recurso para todas as iterações, defina o sinalizador `model_explainability` como `True` em AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task='classification',
                                 debug_log='automl_errors.log',
                                 primary_metric='AUC_weighted',
                                 max_time_sec=12000,
                                 iterations=10,
                                 verbosity=logging.INFO,
                                 training_data=train_data,
                                 label_column_name=y_train,
                                 validation_data=test_data,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Quando terminar, será possível usar o método retrieve_model_explanation para recuperar a importância do recurso para uma iteração específica.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Exiba a URL para exibir a importância do recurso usando o objeto de execução:

```
automl_run.get_portal_url()
```

Você pode visualizar o gráfico de importância do recurso em seu espaço de trabalho na portal do Azure ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com). O gráfico também é mostrado ao usar o widget `RunDetails` [Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) em um bloco de anotações. Para saber mais sobre os gráficos, consulte [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![gráfico de importância do recurso](./media/how-to-configure-auto-train/feature-importance.png)

Para obter mais informações sobre como as explicações de modelo e a importância do recurso podem ser habilitadas em outras áreas do SDK fora do aprendizado de máquina automatizado, consulte o artigo [conceito](machine-learning-interpretability-explainability.md) sobre interpretação.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
