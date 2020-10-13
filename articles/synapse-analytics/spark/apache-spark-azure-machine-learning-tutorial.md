---
title: 'Tutorial: Executar experimentos usando o ML Automatizado do Azure'
description: Um tutorial sobre como executar experimentos de machine learning usando o Apache Spark e o ML Automatizado do Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: da4cef50610b219689e2271e9f70fd1adb1a235f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540499"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>Tutorial: Executar experimentos usando o ML Automatizado do Azure e o Apache Spark

O Azure Machine Learning é um ambiente baseado em nuvem que permite treinar, implantar, automatizar, gerenciar e acompanhar modelos de machine learning. 

Neste tutorial, você usará o [machine learning automatizado](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) no Azure Machine Learning para criar um modelo de regressão a fim de prever preços de tarifa de táxi na cidade de Nova York. Esse processo aceita dados de treinamento e configurações e itera automaticamente por meio de combinações de configurações de hiperparâmetro, modelos e métodos de normalização/padronização de diferentes recursos para chegar ao melhor modelo.

Neste tutorial, você aprende as seguintes tarefas:
- Baixar os dados usando o Apache Spark e o Azure Open Datasets
- Transformar e limpar dados usando dataframes do Apache Spark
- Treinar um modelo de regressão com aprendizado de máquina automatizado
- Calcular a precisão do modelo

### <a name="before-you-begin"></a>Antes de começar
- Crie um Pool do Apache Spark seguindo o [tutorial Criar um Pool do Apache Spark](../quickstart-create-apache-spark-pool-studio.md).
- Conclua o [tutorial de instalação do workspace do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) caso você não tenha um workspace do Azure Machine Learning. 

