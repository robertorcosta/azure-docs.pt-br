---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/20/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 025d3b1e0ce2f46cc689d74fe659facf026dc215
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852490"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizado no Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, vamos mostrar como definir várias configurações dos seus experimentos de machine learning automatizado com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de machine learning automatizado, confira [Tutorial: Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [Treinar modelos com aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Classificação, regressão ou previsão de série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se preferir um experimento sem código, você também pode [Criar seus experimentos de machine learning automatizado no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O machine learning automatizado tem suporte a tipos de tarefa de classificação, regressão e previsão. Saiba mais sobre [tipos de tarefas](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

> [!NOTE]
> Se você planeja exportar seus modelos de machine learning automatizado para um [modelo de ONNX](concept-onnx.md), somente os algoritmos indicados com um * podem ser convertidos no formato ONNX. Saiba mais sobre [conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observe também que, no momento, o ONNX tem suporte apenas a tarefas de classificação e regressão. 

classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)* |[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)*|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador do Perceptron médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)|[Regressor descendente do gradiente online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressor linear rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Classificador SVM linear](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)*||

Use o parâmetro `task` no construtor `AutoMLConfig` para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um **DataFrame do Pandas** ou em um **TabularDataset do Azure Machine Learning**.  [Saiba mais sobre conjuntos de dados](how-to-create-register-datasets.md).

Requisitos para dados de treinamento:
- Os dados devem estar em formato de tabela.
- O valor que você quer prever, coluna de destino, deve estar presente nos dados.

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

* transferir facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho
* disponibilizar dados para scripts de treinamento ao executar recursos de computação em nuvem

Consulte [instruções](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) para ver um exemplo de como usar a classe `Dataset` para montar dados em seu destino de computação.

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar conjuntos de treinamento e de validação separados diretamente no `AutoMLConfig` Construtor com as seguintes opções. Saiba mais sobre [como configurar divisões de dados e validação cruzada](how-to-configure-cross-validation-data-splits.md) para seus experimentos de AutoML. 

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

