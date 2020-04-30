---
title: Criar um aplicativo de Machine Learning com o Apache Spark MLlib e o Azure Synapse Analytics
description: Saiba como usar Apache Spark MLlib para criar um aplicativo de Machine Learning que analisa um conjunto de informações usando a classificação por meio de regressão logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096292"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Criar um aplicativo de Machine Learning com o Apache Spark MLlib e o Azure Synapse Analytics

Neste artigo, você aprenderá a usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar um aplicativo de aprendizado de máquina que realiza uma análise preditiva simples em um conjunto de um DataSet aberto do Azure. O Spark fornece bibliotecas de aprendizado de máquina internas. Este exemplo usa *classificação* por meio de regressão logística.

O MLlib é uma biblioteca principal do Spark que fornece muitos utilitários que são úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

- classificação
- Regressão
- Clustering
- Modelagem de tópico
- Decomposição de valor singular (SVD) e análise de componente principal (PCA)
- Teste de hipótese e cálculo de estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e regressão logística

*Classificação*, uma tarefa popular de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação descobrir como atribuir *Rótulos* aos dados de entrada que você fornecer. Por exemplo, você pode considerar um algoritmo de aprendizado de máquina que aceita informações de ações como entrada e divide o estoque em duas categorias: ações que você deve vender e ações que devem ser mantidas.

A *regressão logística* é um algoritmo que você pode usar para classificação. A API de regressão logística do Spark é útil para *classificação binária*ou para classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre a regressão logística, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exemplo de análise preditiva em dados de táxi de NYC

