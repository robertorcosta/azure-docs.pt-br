---
title: Criar um aplicativo de aprendizado de máquina com o Apache Spark MLlib e o Azure Synapse Analytics
description: Saiba como usar o Apache Spark MLlib para criar um aplicativo de aprendizado de máquina que analisa um conjunto de dados usando a classificação por meio de regressão logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: fd3637eed35fa4b9f40623612be9fc99703051e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368168"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Criar um aplicativo de aprendizado de máquina com o Apache Spark MLlib e o Azure Synapse Analytics

Neste artigo, você vai saber como usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar um aplicativo de aprendizado de máquina que faz uma análise preditiva simples em um Conjunto de Dados em Aberto do Azure. O Spark fornece bibliotecas de aprendizado de máquina integradas. Este exemplo usa *classificação* por meio de regressão logística.

MLlib é uma biblioteca Spark principal que fornece vários utilitários úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

- classificação
- Regressão
- Clustering
- Modelagem de tópico
- Decomposição de valor singular (SVD) e análise de componente principal (PCA)
- Teste de hipótese e cálculo de estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e regressão logística

*Classificação*, uma tarefa popular de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação para descobrir como atribuir *rótulos* a dados de entrada que você fornece. Por exemplo, você pode pensar em um algoritmo de aprendizado de máquina que aceite informações sobre estoque como entrada e divida o estoque em duas categorias: estoque que você deve vender e estoque que deve ser mantido.

*Regressão logística* é um algoritmo que você pode usar para classificação. A API de regressão logística do Spark é útil para *classificação binária*ou para classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre a regressão logística, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exemplo de análise preditiva em dados de táxi de Nova York

