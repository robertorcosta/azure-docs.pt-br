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
ms.openlocfilehash: 11f52df683b7a85a8ad5163bbede33d786897452
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257259"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizado em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, saiba como definir várias configurações de seus experimentos automatizados de aprendizado de máquina com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para ver exemplos de experiências automatizadas de aprendizado de máquina, consulte [Tutorial: Treine um modelo de classificação com](tutorial-auto-train-models.md) aprendizado de máquina automatizado ou [modelos de Trem com aprendizado de máquina automatizado na nuvem.](how-to-auto-train-remote.md)

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione seu tipo de experimento: Classificação, Regressão ou Previsão de Séries tempomaior
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se você preferir uma experiência sem código, você também pode [criar seus experimentos automatizados de aprendizado de máquina no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O aprendizado automatizado de máquina suporta tipos de tarefas de classificação, regressão e previsão. Saiba mais sobre [os tipos de tarefas](how-to-define-task-type.md).

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.

> [!NOTE]
> Se você planeja exportar seus modelos criados por ML automáticos para um [modelo ONNX,](concept-onnx.md)apenas os algoritmos indicados com um * podem ser convertidos para o formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observe também que o ONNX só suporta tarefas de classificação e regressão neste momento. 

classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão Logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede Elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Leve GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Leve GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Lars Laço](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação vetorial de suporte (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores Extremamente Aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores Extremamente Aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Baías Ingênuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressor Linear Rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Regressor de descida de gradiente on-line](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Classificador Perceptron Médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classificador Linear SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Use `task` o parâmetro `AutoMLConfig` no construtor para especificar seu tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um **DataFrame pandas** ou em **um Azure Machine Learning TabularDataset**.  [Saiba mais sobre conjuntos de dados](how-to-create-register-datasets.md).

Requisitos para dados de treinamento:
- Os dados devem estar em forma tabular.
- O valor para prever, coluna-alvo, deve estar nos dados.

Os exemplos de código a seguir demonstram como armazenar os dados nesses formatos.

* Conjunto tabularDataset

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

Para execuções remotas, os dados de treinamento devem ser acessíveis a partir da computação remota. A [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe no SDK expõe a funcionalidade a:

* facilmente transferir dados de arquivos estáticos ou fontes de URL para o seu espaço de trabalho
* disponibilizar seus dados para scripts de treinamento ao executar em recursos de computação em nuvem

Veja [o como fazer](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) para um `Dataset` exemplo de usar a classe para montar dados no seu alvo de computação.

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar conjuntos separados de trem e validação diretamente no `AutoMLConfig` construtor.

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada de Monte Carlo (subamostragem aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo não é suportado para previsão de séries tempois.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use conjunto de dados de validação personalizado se a divisão aleatória não for aceitável, geralmente dados de séries temporias ou dados desequilibrados. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
* O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
* Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para exemplos de notebooks com alvos de computação local e remoto.

* Um cluster Azure Databricks em sua assinatura do Azure. Você pode encontrar mais detalhes aqui - [Setup Azure Databricks cluster para Automated ML](how-to-configure-environment.md#azure-databricks)

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para exemplos de notebooks com tijolos de dados do Azure.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experimento de classificação utilizando AUC ponderado como métrica primária com minutos de tempo limite de experimento definidos para 30 minutos e 2 dobras de validação cruzada.

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

Os três `task` diferentes valores de parâmetro `forecasting`(o terceiro tipo `regression` de tarefa é , e usa um pool de algoritmos semelhante como tarefas) determinam a lista de modelos a serem aplicados. Use `whitelist` os `blacklist` parâmetros ou para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos suportados pode ser encontrada na [Classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([Classificação,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [Regressão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Para ajudar a evitar falhas no tempo limite do `experiment_timeout_minutes` experimento, o serviço de validação do ML automatizado exigirá que seja definido como um mínimo de 15 minutos, ou 60 minutos se a sua linha por tamanho de coluna exceder 10 milhões.

### <a name="primary-metric"></a>Métrica principal
A métrica principal determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis que você pode selecionar são determinadas pelo tipo de tarefa escolhida, e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

|classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Conheça as definições específicas dessas métricas em [Entender resultados automatizados de aprendizado de máquina](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Featurização de dados

Em cada experimento automatizado de aprendizado de máquina, seus dados são [automaticamente dimensionados e normalizados](concept-automated-ml.md#preprocess) para ajudar *certos* algoritmos que são sensíveis a recursos que estão em escalas diferentes.  No entanto, você também pode habilitar featurização adicional, como imputação de valores ausentes, codificação e transformações. [Saiba mais sobre o que inclui a featurização.](how-to-use-automated-ml-for-ml-models.md#featurization)

Ao configurar seus experimentos, você pode `featurization`habilitar a configuração avançada . A tabela a seguir mostra as configurações aceitas para a featurização na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuração de featurização | Descrição |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica passo de featurização personalizado deve ser usado. [Aprenda a personalizar a featurização.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Indica que a etapa de featurização não deve ser feita automaticamente.|
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [guardrails de dados e etapas de featurização](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) são realizados automaticamente.|

> [!NOTE]
> As etapas automatizadas de featurização de aprendizado de máquina (normalização de recursos, manuseio de dados ausentes, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de featurização aplicadas durante o treinamento são aplicadas automaticamente aos seus dados de entrada.

### <a name="time-series-forecasting"></a>Previsão de série temporal
A tarefa `forecasting` da série temporal requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro necessário que define o nome da coluna em seus dados de treinamento contendo uma série de tempo válida.
1. `max_horizon`: Define o tempo que deseja prever com base na periodicidade dos dados de treinamento. Por exemplo, se você tem dados de treinamento com grãos de tempo diários, você define o quão longe em dias você quer que o modelo treine.
1. `grain_column_names`: Define o nome das colunas que contêm dados individuais de séries tempois em seus dados de treinamento. Por exemplo, se você está prevendo vendas de uma determinada marca por loja, você definiria as colunas de loja e marca como suas colunas de grãos. Serão criadas séries de tempo separadas e previsões para cada granulado/agrupamento. 

Para exemplos das configurações utilizadas abaixo, consulte o [caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Configuração do conjunto

Os modelos de conjunto são habilitados por padrão e aparecem como iterações de execução final em uma corrida automatizada de aprendizado de máquina. Atualmente, os métodos de conjunto apoiados são votação e empilhamento. A votação é implementada como voto suave usando médias ponderadas, e a implementação de empilhamento está usando uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos do conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal dos modelos da primeira camada. Se você estiver usando modelos ONNX **ou** tiver a explicação do modelo ativada, o empilhamento será desativado e somente a votação será utilizada.

Existem vários argumentos padrão que `kwargs` podem `AutoMLConfig` ser fornecidos como em um objeto para alterar o comportamento do conjunto de pilha padrão.

* `stack_meta_learner_type`: o meta-aluno é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-alunos `LogisticRegression` padrão são para `LogisticRegressionCV` tarefas de classificação `ElasticNet` (ou se a validação cruzada `ElasticNetCV` estiver ativada) e para tarefas de regressão/previsão (ou se a validação cruzada estiver ativada). Este parâmetro pode ser uma das `LogisticRegression`seguintes strings: `LogisticRegressionCV`, , `LightGBMClassifier`, `ElasticNet` `ElasticNetCV`, , `LightGBMRegressor`ou `LinearRegression`.
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher o tipo de treinamento e validação) a ser reservada para o treinamento do meta-aluno. O valor padrão é `0.2`.
* `stack_meta_learner_kwargs`: parâmetros opcionais para passar para o inicializador do meta-aluno. Esses parâmetros e tipos de parâmetros espelham os parâmetros e os tipos de parâmetros do construtor modelo correspondente, e são encaminhados para o construtor modelo.

O código a seguir mostra um exemplo `AutoMLConfig` de especificação do comportamento do conjunto personalizado em um objeto.

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

O treinamento do conjunto é habilitado por padrão, mas pode ser desativado usando os `enable_voting_ensemble` parâmetros e `enable_stack_ensemble` booleanos.

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

Para ML automatizado, `Experiment` você cria um objeto, `Workspace` que é um objeto nomeado em um usado para executar experimentos.

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
Existem algumas opções que você pode definir para terminar sua experiência.
1. Sem critérios: Se você não definir quaisquer parâmetros de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Saia após um período `experiment_timeout_minutes` de tempo: O uso em suas configurações permite definir quanto tempo em minutos se um experimento continuar em execução.
1. Sair depois que uma pontuação `experiment_exit_score` foi alcançada: O uso completará o experimento depois que uma pontuação métrica primária tiver sido alcançada.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Você pode ver seus resultados de treinamento em um widget ou inline se você estiver em um notebook. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entenda os modelos automatizados ml

Qualquer modelo produzido usando ML automatizado inclui as seguintes etapas:
+ Engenharia automatizada de `"featurization": 'auto'`recursos (se )
+ Dimensionamento/Normalização e algoritmo com valores de hiperparâmetro

Tornamos transparente obter essas informações da saída fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Veja a lista de pré-processamento e `"featurization": 'auto'`engenharia automatizada de [recursos](concept-automated-ml.md#preprocess) que acontece quando .

Considere este exemplo:
+ Existem quatro características de entrada: A (Numeric), B (Numeric), C (Numeric), D (DateTime)
+ Recurso numérico C é descartado porque é uma coluna de identificação com todos os valores únicos
+ Características numéricas A e B têm valores perdidos e, portanto, são imputadas pela média
+ O recurso DateTime D é feito em 11 diferentes recursos projetados

Use essas 2 APIs no primeiro passo do modelo montado para entender mais.  Veja [este caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos projetados.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Esta lista inclui todos os nomes de recursos projetados.

  >[!Note]
  >Use 'timeseriestransformer' para task='forecasting', use 'datatransformer' para tarefa 'regressão' ou 'classificação'.

+ API 2: `get_featurization_summary()` retorna o resumo da featurização para todos os recursos de entrada.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use 'timeseriestransformer' para task='forecasting', use 'datatransformer' para tarefa 'regressão' ou 'classificação'.

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
   |RawFeatureName|Nome do recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Detectou o tipo de dados do recurso de entrada.|
   |Descartada|Indica se o recurso de entrada foi descartado ou usado.|
   |Contagem de características da engenharia|Número de recursos gerados através de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos projetados.|
   
### <a name="customize-feature-engineering"></a>Personalizar a engenharia de recursos
Para personalizar a `"featurization": FeaturizationConfig`engenharia de recursos, especifique .

A personalização suportada inclui:

|Personalização|Definição|
|--|--|
|Atualização de propósito da coluna|Substituir o tipo de recurso para a coluna especificada.|
|Atualização do parâmetro do transformador |Atualize os parâmetros para o transformador especificado. Atualmente suporta Imputer (média, mais freqüente & mediana) e HashOneHotEncoder.|
|Colunas de queda |Colunas para cair de ser featurizada.|
|Transformadores de bloco| Transformadores de bloco saem no processo de featurização.|

Criar o objeto FeaturizationConfig usando chamadas de API:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Dimensionamento/Normalização e algoritmo com valores de hiperparâmetro:

Para entender os valores de escala/normalização e algoritmo/hiperparâmetro para um pipeline, use fitted_model.steps. [Saiba mais sobre escala/normalização](concept-automated-ml.md#preprocess). Aqui está uma amostra de saída:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use esta função auxiliar mostrada [neste caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

A seguinte saída de amostra é para um gasoduto usando um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

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

### <a name="predict-class-probability"></a>Prever a probabilidade de classe

Os modelos produzidos usando ML automatizado têm objetos de invólucro que espelham a funcionalidade de sua classe de origem de código aberto. A maioria dos objetos de wrapper `predict_proba()` do modelo de classificação retornados pelo ML automatizado implementam a função, que aceita uma amostra de dados de matriz semelhante a uma matriz ou esparsa de seus recursos (valores X), e retorna uma matriz n-dimensional de cada amostra e sua respectiva probabilidade de classe.

Supondo que você tenha recuperado o modelo de melhor execução `predict_proba()` e equipado usando as mesmas chamadas de cima, você pode ligar diretamente do modelo montado, fornecendo uma `X_test` amostra no formato apropriado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não `predict_proba()` suportar a função ou o formato estiver incorreto, uma exceção específica da classe do modelo será lançada. Consulte os documentos de referência [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para exemplos de como essa função é implementada para diferentes tipos de modelos.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilidade do modelo

A interpretação do modelo permite que você entenda por que seus modelos fizeram previsões, e os valores de importância do recurso subjacente. O SDK inclui vários pacotes para habilitar recursos de interpretação de modelos, tanto no tempo de treinamento quanto de inferência, para modelos locais e implantados.

Veja [o como fazer](how-to-machine-learning-interpretability-automl.md) amostras de código sobre como habilitar recursos de interpretabilidade especificamente em experimentos automatizados de aprendizado de máquina.

Para obter informações gerais sobre como explicações de modelo e importância de recursos podem ser habilitadas em outras áreas do SDK fora do aprendizado automatizado de máquina, consulte o artigo [conceitual](how-to-machine-learning-interpretability.md) sobre interpretação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou como treinar usando aprendizado de máquina automatizado em um recurso [remoto](how-to-auto-train-remote.md).
