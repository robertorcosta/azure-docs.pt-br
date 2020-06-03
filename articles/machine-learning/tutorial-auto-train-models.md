---
title: 'Tutorial de regressão: ML automatizado'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você aprenderá a gerar um modelo de machine learning usando machine learning automatizado. O Azure Machine Learning pode executar o pré-processamento de dados, a seleção de algoritmo e a seleção de hiperparâmetro de maneira automatizada para você.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 97b129bfaa1a8612040e59c6378aa1965c5c49cd
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118885"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: Usar aprendizado de máquina automatizado para prever tarifas de táxi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usará o machine learning automatizado no Azure Machine Learning para criar um modelo de regressão a fim de prever preços de tarifa de táxi na cidade de Nova York. Esse processo aceita dados de treinamento e configurações e itera automaticamente por meio de combinações de configurações de hiperparâmetro, modelos e métodos de normalização/padronização de diferentes recursos para chegar ao melhor modelo.

![Fluxograma](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Baixar, transformar e limpar dados usando Azure Open Datasets
> * Treinar um modelo de regressão com aprendizado de máquina automatizado
> * Calcular a precisão do modelo

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga](https://aka.ms/AMLFree) do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o [tutorial de instalação](tutorial-1st-experiment-sdk-setup.md) caso ainda não tenha uma máquina virtual de notebook ou um Workspace do Azure Machine Learning.
* Depois de concluir o tutorial de instalação, abra o notebook *tutorials/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb* usando o mesmo servidor de notebook.

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) caso você deseje executá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local). Execute `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` para obter os pacotes necessários.

## <a name="download-and-prepare-data"></a>Baixar e preparar dados

Importe os pacotes necessários. O pacote de conjuntos de dados abertos contém uma classe que representa cada fonte de dados (`NycTlcGreen`, por exemplo) para filtrar facilmente os parâmetros de data antes de baixar.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece criando um dataframe para manter os dados de táxis. Ao trabalhar em um ambiente que não seja Spark, os conjuntos de dados abertos só permitem baixar um mês de dados de cada vez com determinadas classes para evitar `MemoryError` com grandes conjuntos de dados.

Para baixar dados de táxis, busque iterativamente um mês por vez e, antes de adicioná-los ao `green_taxi_df`, faça uma amostragem aleatória de dois mil registros de cada mês para evitar a sobrecarga do dataframe. Em seguida, visualize os dados.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>15,00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>16,30</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>4,80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0.50</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>5.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>7,80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0,00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>1</td>
      <td>7,00</td>
      <td>0,00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nan</td>
      <td>9.55</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1.00</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 23 colunas</p>
</div>


Agora que os dados iniciais foram carregados, defina uma função para criar vários recursos baseados em tempo usando o campo datetime de coleta. Isso criará novos campos para o número do mês, dia do mês, dia da semana e hora do dia e permitirá que o modelo considere a sazonalidade com base no tempo. Use a função `apply()` no dataframe para aplicar iterativamente a função `build_time_features()` a cada linha nos dados de táxis.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>16,30</td>
      <td>1.00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1.00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>4,80</td>
      <td>1.00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1.00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0.50</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>5.00</td>
      <td>1.00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>7,80</td>
      <td>1.00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,80</td>
      <td>1.00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1.00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nan</td>
      <td>9.55</td>
      <td>1.00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1.00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 27 colunas</p>
</div>

Remova algumas das colunas de que você não precisará para treinamento ou criação de recursos adicionais.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Limpar dados

Execute a função `describe()` no novo dataframe para ver estatísticas resumidas de cada campo.