Neste exemplo, você usa o Spark para realizar uma análise preditiva nos dados de gorjeta das corridas de táxi de Nova York. Os dados estão disponíveis por meio dos [Conjuntos de Dados em Aberto no Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Esse subconjunto do conjunto de dados contém informações sobre as corridas de táxi amarelo, incluindo informações sobre cada corrida, a hora de início e de término e os locais, o custo e outros atributos interessantes.

> [!IMPORTANT]
> Pode haver encargos adicionais para a extração desses dados no local de armazenamento.

Nas etapas a seguir, você desenvolverá um modelo para prever se uma corrida específica inclui uma gorjeta ou não.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie um aplicativo de aprendizado de máquina Apache Spark MLlib

1. Crie um bloco de notas usando o kernel PySpark. Para obter as instruções, confira [Criar um bloco de notas](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importe os tipos obrigatórios necessários para este aplicativo. Copie e cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** ou execute a célula usando o ícone de reprodução azul à esquerda do código.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. O contexto do Spark é criado automaticamente para você ao executar a primeira célula do código.

## <a name="construct-the-input-dataframe"></a>Construir o dataframe de entrada

Como os dados brutos estão no formato Parquet, você pode usar o contexto do Spark para extrair o arquivo diretamente na memória como um dataframe. Embora o código a seguir use as opções padrão, é possível forçar o mapeamento dos tipos de dados e outros atributos de esquema, se necessário.

1. Execute as linhas a seguir para criar um dataframe do Spark, colando o código em uma nova célula. Isso recupera os dados por meio da API de conjuntos de dados abertos. A extração de todos esses dados gera cerca de 1,5 bilhão de linhas. Dependendo do tamanho do pool do Spark (versão prévia), os dados brutos podem ser muito grandes ou levar muito tempo para a operação. Você pode filtrar esses dados para um volume menor. O uso de start_date e end_date aplica um filtro que retorna um mês de dados.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. A desvantagem da filtragem simples é que, de uma perspectiva estatística, ela pode introduzir desvio nos dados. Outra abordagem é usar a amostragem incorporada ao Spark. O código a seguir reduz o conjunto de dados para cerca de 2.000 linhas, se aplicado após o código acima. Essa etapa de amostragem pode ser usada, em vez do filtro simples, ou em conjunto com o filtro simples.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Agora é possível examinar os dados para ver o que foi lido. Normalmente, é melhor examinar os dados com um subconjunto, em vez do conjunto completo, dependendo do tamanho do conjunto de dados. O código a seguir oferece duas maneiras de exibir os dados: a primeira é básica e a segunda proporciona uma experiência de grade muito mais enriquecedora, bem como a capacidade de visualizar os dados graficamente.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Dependendo do tamanho do conjunto de dados gerado e da necessidade de experimentar ou executar o bloco de notas muitas vezes, pode ser aconselhável armazenar em cache o conjunto de dados localmente no espaço de trabalho. Há três maneiras de realizar o armazenamento em cache explícito:

   - Salvar o dataframe localmente como arquivo
   - Salvar o dataframe como tabela ou exibição temporária
   - Salvar o dataframe como tabela permanente

As duas primeiras abordagens estão inclusas nos exemplos de código a seguir.

A criação de uma tabela ou exibição temporária fornece caminhos de acesso diferentes para os dados, mas só funciona durante a sessão de instância do Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Entender os dados

Normalmente, você passaria por uma fase de *EDA (análise exploratória de dados)* neste ponto, para desenvolver uma compreensão dos dados. O código a seguir mostra três visualizações diferentes dos dados relacionados às gorjetas que levam a conclusões sobre o estado e a qualidade dos dados.

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

![Histograma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Diagrama de caixa](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Gráfico de dispersão](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparando os dados

Os dados em sua forma bruta geralmente não são adequados para passar diretamente para um modelo. Uma série de ações deve ser realizada nos dados para que cheguem a um estado onde possam ser utilizados no modelo.

No código abaixo, quatro classes de operações são realizadas:

- A remoção de exceções/valores incorretos por meio da filtragem.
- A remoção de colunas que não são necessárias.
- A criação de novas colunas derivadas dos dados brutos para fazer com que o modelo funcione com mais eficiência, às vezes chamada de caracterização.
- Rotulagem, como você está realizando a classificação binária (haverá gorjeta ou não em determinada corrida), é necessário converter o valor das gorjetas em um valor 0 ou 1.

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

A tarefa final é converter os dados rotulados para um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares de vetor de recurso de rótulo*, em que o *vetor de recurso* é um vetor de números que representa o ponto de entrada. Portanto, é necessário converter as colunas categóricas em números. As colunas `trafficTimeBins` e `weekdayString` precisam ser convertidas em representações de inteiros. Há várias abordagens para realizar a conversão, no entanto, a abordagem adotada neste exemplo é *OneHotEncoding*, uma abordagem comum.

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

A primeira tarefa é dividir o conjunto de dados em um conjunto de treinamento e um conjunto de teste ou validação. A divisão aqui é arbitrária e você deve brincar com diferentes configurações de divisão para ver se elas afetam o modelo.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Agora que há dois DataFrames, a próxima tarefa é criar a fórmula do modelo e executá-la no DataFrames de treinamento e depois validar em relação ao DataFrames de teste. Você deve fazer experiências com versões diferentes da fórmula do modelo para ver o impacto de diferentes combinações.

> [!Note]
> Para salvar o modelo, você precisará da função RBAC do Colaborador de Dados do Azure Storage Blob. Na conta de armazenamento, navegue até o Controle de Acesso (IAM) e selecione Adicionar atribuição de função. Atribua a função RBAC do Colaborador de Dados do Azure Storage Blob ao servidor do Banco de Dados SQL. Somente membros com o privilégio Proprietário podem executar essa etapa. Para várias funções internas de recursos do Azure, confira este [guia](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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

Agora você pode construir uma visualização final para ajudar a justificar os resultados deste teste. Uma [Curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) é uma maneira de examinar o resultado.

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

![Curva ROC para modelo de gorjetas de regressão logística](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC para modelo de gorjetas de regressão logística")

## <a name="shut-down-the-spark-instance"></a>Desligar a instância do Spark

Depois de concluir a execução do aplicativo, desligue o bloco de notas para liberar os recursos fechando a guia ou selecione **Encerrar sessão** no painel de status na parte inferior do bloco de notas.

## <a name="see-also"></a>Confira também

- [Visão geral: Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Próximas etapas

- [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial do Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Algumas das documentações oficiais do Apache Spark dependem do uso do console do Spark, que não está disponível no Azure Synapse Spark. Use as experiências de [notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou do [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