### <a name="understand-regression-models"></a>Entender os modelos de regressão
*Os modelos de regressão* preveem valores de saída numéricos com base em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis de preditores independentes estimando como uma variável afeta as outras.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Exemplo de análise de regressão nos dados de táxi de Nova York
Neste exemplo, você usará o Spark para realizar algumas análises nos dados de gorjeta das corridas de táxi de Nova York. Os dados estão disponíveis por meio dos [Conjuntos de Dados em Aberto no Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Esse subconjunto do conjunto de dados contém informações sobre as corridas de táxi amarelo, incluindo informações sobre cada corrida, a hora de início e de término e os locais, o custo e outros atributos interessantes.

> [!IMPORTANT]
> 
> Pode haver encargos adicionais para a extração desses dados no local de armazenamento. Nas etapas a seguir, você desenvolverá um modelo para prever preços de tarifa de táxi de Nova York. 
> 

##  <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados

1. Crie um bloco de notas usando o kernel PySpark. Para obter instruções, confira [Criar um Notebook](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. O contexto do Spark é criado automaticamente para você ao executar a primeira célula do código.
   >

2. Como os dados brutos estão no formato Parquet, você pode usar o contexto do Spark para extrair o arquivo diretamente na memória como um dataframe. Crie um dataframe do Spark recuperando os dados por meio da API do Conjunto de Dados em Aberto no Azure. Aqui, usaremos as propriedades do dataframe do Spark *schema on read* para inferir os tipos de dados e o esquema. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. Dependendo do tamanho do pool do Spark (versão prévia), os dados brutos podem ser muito grandes ou levar muito tempo para a operação. Você pode filtrar esses dados para obter algo menor usando os filtros ```start_date``` e ```end_date```. Isso aplica um filtro que retorna dados de um mês. Assim que filtrarmos o dataframe, executaremos a função ```describe()``` no novo dataframe para ver estatísticas resumidas de cada campo. 

   Com base nas estatísticas resumidas, vemos que há algumas irregularidades e exceções nos dados. Por exemplo, as estatísticas mostram que a distância mínima de viagem é menor que zero. Será necessário filtrar esses pontos de dados irregulares.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Agora, vamos gerar recursos do conjunto de dados selecionando um conjunto de colunas e criando vários recursos baseados em tempo relativos ao campo datetime do início da corrida. Também vamos filtrar as exceções que foram identificadas na etapa anterior e remover as últimas colunas, que são desnecessárias para o treinamento.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
Veja que isso criará um novo dataframe com colunas adicionais para o dia do mês, a hora de início da corrida, o dia da semana e o tempo total de viagem. 

![Figura do dataframe do táxi.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Gerar conjuntos de dados de testes e de validação

Quando tivermos nosso conjunto de dados final, poderemos dividir os dados em conjuntos de treinamento e de teste usando a função ```random_ split ``` do Spark. Usando os pesos fornecidos, essa função dividirá aleatoriamente os dados em: conjunto de dados de treinamento para o treinamento do modelo e no conjunto de dados de validação para teste.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Essa etapa garante que os pontos de dados testem o modelo concluído que não foi usado para treinar o modelo. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Conectar a um workspace do Azure Machine Learning
No Azure Machine Learning, um **Workspace** é uma classe que aceita suas informações de recursos e assinatura do Azure. Ele também cria um recurso de nuvem para monitorar e acompanhar a execução do seu modelo. Nesta etapa, vamos criar um objeto de workspace do Azure Machine Learning existente.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Converter um dataframe em um conjunto de dados do Azure Machine Learning
Para enviar um experimento remoto, precisaremos converter nosso conjunto de dados em um ```TabularDatset``` do Azure Machine Learning. Um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular, com base na análise dos arquivos fornecidos.

O código a seguir obtém o workspace existente e o armazenamento de dados padrão do Azure Machine Learning. Em seguida, ele passa o armazenamento de dados e os locais de arquivo para que o parâmetro de caminho crie um ```TabularDataset```. 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Imagem do conjunto de dados carregado.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Enviar um experimento de ML Automático

#### <a name="define-training-settings"></a>Definir configurações de treinamento

1. Para enviar um experimento, precisaremos definir as configurações de modelos e os parâmetros do experimento para o treinamento. Você pode exibir a lista completa de configurações [aqui](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. Agora, passaremos as configurações de treinamento definidas como um parâmetro \*\*kwargs para um objeto AutoMLConfig. Como estamos treinando no Spark, também precisaremos passar o Contexto do Spark, que é acessado automaticamente pela variável ```sc```. Além disso, especificaremos os dados de treinamento e o tipo de modelo, que é a regressão nesse caso.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

#### <a name="train-the-automatic-regression-model"></a>Treinar o modelo de regressão automática 
Agora, criaremos um objeto de experimento no workspace do Azure Machine Learning. Um experimento atua como um contêiner para suas execuções individuais. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Assim que o experimento for concluído, a saída retornará detalhes sobre as iterações realizadas. Para cada iteração, você vê o tipo de modelo, a duração da execução e a precisão do treinamento. O campo BEST monitora a melhor pontuação de treinamento em execução com base no tipo de métrica.

![Captura de tela da saída do modelo.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Depois de enviado, o experimento do AutoML executará várias iterações e tipos de modelo. Essa execução normalmente levará de 1 a 1,5 horas. 

#### <a name="retrieve-the-best-model"></a>Recuperar o melhor modelo
Para selecionar o melhor modelo das iterações, usaremos a função ```get_output``` para retornar o melhor modelo executado e ajustado. O código abaixo vai recuperar o modelo de melhor execução e ajuste para qualquer métrica registrada em log ou uma iteração específica.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Testar a precisão do modelo

1. Para testar a precisão do modelo, usaremos o melhor modelo para executar previsões de tarifa de táxi no conjunto de dados de teste. A função ```predict``` usa o melhor modelo e prevê os valores de y (valor da tarifa) com base no conjunto de dados de validação. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. A REQM (raiz do erro quadrático médio) é uma medida usada com frequência das diferenças entre os valores da amostra previstos por um modelo e os valores observados. Calcularemos a raiz do erro quadrático médio dos resultados comparando o dataframe y_test com os valores previstos pelo modelo. 

   A função ```mean_squared_error``` usa duas matrizes e calcula o erro médio quadrático entre elas. Em seguida, obtemos a raiz quadrada do resultado. Essa métrica indica aproximadamente o quão distantes as previsões das tarifas de táxi estão dos valores das tarifas reais.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   A raiz do erro quadrático médio é uma boa medida da precisão da previsão da resposta do modelo. Nos resultados você vê que o modelo é razoavelmente bom em prever as tarifas de táxi com os recursos do conjunto de dados, normalmente dentro de +- $ 2,00

3. Execute o código a seguir para calcular o MAPE (desvio percentual absoluto médio). Essa métrica expressa a precisão como um percentual do erro. Ela faz isso calculando uma diferença absoluta entre cada valor previsto e real, somando todas as diferenças depois. Em seguida, ela expressa essa soma como um percentual do total de valores reais.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Nas duas métricas de precisão da previsão, você vê que o modelo é razoavelmente bom em prever as tarifas de táxi por meio dos recursos do conjunto de dados. 

4. Depois de ajustar um modelo de regressão linear, agora precisaremos determinar se o modelo se ajusta bem aos dados. Para fazer isso, faremos um gráfico dos valores reais de tarifa em relação à saída prevista. Além disso, também calcularemos a medida R-quadrado para entender a proximidade dos dados com a linha de regressão ajustada.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Captura de tela do gráfico de regressão.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Com base nos resultados, podemos ver que a medida R-quadrado corresponde a 95% da nossa variância. Isso também é validado pelo gráfico do real versus observado. Quanto mais a variância for explicada pelo modelo de regressão, mais próximos os pontos de dados ficarão da linha de regressão ajustada.  

## <a name="register-model-to-azure-machine-learning"></a>Registrar o modelo no Azure Machine Learning
Depois de validarmos nosso melhor modelo, podemos registrar o modelo no Azure Machine Learning. Após registrar o modelo, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que registrou.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Exibir resultados no Azure Machine Learning
Por fim, você também pode acessar os resultados das iterações navegando até o experimento no workspace do Azure Machine Learning. Lá você poderá se aprofundar em mais detalhes sobre o status da execução, as tentativas de modelos e outras métricas do modelo. 

![Captura de tela do workspace do AML.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Próximas etapas
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Tutorial da MLlib do Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
