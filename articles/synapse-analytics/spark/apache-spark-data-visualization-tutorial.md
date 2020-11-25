---
title: Visualizar dados com Apache Spark
description: Crie visualizações de dados ricas usando o Apache Spark e os blocos de anotações do Azure Synapse Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919374"
---
# <a name="analyze-data-with-apache-spark"></a>Analisar dados com Apache Spark

Neste tutorial, você aprenderá a executar a análise de dados exploratórios usando os Azure Open DataSets e Apache Spark e, em seguida, visualizando os resultados em um bloco de anotações do Azure Synapse Studio.

Em particular, analisaremos o conjunto de dado de [táxi da cidade de Nova York (NYC)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Os dados estão disponíveis por meio dos Conjuntos de Dados em Aberto no Azure. Esse subconjunto do conjunto de dados contém informações sobre viagens de táxis amarelos, incluindo informações sobre cada viagem, a hora de início e de término e os locais, o custo e outros atributos interessantes.
  
## <a name="before-you-begin"></a>Antes de começar
- Criar um pool de Apache Spark seguindo o [tutorial criar um pool de Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md) 

## <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados
1. Crie um bloco de notas usando o kernel PySpark. Para obter instruções, consulte [criar um bloco de anotações](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. O contexto do Spark é criado automaticamente para você ao executar a primeira célula do código.
>

2. Neste tutorial, usaremos várias bibliotecas diferentes para nos ajudar a visualizar o conjunto de os. Para fazer essa análise, será necessário importar as seguintes bibliotecas: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Como os dados brutos estão no formato Parquet, você pode usar o contexto do Spark para extrair o arquivo diretamente na memória como um dataframe. Crie um dataframe do Spark recuperando os dados por meio da API do Conjunto de Dados em Aberto no Azure. Aqui, usaremos o esquema do dataframe do Spark nas propriedades de *leitura* para inferir os tipos de texto e o esquema.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Depois que os dados são lidos, queremos fazer algumas filtragens iniciais para limpar o conjunto. Podemos remover colunas desnecessárias e adicionar colunas adicionais que extraem informações importantes. Além disso, filtraremos as anomalias dentro do conjunto de um.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Analisar dados
 Como analista de dados, você tem uma ampla variedade de ferramentas disponíveis para ajudá-lo a extrair informações dos dados. Nesta parte do tutorial, veremos algumas ferramentas úteis disponíveis nos notebooks do Azure Synapse Analytics. Nesta análise, queremos entender os fatores que geram mais dicas de táxi para o período selecionado.

###  <a name="apache-spark-sql-magic"></a>Apache Spark mágica do SQL 
Primeiro, executaremos a análise de dados exploratórios Apache Spark comandos SQL e Magic com o notebook Synapse. Assim que tivermos nossa consulta, veremos os resultados usando o ```chart options``` recurso interno. 

1. No bloco de anotações, crie uma nova célula e copie o código abaixo. Usando essa consulta, queremos entender como os valores de gorjeta médios foram alterados no período selecionado. Essa consulta também nos ajudará a identificar outras informações úteis, incluindo o valor mínimo/máximo da gorjeta por dia e o valor médio da tarifa.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. Depois que nossa consulta terminar a execução, podemos Visualizar os resultados alternando para a **exibição de gráfico**. Neste exemplo, criaremos um gráfico de **linhas** especificando o ```day_of_month``` campo como a **chave** e ```avgTipAmount``` como o **valor**. Depois de fazer as seleções, clique em **aplicar** para atualizar seu gráfico. 
   
## <a name="visualize-data"></a>Visualizar dados
Além das opções internas de gráficos do bloco de anotações, você também pode usar bibliotecas populares de software livre para criar suas próprias visualizações. Nos exemplos a seguir, usaremos o manasceu e o matplotlib, que são bibliotecas Python comumente usadas para visualização de dados. 

> [!Note]
> 
> Por padrão, cada pool de Apache Spark do Azure Synapse Analytics contém um conjunto de bibliotecas padrão usadas com frequência. Você pode exibir a lista completa de bibliotecas no [tempo de execução do Synapse do Azure](../spark/apache-spark-version-support.md). online. Além disso, para disponibilizar códigos de terceiros ou criados localmente para seus aplicativos, você pode [instalar uma biblioteca](../spark/apache-spark-azure-portal-add-libraries.md) em um dos pools do Spark (versão prévia).
>

1. Para tornar o desenvolvimento mais fácil e menos dispendioso, vamos reduzir o exemplo do conjunto de os. Usaremos o recurso interno de amostragem de Apache Spark. Além disso, o manascido e o matplotlib exigem uma matriz de dataframe do pandas ou numpy. Para obter um dataframe do pandas, usaremos o ```toPandas()``` comando para converter nosso dataframe.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Primeiro, queremos entender a distribuição de dicas em nosso conjunto de mesmos. Usaremos matplotlib para criar um histograma que mostra a distribuição do valor Tip e a contagem. Com base na distribuição, podemos ver que as dicas são distorcidas em relação a valores menores ou iguais a $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histograma de dicas](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Em seguida, queremos entender a relação entre as dicas de uma determinada viagem e o dia da semana. Usaremos o manascido para criar um gráfico de caixa Resumindo as tendências de cada dia da semana. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Distribuição de dicas por dia](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Agora, outra hipótese de que temos pode ser que haja uma relação positiva entre o número de passageiros e o valor total da gorjeta de táxi. Para verificar essa relação, executaremos o código a seguir para gerar um gráfico de caixa que ilustra a distribuição de dicas para cada contagem de passageiros. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Gráfico de caixa estreita](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Por fim, queremos entender a relação entre o valor da gorjeta do valor da tarifa. Com base nos resultados, podemos ver que há várias observações em que as pessoas não gorjetam. No entanto, também vemos uma relação positiva entre os valores gerais de Tarifa e gorjeta.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Dispersão](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Desligar a instância do Spark

Depois de concluir a execução do aplicativo, desligue o bloco de notas para liberar os recursos fechando a guia ou selecione **Encerrar sessão** no painel de status na parte inferior do bloco de notas.

## <a name="see-also"></a>Confira também

- [Visão geral: Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)
- [Criar um modelo de aprendizado de máquina com o Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial do Apache Spark](https://spark.apache.org/docs/latest/)