```python
green_taxi_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
      <td>48.000,00</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1,78</td>
      <td>1,37</td>
      <td>2.87</td>
      <td>-73,83</td>
      <td>40,69</td>
      <td>-73,84</td>
      <td>40,70</td>
      <td>14,75</td>
      <td>6,50</td>
      <td>15,13</td>
      <td>3,27</td>
      <td>13,52</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0,41</td>
      <td>1.04</td>
      <td>2,93</td>
      <td>2.76</td>
      <td>1,52</td>
      <td>2.61</td>
      <td>1,44</td>
      <td>12,08</td>
      <td>3.45</td>
      <td>8,45</td>
      <td>1,95</td>
      <td>6,83</td>
    </tr>
    <tr>
      <th>Min</th>
      <td>1.00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-300,00</td>
      <td>1.00</td>
      <td>1.00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,00</td>
      <td>1.00</td>
      <td>1,06</td>
      <td>-73,96</td>
      <td>40,70</td>
      <td>-73,97</td>
      <td>40,70</td>
      <td>7,80</td>
      <td>3,75</td>
      <td>8,00</td>
      <td>2,00</td>
      <td>9,00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,00</td>
      <td>1.00</td>
      <td>1,90</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>11,30</td>
      <td>6,50</td>
      <td>15,00</td>
      <td>3.00</td>
      <td>15,00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,00</td>
      <td>1.00</td>
      <td>3.60</td>
      <td>-73,92</td>
      <td>40,80</td>
      <td>-73,91</td>
      <td>40,79</td>
      <td>17,80</td>
      <td>9,25</td>
      <td>22,00</td>
      <td>5.00</td>
      <td>19,00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,00</td>
      <td>9,00</td>
      <td>97,57</td>
      <td>0,00</td>
      <td>41,93</td>
      <td>0,00</td>
      <td>41,94</td>
      <td>450,00</td>
      <td>12,00</td>
      <td>30,00</td>
      <td>6,00</td>
      <td>23,00</td>
    </tr>
  </tbody>
</table>
</div>


A partir das estatísticas de resumo, você pode observar que há vários campos com valores discrepantes ou valores que reduzem a precisão do modelo. Primeiro, filtre os campos lat/long para que estejam dentro dos limites da área Manhattan. Isso filtrará viagens de táxi mais longas ou viagens que são exceções no que diz respeito à sua relação com outros recursos.

Além disso, filtre o campo `tripDistance` para que tenha mais que zero, mas menos que 31 milhas (a distância Haversine entre os dois pares lat/long). Isso elimina viagens mais longas que são exceção e que têm custo divergente.

Por fim, o campo `totalAmount` tem valores negativos para as tarifas de táxi, o que não faz sentido no contexto de nosso modelo, e o campo `passengerCount` tem dados inválidos com os valores mínimos iguais a zero.

Filtre essas anomalias usando funções de consulta e remova as últimas colunas desnecessárias para o treinamento.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Chame `describe()` novamente nos dados para garantir que a limpeza funcionou conforme o esperado. Agora você tem um conjunto preparado e limpo de dados de táxis, feriados e clima para usar no treinamento de modelo de machine learning.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Configurar o workspace

Crie um objeto de workspace a partir do workspace existente. Um [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita suas informações de recursos e assinatura do Azure. Ele também cria um recurso de nuvem para monitorar e acompanhar a execução do seu modelo. `Workspace.from_config()` lê o arquivo **config. json** e carrega os detalhes de autenticação em um objeto chamado `ws`. `ws` é usado em todo o restante do código neste tutorial.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Divida os dados em conjuntos de treinamento e teste

Divida os dados em conjuntos de treinamento e teste usando a função `train_test_split` na biblioteca `scikit-learn`. Essa função separa os dados no conjunto de dados X (**recursos**) para treinamento do modelo e no conjunto de dados Y (**valores a serem previstos**) para teste.

O parâmetro `test_size` determina a porcentagem de dados a ser alocada para teste. O parâmetro `random_state` define uma semente para o gerador aleatório, de modo que suas divisões de teste de treinamento sejam determinísticas.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

A finalidade dessa etapa é ter pontos de dados para testar o modelo acabado que não foram usados para treinar o modelo a fim de medir a verdadeira precisão.

Em outras palavras, um modelo bem treinado deve ser capaz de fazer previsões com precisão por meio de dados que ainda não viu. Agora, você tem dados preparados para treinar automaticamente um modelo de machine learning.

## <a name="automatically-train-a-model"></a>Treinar um modelo automaticamente

Para treinar o modelo automaticamente, execute as seguintes etapas:
1. Defina configurações para a execução do experimento. Anexe seus dados de treinamento à configuração e modifique as configurações que controlam o processo de treinamento.
1. Envie o experimento para ajuste do modelo. Depois de enviar o experimento, o processo itera diferentes algoritmos de aprendizado de máquina e configurações de hiperparâmetro, aderindo às restrições definidas. Ele escolhe o modelo mais adequado otimizando uma métrica de precisão.

### <a name="define-training-settings"></a>Definir configurações de treinamento

Defina as configurações de modelos e os parâmetros do experimento para o treinamento. Exiba a lista completa das [configurações](how-to-configure-auto-train.md). O envio do experimento com essas configurações padrão demorará aproximadamente de 5 a 20 minutos; se quiser um tempo de execução menor, reduza o parâmetro `experiment_timeout_minutes`.

|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|2|Limite de tempo em minutos para cada iteração. Reduza esse valor para diminuir o runtime total.|
|**experiment_timeout_minutes**|20|Quantidade máxima de tempo em minutos que todas as iterações combinadas podem levar antes que o experimento seja encerrado.|
|**enable_early_stopping**|True|Sinalizador para permitir o encerramento antecipado se a pontuação não estiver melhorando em curto prazo.|
|**primary_metric**| spearman_correlation | Métrica que você deseja otimizar. O modelo mais adequado será escolhido com base nessa métrica.|
|**featurization**| auto | Com o uso de **True**, o experimento pode pré-processar os dados de entrada (lidar com os dados ignorados, converter texto em numérico etc.)|
|**verbosity**| logging.INFO | Controla o nível de registro em log.|
|**n_cross_validations**|5|Número de divisões de validação cruzada para executar quando os dados de validação não são especificados.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Use as configurações de treinamento definidas como um parâmetro `**kwargs` para um objeto `AutoMLConfig`. Além disso, especifique os dados de treinamento e o tipo de modelo, que é `regression`, neste caso.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="train-the-automatic-regression-model"></a>Treinar o modelo de regressão automática

Criar um objeto de experimento em seu workspace. Um experimento atua como um contêiner para suas execuções individuais. Transmita o objeto `automl_config` definido para o experimento e defina a saída como `True` para exibir o andamento durante a execução.

Após começar o experimento, a saída mostrada é atualizada em tempo real enquanto o experimento é executado. Para cada iteração, você vê o tipo de modelo, a duração da execução e a precisão do treinamento. O campo `BEST` monitora a melhor pontuação de treinamento em execução com base no tipo de métrica.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Explorar os resultados

Explore os resultados do treinamento automático com um [widget do Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). O widget permite que você veja um grafo e uma tabela de todas as iterações de execução individuais, em conjunto com metadados e métricas de precisão de treinamento. Além disso, você pode filtrar em métricas de precisão diferentes da métrica primária com o seletor de lista suspensa.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Detalhes de execução do widget do Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Gráfico do widget do Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Recuperar o melhor modelo

Selecione o melhor modelo entre suas iterações. A função `get_output` retorna a melhor execução e o modelo ajustado para a última invocação de ajuste. Ao usar as sobrecargas em `get_output`, você pode recuperar o modelo de melhor execução e ajuste para qualquer métrica registrada em log ou iteração em particular.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testar a precisão do melhor modelo

Use o melhor modelo para executar previsões no conjunto de dados de teste e prever as tarifas de táxi. A função `predict` usa o melhor modelo e prevê os valores de Y, **custo da corrida**, do conjunto de dados `x_test`. Imprima os 10 primeiros valores de custo previstos de `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Calcule o `root mean squared error` dos resultados. Converta o dataframe `y_test` em uma lista para comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o erro médio ao quadrado entre elas. Usar a raiz quadrada do resultado gera um erro nas mesmas unidades que a variável y, **custo**. Indica aproximadamente o quão distantes as previsões das tarifas de táxi estão das tarifas reais.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Execute o código a seguir para calcular o MAPE (desvio percentual absoluto médio) usando os conjuntos de dados `y_actual` e `y_predict` completos. Essa métrica calcula uma diferença absoluta entre cada valor previsto e real e somas todas as diferenças. Em seguida, expressa a soma como uma porcentagem do total de valores reais.

