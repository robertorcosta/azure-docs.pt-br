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
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: 6d05f5fa20b5deee14b1a2fada389d869d48908a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709080"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizado no Python


Neste guia, vamos mostrar como definir várias configurações dos seus experimentos de machine learning automatizado com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, consulte [tutorial: treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [treinar modelos com o aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Classificação, regressão ou previsão de série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se preferir um experimento sem código, você também pode [Criar seus experimentos de machine learning automatizado no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa, 
* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

* O SDK do Azure Machine Learning Python instalado.
    Para instalar o SDK, você pode, 
    * Crie uma instância de computação, que instala automaticamente o SDK e é pré-configurado para fluxos de trabalho de ML. Consulte [criar e gerenciar uma instância de computação Azure Machine Learning](how-to-create-manage-compute-instance.md) para obter mais informações. 

    * [Instale o SDK por conta própria](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). Apenas não se esqueça de incluir o `automl` extra. 

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O Machine Learning automatizado oferece suporte a tipos de tarefas do `classification` , `regression` e `forecasting` . Saiba mais sobre [tipos de tarefas](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

O código a seguir usa o `task` parâmetro no `AutoMLConfig` construtor para especificar o tipo de experimento como `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um **DataFrame do Pandas** ou em um **TabularDataset do Azure Machine Learning**. [Saiba mais sobre conjuntos de dados](how-to-create-register-datasets.md).

Requisitos para dados de treinamento:
- Os dados devem estar em formato de tabela.
- O valor que você quer prever, coluna de destino, deve estar presente nos dados.

**Para experimentos remotos**, os dados de treinamento devem ser acessíveis a partir da computação remota. AutoML só aceita [Azure Machine Learning TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) ao trabalhar em uma computação remota. 

Azure Machine Learning conjuntos de valores expõem a funcionalidade para:

* Transfira facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho.
* Disponibilize seus dados para treinar scripts ao executar recursos de computação em nuvem. Confira [como treinar com conjuntos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) de dados para obter um exemplo de como usar a `Dataset` classe para montar o seu destino de computação remota.

O código a seguir cria um TabularDataset de uma URL da Web. Consulte [criar um TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) para obter exemplos de código sobre como criar conjuntos de itens de outras fontes, como arquivos locais e repositórios de armazenamento.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Para experimentos de computação local**, recomendamos o pandas dataframes para tempos de processamento mais rápidos.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Dados de treinamento, validação e teste

Você pode especificar **conjuntos de validação e de treinamento** separados diretamente no `AutoMLConfig` Construtor. Saiba mais sobre [como configurar divisões de dados e validação cruzada](how-to-configure-cross-validation-data-splits.md) para seus experimentos de AutoML. 

Se você não especificar explicitamente um `validation_data` parâmetro ou `n_cross_validation` , o AutoML aplicará técnicas padrão para determinar como a validação é executada. Essa determinação depende do número de linhas no conjunto de registros atribuído ao seu `training_data` parâmetro. 

|&nbsp;Tamanho dos dados de treinamento &nbsp;| Técnica de validação |
|---|-----|
|**Mais &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A divisão de dados de treinamento/validação é aplicada. O padrão é levar 10% do conjunto de dados de treinamento inicial como o conjunto de validação. Por sua vez, esse conjunto de validação é usado para cálculo de métricas.
|**Menos &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A abordagem de validação cruzada é aplicada. O número padrão de dobras depende do número de linhas. <br> **Se o conjunto de registros for menor que 1.000 linhas**, serão usadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000**, serão usadas três dobras.

Neste momento, você precisa fornecer seus próprios dados de **teste** para avaliação do modelo. Para obter um exemplo de código de como trazer seus próprios dados de teste para avaliação de modelo, consulte a seção de **teste** deste [Notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento automatizado do Machine Learning pode ser executado nas opções de computação a seguir. Conheça os [prós e contras das opções de computação local e remota](concept-automated-ml.md#local-remote) . 

* Seu computador **local** , como uma área de trabalho local ou um laptop, geralmente quando você tem um pequeno conjunto de um e ainda está no estágio de exploração. Consulte [este bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) para obter um exemplo de computação local. 
 
* Um computador **remoto** na nuvem – [Azure Machine Learning computação gerenciada](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. 

    Consulte [este bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) para obter um exemplo remoto usando Azure Machine Learning computação gerenciada. 

* Um **cluster Azure Databricks** na sua assinatura do Azure. Você pode encontrar mais detalhes aqui- [configurar Azure Databricks cluster para o ml automatizado](how-to-configure-environment.md#aml-databricks). Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para obter exemplos de notebooks com Azure Databricks.

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
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. O exemplo a seguir é um experimento de regressão definido para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. Tarefas de previsão exigem configuração adicional, consulte o artigo [treinar automaticamente um modelo de previsão de série temporal](how-to-auto-train-forecast.md) para obter mais detalhes. 

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
    
### <a name="supported-models"></a>Modelos com suporte

O aprendizado de máquina automatizado tenta modelos e algoritmos diferentes durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo. 

Os três `task` valores de parâmetro diferentes determinam a lista de algoritmos ou modelos a serem aplicados. Use os parâmetros `allowed_models` ou `blocked_models` para modificar ainda mais as iterações com os modelos disponíveis a serem incluídos ou excluídos. 

A tabela a seguir resume os modelos com suporte por tipo de tarefa. 

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
[Classificador do Perceptron médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)|[Regressor descendente do gradiente online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest&preserve-view=true) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressor linear rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest&preserve-view=true)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Classificador SVM linear](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)*||

### <a name="primary-metric"></a>Métrica principal
O `primary metric` parâmetro determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis para seleção são determinadas pelo tipo de tarefa que você escolher, e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

Saiba mais sobre as definições específicas dessas métricas em [Entender os resultados do machine learning automatizado](how-to-understand-automated-ml.md).

|classificação | Regressão | Previsão de série temporal
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Definição de recursos de dados

Em todos os experimentos de machine learning automatizado, seus dados são automaticamente escalados e normalizados para ajudar *determinados* algoritmos sensíveis a recursos que estão em escalas diferentes. Esse dimensionamento e normalização são chamados de personalização. Consulte [personalização em AutoML](how-to-configure-auto-features.md#) para obter mais detalhes e exemplos de código. 

Ao configurar seus experimentos em seu `AutoMLConfig` objeto, você pode habilitar/desabilitar a configuração `featurization` . A tabela a seguir mostra as configurações aceitas para personalização no [objeto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de definição de recursos | Descrição |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [verificadores de integridade dos dados e etapas de definição de recursos](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Configuração padrão**.|
|`"featurization": 'off'`| Indica que a etapa personalização não deve ser feita automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que uma etapa de definição de recursos personalizada deve ser usada. [Saiba como personalizar a definição de recursos](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> As etapas de definição de recursos de machine learning automatizado (normalização de recursos, manipulação de dados ausentes, conversão de texto em números, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de definição de recursos aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Configuração do Ensemble

Os modelos Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução AutoML. Atualmente, há suporte para **VotingEnsemble** e **StackEnsemble** . 

A votação implementa a votação suave, que usa médias ponderadas. A implementação de empilhamento usa uma implementação de duas camadas, em que a primeira camada tem os mesmos modelos que o Ensemble de votação, e o segundo modelo de camada é usado para localizar a combinação ideal dos modelos da primeira camada. 

Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitado, o empilhamento será desabilitado e apenas a votação será utilizada.

O treinamento do Ensemble pode ser desabilitado usando os `enable_voting_ensemble` `enable_stack_ensemble` parâmetros Boolianos e.

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

Para alterar o comportamento padrão de Ensemble, há vários argumentos padrão que podem ser fornecidos como `kwargs` em um `AutoMLConfig` objeto.

> [!IMPORTANT]
>  Os parâmetros a seguir não são parâmetros explícitos da classe AutoMLConfig. 

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

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Critérios de saída

Há algumas opções que você pode definir para encerrar seu experimento.

|Critérios| descrição
|----|----
Nenhum &nbsp; critério | Se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum outro progresso seja feito em sua métrica primária.
Após &nbsp; um &nbsp; período &nbsp; de &nbsp; tempo| Use `experiment_timeout_minutes` em suas configurações para definir por quanto tempo, em minutos, seu experimento deve continuar a ser executado. <br><br> Para ajudar a evitar falhas de tempo limite de experimento, há um mínimo de 15 minutos ou 60 minutos se a linha por tamanho de coluna exceder 10 milhões.
Uma &nbsp; Pontuação &nbsp; foi &nbsp; &nbsp; atingida| Usar `experiment_exit_score` concluirá o experimento depois que uma pontuação de métrica primária especificada for atingida.

## <a name="explore-models-and-metrics"></a>Explorar modelos e métricas

Será possível exibir os resultados de treinamento em um widget ou embutidos se você estiver usando um notebook. Confira [Track and evaluate models](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) (Rastrear e avaliar modelos) para obter mais detalhes.

Consulte [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md) para obter definições e exemplos dos gráficos de desempenho e métricas fornecidos para cada execução. 

Para obter um resumo do personalização e entender quais recursos foram adicionados a um modelo específico, consulte [transparência do personalização](how-to-configure-auto-features.md#featurization-transparency). 

## <a name="register-and-deploy-models"></a>Registrar e implantar modelos

Para obter detalhes sobre como baixar ou registrar um modelo para implantação em um serviço Web, consulte [como e onde implantar um modelo](how-to-deploy-and-where.md).

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

+ Saiba como treinar vários modelos com o AutoML no [acelerador de solução de vários modelos](https://aka.ms/many-models).
