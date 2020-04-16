---
title: Construa um aplicativo de aprendizado de máquina com Apache Spark MLlib e Azure Synapse Analytics
description: Aprenda a usar o Apache Spark MLlib para criar um aplicativo de aprendizado de máquina que analisa um conjunto de dados usando a classificação através da regressão logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430000"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Construa um aplicativo de aprendizado de máquina com Apache Spark MLlib e Azure Synapse Analytics

Neste artigo, você aprende a usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar um aplicativo de aprendizado de máquina que faz análises preditivas simples em um conjunto de dados aberto do Azure. A Spark fornece bibliotecas de aprendizado de máquina incorporadas. Este exemplo utiliza *a classificação* através da regressão logística.

MLlib é uma biblioteca core Spark que fornece muitos utilitários que são úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

- classificação
- Regressão
- Clustering
- Modelagem de tópico
- Decomposição de valor singular (SVD) e análise de componente principal (PCA)
- Teste de hipótese e cálculo de estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e regressão logística

*Classificação*, uma tarefa popular de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação descobrir como atribuir *rótulos* aos dados de entrada que você fornece. Por exemplo, você pode pensar em um algoritmo de aprendizado de máquina que aceita informações de ações como entrada e divide o estoque em duas categorias: ações que você deve vender e ações que você deve manter.

*Regressão logística* é um algoritmo que você pode usar para classificação. A API de regressão logística do Spark é útil para *classificação binária*ou para classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre a regressão logística, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exemplo de análise preditiva em dados do NYC Taxi