```python
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

    Model MAPE:
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


Nas duas métricas de precisão da previsão, você vê que o modelo é razoavelmente bom em prever as tarifas de táxi por meio dos recursos do conjunto de dados, normalmente dentro de +- $ 4,00 e com erro de aproximadamente 15%.

O processo de desenvolvimento do modelo de machine learning tradicional é altamente intensivo de recursos e requer conhecimento de domínio significativo e investimento de tempo para executar e comparar os resultados de dezenas de modelos. A utilização do aprendizado de máquina automatizado é uma ótima maneira de testar rapidamente muitos modelos diferentes para seu cenário.

## <a name="clean-up-resources"></a>Limpar os recursos

Não conclua esta seção se você planeja executar outros tutoriais do Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Parar a instância de computação

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua-os para não gerar encargos.

1. No portal do Azure, selecione **Grupos de recursos** no canto esquerdo.
1. Selecione o grupo de recursos criado na lista.
1. Selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Excluir**.

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de aprendizado de máquina automatizado, você executou as seguintes tarefas:

> [!div class="checklist"]
> * Configurou um workspace e preparou os dados para um experimento.
> * Treinou usando um modelo de regressão automatizado localmente com parâmetros personalizados.
> * Explorou e examinou os resultados do treinamento.

[Implantar seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