Saiba mais sobre como o autoML aplica a validação cruzada para [evitar os modelos com sobreajuste](concept-manage-ml-pitfalls.md#prevent-over-fitting).

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (Subamostra aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizado se a divisão aleatória não for aceitável, geralmente, dados de série temporal ou dados não balanceados. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios. Saiba mais sobre [como configurar um conjunto de validação personalizado com o SDK](how-to-configure-cross-validation-data-splits.md#provide-validation-data).

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
* O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
* Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure. 

  Confira este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

* Um cluster Azure Databricks na sua assinatura do Azure. Você pode encontrar mais detalhes aqui: [Configurar cluster Azure Databricks para o ML Automatizado](how-to-configure-environment.md#azure-databricks)

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para obter exemplos de notebooks com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experimento de classificação usando AUC ponderado como a métrica primária com tempo limite de experimento definido como 30 minutos e 2 dobras de validação cruzada.

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
2. Veja abaixo um exemplo de um experimento de regressão definido para terminar após 60 minutos com cinco dobras cruzadas de validação.

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

Os três valores de parâmetro `task` diferentes (o terceiro tipo de tarefa é `forecasting` e usa um pool de algoritmos semelhante como tarefas `regression`) determinam a lista de modelos a serem aplicados. Use os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os modelos disponíveis a serem incluídos ou excluídos. A lista de modelos com suporte pode ser encontrada em [Classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([Classificação](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting) e [Regressão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Para evitar falhas de tempo limite de experimento, o serviço de validação do ML automatizado exigirá que `experiment_timeout_minutes` seja definido para um mínimo de 15 minutos ou 60 minutos se o tamanho de linha por coluna exceder 10 milhões.

### <a name="primary-metric"></a>Métrica principal
A métrica primária determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis para seleção são determinadas pelo tipo de tarefa que você escolher, e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

|classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Saiba mais sobre as definições específicas dessas métricas em [Entender os resultados do machine learning automatizado](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Definição de recursos de dados

Em todos os experimentos de machine learning automatizado, seus dados são [automaticamente escalados e normalizados](how-to-configure-auto-features.md#) para ajudar *determinados* algoritmos sensíveis a recursos que estão em escalas diferentes.  No entanto, você também pode habilitar definição de recursos adicional, como imputação de valores ausentes, codificação e transformações.

Ao configurar seus experimentos em seu `AutoMLConfig` objeto, você pode habilitar/desabilitar a configuração `featurization` . A tabela a seguir mostra as configurações aceitas para definição de recursos na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuração de definição de recursos | Descrição |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [verificadores de integridade dos dados e etapas de definição de recursos](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Configuração padrão**|
|`"featurization": 'off'`| Indica que a etapa de definição de recursos não deve ser feita automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que uma etapa de definição de recursos personalizada deve ser usada. [Saiba como personalizar a definição de recursos](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> As etapas de definição de recursos de machine learning automatizado (normalização de recursos, manipulação de dados ausentes, conversão de texto em números, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de definição de recursos aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="time-series-forecasting"></a>Previsão de série temporal
A tarefa `forecasting` de série temporal requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro obrigatório que define o nome da coluna que contém uma série temporal válida nos seus dados de treinamento.
1. `forecast_horizon`: Define quantos períodos encaminhar você gostaria de prever. O horizonte inteiro está em unidades da frequência da série temporal. Por exemplo, se você tiver dados de treinamento com frequência diária, defina a distância em dias que deseja que o modelo treine.
1. `time_series_id_column_names`: Define as colunas que identificam exclusivamente a série temporal em dados que têm várias linhas com o mesmo carimbo de data/hora. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de repositório e marca como seus identificadores de série temporal. Previsões separadas serão criadas para cada agrupamento. Se os identificadores de série temporal não estiverem definidos, o conjunto de dados será considerado uma série temporal.

Para ver exemplos das configurações usadas abaixo, consulte o [exemplo de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as time series identifiers for training.
time_series_id_column_names = ['Store', 'Brand']
nseries = data.groupby(time_series_id_column_names).ngroups

# View the number of time series data with defined time series identifiers
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'time_series_id_column_names': time_series_id_column_names,
    'drop_column_names': ['logQuantity'],
    'forecast_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a> Configuração do Ensemble

Os modelos de Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução de machine learning automatizada. Atualmente, os métodos de ensemble com suporte são votação e empilhamento. A votação é implementada como votação suave usando médias ponderadas, e a implementação de empilhamento usa uma implementação de duas camadas, em que os modelos da primeira camada são iguais ao do ensemble de votação, e o da segunda camada é usado para encontrar a combinação ideal dos modelos da primeira camada. Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitada, o empilhamento será desabilitado e somente a votação será utilizada.

Há vários argumentos padrão que podem ser fornecidos como `kwargs` em um objeto `AutoMLConfig` para alterar o comportamento padrão de ensemble.

* `ensemble_download_models_timeout_sec`: Durante **VotingEnsemble** e geração de modelo de **StackEnsemble**, vários modelos ajustados das execuções filho anteriores são baixados. Se você encontrar esse erro: `AutoMLEnsembleException: Could not find any models for running ensembling`, talvez seja necessário esperar mais para que os modelos sejam baixados. O valor padrão é 300 segundos para baixar esses modelos em paralelo e não o tempo limite máximo não é limitado. Configure esse parâmetro com um valor maior que 300 segundos, se for necessário mais tempo. 

  > [!NOTE]
  >  Se o tempo limite for atingido e houver modelos baixados, o ensembling continuará com quantos modelos foram baixados. Não é necessário que todos os modelos sejam baixados para concluir dentro desse tempo limite.

Os parâmetros a seguir se aplicam somente a modelos de **StackEnsemble**: 

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-aprendizes padrão são `LogisticRegression` para tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver habilitada) e `ElasticNet` para tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver habilitada). Esse parâmetro pode ser uma das seguintes cadeias de caracteres: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.

* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher treinar e tipo de validação de treinamento) a ser reservado para treinar o meta-aprendiz. O valor padrão é `0.2`. 

* `stack_meta_learner_kwargs`: parâmetros opcionais a serem passados para o inicializador do meta-aprendiz. Esses parâmetros e tipos de parâmetro espelham os parâmetros e os tipos de parâmetro do construtor de modelo correspondente e são encaminhados para o construtor de modelo.

O código a seguir mostra um exemplo de especificação do comportamento de ensemble personalizado em um objeto `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

O treinamento do ensemble é habilitado por padrão, mas pode ser desabilitado usando os parâmetros boolianos `enable_voting_ensemble` e `enable_stack_ensemble`.

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

Para ML automatizado, você pode criar um objeto `Experiment`, que é um objeto nomeado em um `Workspace` usado para executar experimentos.

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

### <a name="exit-criteria"></a><a name="exit"></a> Critérios de saída

Há algumas opções que você pode definir para encerrar seu experimento.
1. Sem critérios: Se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Sair após um período de tempo: Usar `experiment_timeout_minutes` em suas configurações permite definir o tempo em minutos que um experimento continuará em execução.
1. Sair depois que uma pontuação for alcançada: O uso de `experiment_exit_score` concluirá o experimento depois que uma pontuação de métrica primária for atingida.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Será possível exibir os resultados de treinamento em um widget ou embutidos se você estiver usando um notebook. Confira [Track and evaluate models](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) (Rastrear e avaliar modelos) para obter mais detalhes.

Para obter detalhes sobre como baixar ou registrar um modelo para implantação em um serviço Web, consulte [como e onde implantar um modelo](how-to-deploy-and-where.md).

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizado

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia de recursos automatizada (se `"featurization": 'auto'`)
+ Escalamento/Normalização e algoritmo com valores de hiperparâmetro

Tornamos transparente para obter essas informações da saída de fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia de recursos automatizada

Consulte a lista de pré-processamento e [engenharia de recursos automatizada]() que ocorre quando `"featurization": 'auto'`.

Considere este exemplo:
+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (datetime)
+ O recurso numérico C é removido porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputados pela média
+ O recurso datetime D é destacado em 11 diferentes recursos de engenharia

Use essas duas APIs na primeira etapa do modelo ajustado para entender melhor.  Veja [este notebook de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos de engenharia.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Essa lista inclui todos os nomes de recursos de engenharia.

  >[!Note]
  >Use “timeseriestransformer” para task=”forecasting”, caso contrário, use “datatransformer” para a tarefa “regression” ou “classification”.

+ API 2: `get_featurization_summary()` retorna o resumo de definição de recursos para todos os recursos de entrada.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use “timeseriestransformer” para task=”forecasting”, caso contrário, use “datatransformer” para a tarefa “regression” ou “classification”.

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
   |Dropped|Indica se o recurso de entrada foi removido ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Escalamento/Normalização e algoritmo com valores de hiperparâmetro:

Para entender os valores de escalamento/normalização e de algoritmo/hiperparâmetro para um pipeline, use fitted_model.steps. [Saiba mais sobre escalamento/normalização](how-to-configure-auto-features.md). Aqui está uma amostra de saída:

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

A saída de exemplo a seguir é para um pipeline que usa um algoritmo específico (LogisticRegression com RobustScalar, nesse caso).

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

Os modelos produzidos usando o ML automatizado têm objetos wrapper que espelham a funcionalidade de sua classe de origem open-source. A maioria dos objetos do wrapper do modelo de classificação retornados pelo ML automatizado implementam a função `predict_proba()`, que aceita uma amostra de dados de matriz ou de matriz esparsa dos seus recursos (valores X) e retorna uma matriz n-dimensional de cada amostra e sua respectiva probabilidade de classe.

Supondo que você tenha recuperado a melhor execução e o modelo ajustado usando as mesmas chamadas acima, você pode chamar `predict_proba()` diretamente do modelo ajustado, fornecendo uma amostra de `X_test` no formato apropriado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não oferecer suporte à função `predict_proba()` ou o formato estiver incorreto, uma exceção específica para classe de modelo será lançada. Consulte os documentos de referência de [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para obter exemplos de como essa função é implementada para diferentes tipos de modelo.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilidade do modelo

A interpretabilidade de modelo permite entender por que seus modelos fizeram previsões e os valores de importância do recurso subjacentes. O SDK inclui vários pacotes para habilitar recursos de interpretação de modelo, tanto no tempo de inferência quanto no treinamento, para modelos locais e implantados.

Consulte [instruções](how-to-machine-learning-interpretability-automl.md) para ver exemplos de código para habilitar recursos de interpretação especificamente em experimentos de machine learning automatizado.

Para obter informações gerais sobre como as explicações de modelo e a importância do recursos podem ser habilitadas em outras áreas do SDK fora do machine learning automatizado, consulte o artigo [conceito](how-to-machine-learning-interpretability.md) sobre a interpretabilidade.

> [!NOTE]
> O modelo ForecastTCN não tem suporte no momento pelo cliente de explicação. Esse modelo não retornará um painel de explicação se ele for retornado como o melhor modelo e não oferecer suporte a execuções de explicação sob demanda.

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

+ Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
+ Saiba como treinar vários modelos com o autoML no [Acelerador de solução de muitos modelos](https://aka.ms/many-models).