Neste exemplo, você usa o Spark para executar uma análise preditiva em dados de gorjeta de corrida de táxi de Nova York. Os dados estão disponíveis por meio [do Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Esse subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelo, incluindo informações sobre cada corrida, a hora de início e de término e os locais, o custo e outros atributos interessantes.

> [!IMPORTANT]
> Pode haver encargos adicionais para a extração desses dados de seu local de armazenamento.

Nas etapas a seguir, você desenvolverá um modelo para prever se uma corrida específica inclui uma gorjeta ou não.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie um aplicativo de aprendizado de máquina Apache Spark MLlib

1. Crie um bloco de anotações usando o kernel PySpark. Para obter instruções, consulte [criar um bloco de anotações](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importe os tipos obrigatórios necessários para este aplicativo. Copie e cole o código a seguir em uma célula vazia e pressione **Shift + Enter**ou execute a célula usando o ícone de reprodução azul à esquerda do código.

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

    Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. O contexto do Spark é criado automaticamente para você quando você executa a primeira célula de código.

## <a name="construct-the-input-dataframe"></a>Construir o dataframe de entrada

Como os dados brutos estão em um formato parquet, você pode usar o contexto do Spark para efetuar pull do arquivo na memória como um dataframe diretamente. Embora o código a seguir use as opções padrão, é possível forçar o mapeamento de tipos de dados e outros atributos de esquema, se necessário.

1. Execute as linhas a seguir para criar um dataframe do Spark colando o código em uma nova célula. A primeira seção atribui informações de acesso do armazenamento do Azure a variáveis. A segunda seção permite que o Spark Leia a partir do armazenamento de BLOBs remotamente. A última linha de código lê parquet, mas nenhum dado é carregado neste ponto.

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

2. A extração de todos esses dados gera cerca de 1.500.000.000 linhas. Dependendo do tamanho do pool do Spark (versão prévia), os dados brutos podem ser muito grandes ou levar muito tempo para operar. Você pode filtrar esses dados para algo menor. Se necessário, adicione as seguintes linhas para filtrar os dados para cerca de 2 milhões linhas para uma experiência mais responsiva. Use esses parâmetros para efetuar pull de uma semana de dados.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. A desvantagem da filtragem simples é que, de uma perspectiva estatística, ela pode apresentar tendência nos dados. Outra abordagem é usar a amostragem incorporada ao Spark. O código a seguir reduz o conjunto de informações para cerca de 2000 linhas, se aplicado após o código acima. Essa etapa de amostragem pode ser usada em vez do filtro simples ou em conjunto com o filtro simples.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Agora é possível examinar os dados para ver o que foi lido. Normalmente, é melhor examinar os dados com um subconjunto, em vez do conjunto completo, dependendo do tamanho do DataSet. O código a seguir oferece duas maneiras de exibir os dados: o primeiro sendo básico e o último que fornece uma experiência de grade muito mais rica, bem como a capacidade de visualizar os dados graficamente.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Dependendo do tamanho do conjunto de espaço de trabalho gerado e da necessidade de experimentar ou executar o bloco de anotações muitas vezes, pode ser aconselhável armazenar em cache localmente o conjunto de espaço de trabalho. Há três maneiras de executar o cache explícito:

   - Salvar o dataframe localmente como um arquivo
   - Salvar o dataframe como uma tabela ou exibição temporária
   - Salvar o dataframe como uma tabela permanente

As duas primeiras abordagens são incluídas nos exemplos de código a seguir.

A criação de uma tabela ou exibição temporária fornece caminhos de acesso diferentes para os dados, mas só dura a duração da sessão de instância do Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Entender os dados

Normalmente, você passaria por uma fase de *análise de dados exploratórios* (EDA) neste ponto para desenvolver uma compreensão dos dados. O código a seguir mostra três visualizações diferentes dos dados relacionados a dicas que levam a conclusões sobre o estado e a qualidade dos dados.

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

![Gráfico](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![de dispersão de](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![gráfico de caixa de histograma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparando os dados

Os dados em sua forma bruta geralmente não são adequados para passar diretamente para um modelo. Uma série de ações deve ser executada nos dados para colocá-lo em um estado em que o modelo possa consumi-lo.

No código abaixo, quatro classes de operações são executadas:

- A remoção de exceções/valores incorretos por meio da filtragem.
- A remoção de colunas, que não são necessárias.
- A criação de novas colunas derivadas dos dados brutos para tornar o modelo funcionar com mais eficiência, às vezes chamado de personalização.
- Rotulagem, como você está executando a classificação binária (haverá uma gorjeta ou não em uma determinada viagem), há a necessidade de converter o valor da gorjeta em um valor 0 ou 1.

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

Em seguida, uma segunda passagem é feita sobre os dados para adicionar os recursos finais.

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

A tarefa final é converter os dados rotulados para um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares de vetor de recurso de rótulo*, em que o *vetor de recurso* é um vetor de números que representa o ponto de entrada. Portanto, precisamos converter as colunas categóricas em números. As `trafficTimeBins` colunas `weekdayString` e precisam ser convertidas em representações de inteiros. Há várias abordagens para executar a conversão, no entanto, a abordagem adotada neste exemplo é *OneHotEncoding*, uma abordagem comum.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Isso resulta em um novo dataframe com todas as colunas no formato correto para treinar um modelo.

## <a name="train-a-logistic-regression-model"></a>Treinar um modelo de regressão logística

A primeira tarefa é dividir o conjunto de um conjunto de treinamento e um conjunto de teste ou validação. A divisão aqui é arbitrária e você deve brincar com diferentes configurações de divisão para ver se elas afetam o modelo.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Agora que há dois quadros de moldura, a próxima tarefa é criar a fórmula do modelo e executá-la no quadro de testes de treinamento e, em seguida, validar em relação ao dataframe de teste. Você deve fazer experiências com versões diferentes da fórmula do modelo para ver o impacto de diferentes combinações.

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

Agora você pode construir uma visualização final para ajudar a justificar os resultados deste teste. Uma [curva Roc](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) é uma maneira de examinar o resultado.

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

![Curva ROC para modelo de gorjeta de regressão logística](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC para modelo de gorjeta de regressão logística")

## <a name="shut-down-the-spark-instance"></a>Desligar a instância do Spark

Depois de concluir a execução do aplicativo, desligue o bloco de anotações para liberar os recursos fechando a guia ou selecione **encerrar sessão** no painel de status na parte inferior do bloco de anotações.

## <a name="see-also"></a>Confira também

- [Visão geral: Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Próximas etapas

- [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial do Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Algumas das documentações oficiais do Apache Spark dependem do uso do console do Spark, que não está disponível no Azure Synapse Spark. Use as experiências de [notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou do [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