Neste exemplo, você usa o Spark para realizar algumas análises preditivas sobre dados de dicas de viagem de táxi de Nova York. Os dados estão disponíveis através [do Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o horário de início e término e locais, o custo e outros atributos interessantes.

> [!IMPORTANT]
> Pode haver taxas adicionais para retirar esses dados de seu local de armazenamento.

Nas etapas seguintes, você desenvolve um modelo para prever se uma determinada viagem inclui uma dica ou não.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie um aplicativo de aprendizado de máquina Apache Spark MLlib

1. Crie um notebook usando o kernel PySpark. Para obter as instruções, consulte [Criar um caderno](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook).
2. Importe os tipos obrigatórios necessários para este aplicativo. Copie e cole o seguinte código em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**, ou execute a célula usando o ícone de reprodução azul à esquerda do código.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. O contexto Spark é criado automaticamente para você quando você executa a primeira célula de código.

## <a name="construct-the-input-dataframe"></a>Construir o dataframe de entrada

Como os dados brutos estão em um formato Parquet, você pode usar o contexto Spark para puxar o arquivo para a memória como um dataframe diretamente. Embora o código abaixo use as opções padrão, é possível forçar o mapeamento de tipos de dados e outros atributos de esquema, se necessário.

1. Execute as seguintes linhas para criar um dataframe spark colando o código em uma nova célula. A primeira seção atribui informações de acesso de armazenamento do Azure a variáveis. A segunda seção permite que o Spark leia a partir do armazenamento blob remotamente. A última linha de código lê parquet, mas nenhum dado é carregado neste momento.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Puxar todos esses dados gera cerca de 1,5 bilhão de linhas. Dependendo do tamanho do seu pool spark (visualização), os dados brutos podem ser muito grandes ou levar muito tempo para operar. Você pode filtrar esses dados para algo menor. Se necessário, adicione as seguintes linhas para filtrar os dados em cerca de 2 milhões de linhas para uma experiência mais responsiva. Use esses parâmetros para puxar uma semana de dados.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. A desvantagem da simples filtragem é que, do ponto de vista estatístico, pode introduzir viés nos dados. Outra abordagem é usar a amostragem incorporada ao Spark. O código a seguir reduz o conjunto de dados para cerca de 2000 linhas, se aplicado após o código acima. Esta etapa de amostragem pode ser usada em vez do filtro simples ou em conjunto com o filtro simples.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Agora é possível olhar para os dados para ver o que foi lido. Normalmente é melhor rever dados com um subconjunto em vez do conjunto completo, dependendo do tamanho do conjunto de dados. O código a seguir oferece duas maneiras de visualizar os dados: o primeiro sendo básico e o segundo proporcionando uma experiência de grade muito mais rica, bem como a capacidade de visualizar os dados graficamente.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Dependendo do tamanho do conjunto de dados gerado e da necessidade de experimentar ou executar o notebook muitas vezes, pode ser aconselhável fazer cache do conjunto de dados localmente no espaço de trabalho. Existem três maneiras de fazer o cache explícito:

   - Salvar o dataframe localmente como um arquivo
   - Salve o dataframe como uma tabela ou exibição temporária
   - Salve o dataframe como uma tabela permanente

As duas primeiras dessas abordagens estão incluídas nos seguintes exemplos de código.

A criação de uma tabela ou exibição temporária fornece diferentes caminhos de acesso aos dados, mas dura apenas a duração da sessão de instância spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Entender os dados

Normalmente você passaria por uma fase de *análise exploratória* de dados (EDA) neste momento para desenvolver uma compreensão dos dados. O código a seguir mostra três visualizações diferentes dos dados relacionados a dicas que levam a conclusões sobre o estado e a qualidade dos dados.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Enredo de](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![dispersão](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![de plotagem de estola da caixa de histograma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparando os dados

Os dados em sua forma bruta são freqüentemente inadequados para passar diretamente para um modelo. Uma série de ações devem ser realizadas nos dados para colocá-los em um estado onde o modelo possa consumi-lo.

No código abaixo são realizadas quatro classes de operações:

- A remoção de outliers/valores incorretos através da filtragem.
- A remoção de colunas, que não são necessárias.
- A criação de novas colunas derivadas dos dados brutos para fazer o modelo funcionar de forma mais eficaz, às vezes chamada de featurização.
- Rotulagem, como você está empreendendo classificação binária (haverá uma dica ou não em uma determinada viagem) há a necessidade de converter o valor da gorjeta em um valor 0 ou 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Uma segunda passagem é feita sobre os dados para adicionar os recursos finais.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística

A tarefa final é converter os dados rotulados para um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares de vetores de característica de rótulo,* onde o *vetor de recurso* é um vetor de números representando o ponto de entrada. Então, precisamos converter as colunas categóricas em números. As `trafficTimeBins` `weekdayString` colunas e colunas precisam ser convertidas em representações inteiras. Existem múltiplas abordagens para realizar a conversão, no entanto, a abordagem tomada neste exemplo é *oneHotEncoding*, uma abordagem comum.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Isso resulta em um novo dataframe com todas as colunas no formato certo para treinar um modelo.

## <a name="train-a-logistic-regression-model"></a>Treinar um modelo de regressão logística

A primeira tarefa é dividir o conjunto de dados em um conjunto de treinamento e um conjunto de testes ou validação. A divisão aqui é arbitrária e você deve brincar com diferentes configurações divididas para ver se elas impactam o modelo.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Agora que existem dois DataFrames, a próxima tarefa é criar a fórmula do modelo e executá-la contra o DataFrame de treinamento e, em seguida, validar contra o DataFrame de teste. Você deve experimentar diferentes versões da fórmula do modelo para ver o impacto de diferentes combinações.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

A saída desta célula é

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão

Agora você pode construir uma visualização final para ajudar a justificar os resultados deste teste. Uma [Curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) é uma maneira de rever o resultado.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Roc Curve para modelo de ponta de regressão logística](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Roc Curve para modelo de ponta de regressão logística")

## <a name="shut-down-the-spark-instance"></a>Desligá-lo a instância de Faísca

Depois de terminar de executar o aplicativo, desligue o notebook para liberar os recursos fechando a guia ou selecione **Sessão** final no painel de status na parte inferior do notebook.

## <a name="see-also"></a>Confira também

- [Visão geral: Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Próximas etapas

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial do Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Alguns dos documentos oficiais do Apache Spark dependem do uso do console Spark, que não está disponível no Azure Synapse Spark. Use as experiências do [notebook](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou [da IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
