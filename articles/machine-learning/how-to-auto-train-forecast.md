---
title: Treinar automaticamente um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para treinar um modelo de regressão de previsão de série temporal usando o aprendizado de máquina automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 05d658c052c5bc12f49d957bb29ad085c269c57b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137341"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treinar automaticamente um modelo de previsão de série temporal
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a configurar e treinar um modelo de regressão de previsão de série temporal usando o aprendizado de máquina automatizado no Azure Machine Learning. 

Configurar um modelo de previsão é semelhante a configurar um modelo de regressão padrão usando o Machine Learning automatizado, mas algumas opções de configuração e etapas de pré-processamento existem para trabalhar com dados de série temporal. 

Por exemplo, você pode [Configurar](#config) o quanto no futuro a previsão deve estender (o horizonte de previsão), bem como um retardo e muito mais. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível.

Os exemplos a seguir mostram como:

* Preparar dados para a modelagem de série temporal
* Configurar parâmetros de série temporal específicos em um [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objeto
* Executar previsões com dados de série temporal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Ao contrário dos métodos de série temporal clássicas, em valores de série temporal posteriores de ML, são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros pregnósticos. Essa abordagem incorpora várias variáveis contextuais e sua relação entre si durante o treinamento. Como vários fatores podem influenciar uma previsão, esse método se alinha bem com os cenários de previsão do mundo real. Por exemplo, ao prever as vendas, as interações de tendências históricas, a taxa de câmbio e o preço, todos orientam o resultado das vendas. 

Os recursos extraídos dos dados de treinamento desempenham uma função crítica. E o ML automatizado executa etapas de pré-processamento padrão e gera recursos adicionais de série temporal para capturar efeitos sazonais e maximizar a precisão preditiva

## <a name="time-series-and-deep-learning-models"></a>Modelos de série temporal e aprendizado profundo


O profundo aprendizado automatizado do ML permite prever dados de série temporal monovariável e MultiVariable.

Os modelos de aprendizado profundo têm três recursos intrínsecos:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Eles dão suporte a várias entradas e saídas
1. Eles podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências

Considerando dados maiores, modelos de aprendizado profundo, como o ForecastTCN da Microsoft, podem melhorar as pontuações do modelo resultante. Saiba como [configurar seu experimento para aprendizado profundo](#configure-a-dnn-enable-forecasting-experiment).

O ML automatizado fornece aos usuários os modelos de série temporal nativa e de aprendizado profundo como parte do sistema de recomendação. 


Modelos| Descrição | Vantagens
----|----|---
Prophet (visualização)|O Prophet funciona melhor com a série temporal que tem efeitos sazonais fortes e várias estações de dados históricos. | Preciso & rápido, robusto a exceções, dados ausentes e alterações consideráveis na sua série temporal.
ARIMA automático (visualização)|A média de movimentação integrada de regressão automática (ARIMA) executa melhor, quando os dados são estáticos. Isso significa que suas propriedades estatísticas como a média e a variância são constantes em todo o conjunto. Por exemplo, se você virar uma moeda, a probabilidade de você obter o cabeçote será de 50%, independentemente se você virar hoje, amanhã ou ano seguinte.| Ótimo para a série monovariável, já que os valores anteriores são usados para prever os valores futuros.
ForecastTCN (visualização)| O ForecastTCN é um modelo de rede neural projetado para lidar com as tarefas de previsão mais exigentes, capturando tendências locais e globais não lineares em seus dados, bem como relações entre a série temporal.|Capaz de aproveitar tendências complexas em seus dados e pode ser rapidamente dimensionada para o maior de conjuntos.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para criar o espaço de trabalho, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
* Este artigo pressupõe familiaridade básica com a configuração de um experimento de aprendizado de máquina automatizado. Siga o [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para ver os padrões básicos de design de experimento de aprendizado automático de máquina.

## <a name="preparing-data"></a>Preparando dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão no Machine Learning automatizado é incluir um recurso em seus dados que representa uma série temporal válida. Uma série temporal regular tem uma frequência bem definida e consistente e tem um valor em cada ponto de exemplo em um período de tempo contínuo. Considere o seguinte instantâneo de um arquivo `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Esse conjunto de dados é um exemplo simples de dados de vendas diários para uma empresa que tem duas lojas diferentes, A e B. Além disso, há um `week_of_year` recurso para isso que permitirá que o modelo detecte sazonalidade semanalmente. O campo `day_datetime` representa uma série temporal limpa com frequência diária e o campo `sales_quantity` é a coluna de destino para executar previsões. Leia os dados em um data frame do pandas e use a `to_datetime` função para garantir que a série temporal seja `datetime` um tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Nesse caso, os dados já estão classificados em ordem crescente pelo campo `day_datetime`hora. No entanto, ao configurar um experimento, verifique se a coluna de tempo desejada está classificada em ordem crescente para criar uma série temporal válida. Suponha que os dados contenham 1.000 registros e criem uma divisão determinística nos dados para criar conjuntos de dados de treinamento e teste. Identifique o nome da coluna do rótulo e defina-o como rótulo. Neste exemplo, o rótulo será `sales_quantity`. Em seguida, separe o campo `test_data` de rótulo de `test_target` para formar o conjunto.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para prever valores futuros, verifique se todos os recursos usados no treinamento podem ser usados ao executar previsões para o horizonte pretendido. Por exemplo, ao criar uma previsão de demanda, incluir um recurso para o preço de estoque atual poderia aumentar imensamente a precisão de treinamento. No entanto, se você pretende prever com um horizonte longo, talvez não seja possível prever com precisão valores de ações futuros correspondentes aos pontos de série temporal futuros, e a precisão do modelo pode ser afetada.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Dados de treinamento e validação
Você pode especificar conjuntos de treinamento e de validação separados diretamente `AutoMLConfig` no construtor.

### <a name="rolling-origin-cross-validation"></a>Validação cruzada de origem sem interrupção
Para a previsão de série temporal, a ROCV (validação cruzada de origem revertida) é usada para dividir a série temporal de modo oportuno consistente. O ROCV divide a série em dados de treinamento e validação usando um ponto de tempo de origem. Deslizar a origem no tempo gera as dobras de validação cruzada.  

![texto alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Essa estratégia preservará a integridade dos dados da série temporal e eliminará o risco de vazamento de dados. O ROCV é usado automaticamente para prever tarefas passando os dados de treinamento e validação juntos e definindo o número de dobras de validação cruzada usando `n_cross_validations`. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Saiba mais sobre o [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurar e executar experimento

Para tarefas de previsão, o Machine Learning automatizado usa etapas de pré-processamento e estimativa específicas para dados de série temporal. As seguintes etapas de pré-processamento serão executadas:

* Detecte a frequência de exemplo de série temporal (por exemplo, por hora, diariamente, semanalmente) e crie novos registros para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores ausentes no destino (via avanço) e colunas de recurso (usando valores de coluna mediana)
* Criar recursos baseados em granulares para habilitar efeitos fixos em diferentes séries
* Crie recursos baseados em tempo para auxiliar no aprendizado de padrões sazonais
* Codificar variáveis categóricas em quantidades numéricas

O [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objeto define as configurações e os dados necessários para uma tarefa de aprendizado de máquina automatizada. Semelhante a um problema de regressão, você define parâmetros de treinamento padrão, como tipo de tarefa, número de iterações, dados de treinamento e número de validações cruzadas. Para tarefas de previsão, há parâmetros adicionais que devem ser definidos para afetar o experimento. A tabela a seguir explica cada parâmetro e seu uso.

| Nome&nbsp;do parâmetro | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna datetime nos dados de entrada usados para criar a série temporal e inferir sua frequência.|✓|
|`grain_column_names`|Nome (s) definindo grupos de séries individuais nos dados de entrada. Se a granulação não for definida, o conjunto de dados será considerado uma série temporal.||
|`max_horizon`|Define o horizonte de previsão máximo desejado em unidades de frequência de série temporal. As unidades são baseadas no intervalo de tempo de seus dados de treinamento, por exemplo, mensalmente, semanalmente que o previsão deve prever.|✓|
|`target_lags`|Número de linhas para atrasar os valores de destino com base na frequência dos dados. O retardo é representado como uma lista ou um único inteiro. O retardo deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponder ou correlacionar por padrão. Por exemplo, ao tentar prever a demanda de um produto, a demanda em qualquer mês pode depender do preço de mercadorias específicas de 3 meses antes. Neste exemplo, você pode querer atrasar o destino (demanda) negativamente por 3 meses para que o modelo seja treinamento na relação correta.||
|`target_rolling_window_size`|*n* períodos históricos a serem usados para gerar valores previstos, <= tamanho do conjunto de treinamento. Se omitido, *n* será o tamanho completo do conjunto de treinamento. Especifique esse parâmetro quando desejar apenas considerar uma determinada quantidade de histórico ao treinar o modelo.||
|`enable_dnn`|Habilitar previsão de DNNs.||

Consulte a [documentação de referência](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter mais informações.

Crie as configurações de série temporal como um objeto Dictionary. Defina o `time_column_name` para o `day_datetime` campo no conjunto de dados. Defina o `grain_column_names` parâmetro para garantir que **dois grupos de série temporal separados** sejam criados para os dados; uma para armazenar a e B. por fim, defina o `max_horizon` como 50 para prever o conjunto de teste inteiro. Defina uma janela de previsão como 10 períodos `target_rolling_window_size`com e especifique um único retardo nos valores de destino para dois períodos à frente com `target_lags` o parâmetro. É recomendável definir `max_horizon`e `target_rolling_window_size` `target_lags` como "auto", que detectará automaticamente esses valores para você. No exemplo a seguir, as configurações "auto" foram usadas para esses parâmetros. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

Ao definir o `grain_column_names` no trecho de código acima, o AutoML criará dois grupos de série temporal separados, também conhecidos como várias séries temporais. Se nenhum detalhamento for definido, AutoML assumirá que o conjunto de data é uma única série temporal. Para saber mais sobre a série temporal única, consulte a [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Agora, crie um `AutoMLConfig` objeto padrão, especificando `forecasting` o tipo de tarefa e envie o experimento. Após a conclusão do modelo, recupere a melhor iteração de execução.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Consulte os [notebooks de exemplo de previsão](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de código detalhados de configuração de previsão avançada, incluindo:

* [detecção de feriados e personalização](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [retardo configurável](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [recursos agregados de janela sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurar um teste de previsão de DNN habilitar

> [!NOTE]
> O suporte do DNN para previsão no Machine Learning automatizado está em versão prévia e não tem suporte para execuções locais.

Para aproveitar o DNNs para previsão, você precisará definir o `enable_dnn` parâmetro em AutoMLConfig como true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Saiba mais sobre [o AutoMLConfig](#configure-and-run-experiment).

Como alternativa, você pode selecionar a `Enable deep learning` opção no estúdio.
![alt text](./media/how-to-auto-train-forecast/enable_dnn.png)

É recomendável usar um cluster de computação AML com SKUs de GPU e pelo menos dois nós como o destino de computação. Para permitir tempo suficiente para que o treinamento do DNN seja concluído, é recomendável definir o tempo limite do experimento como um mínimo de algumas horas.
Para obter mais informações sobre a computação AML e os tamanhos de VM que incluem a GPU, consulte a documentação da [documentação de computação AML](how-to-set-up-training-targets.md#amlcompute) e [tamanhos de máquina virtual otimizados para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Exiba o [bloco](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) de notas de previsão de produção de bebidas para obter um exemplo de código Detalhado, aproveitando o DNNs.

### <a name="target-rolling-window-aggregation"></a>Agregação de janela de destino
Geralmente, as melhores informações que um previsto pode ter é o valor recente do destino. A criação de estatísticas cumulativas do destino pode aumentar a precisão de suas previsões. As agregações de janela de destino permitem que você adicione uma agregação sem interrupção de valores de dados como recursos. Para habilitar o Windows sem interrupção de `target_rolling_window_size` destino, defina o para o tamanho da janela inteira desejada. 

Um exemplo disso pode ser visto ao prever a demanda de energia. Você pode adicionar um recurso de janela sem interrupção de três dias para considerar alterações térmicas de espaços aquecidos. No exemplo a seguir, criamos essa janela de tamanho três definindo `target_rolling_window_size=3` no `AutoMLConfig` Construtor. A tabela mostra a engenharia de recursos que ocorre quando a agregação de janela é aplicada. Colunas para mínimo, máximo e soma são geradas em uma janela deslizante de três com base nas configurações definidas. Cada linha tem um novo recurso calculado, no caso do carimbo de data/hora de 8 de setembro de 2017 4:10:00 os valores máximo, mínimo e soma são calculados usando os valores de demanda para 8 de setembro de 2017 1:10:00-3:10:00. Esta janela de três turnos para popular os dados das linhas restantes.

![texto alternativo](./media/how-to-auto-train-forecast/target-roll.svg)

Gerar e usar esses recursos adicionais como dados contextuais extras ajuda com a precisão do modelo de treinamento.

Exiba um exemplo de código Python aproveitando o [recurso agregado de janela de sobreversão de destino](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Exibir Resumo de engenharia de recursos

Para os tipos de tarefa de série temporal no Machine Learning automatizado, você pode exibir detalhes do processo de engenharia de recursos. O código a seguir mostra cada recurso bruto junto com os seguintes atributos:

* Nome do recurso bruto
* Número de recursos de engenharia formados fora deste recurso bruto
* Tipo detectado
* Se o recurso foi descartado
* Lista de transformações de recursos para o recurso bruto

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prevendo com o melhor modelo

Use a melhor iteração de modelo para prever valores para o conjunto de dados de teste.

A `forecast()` função deve ser usada em vez `predict()`de, isso permitirá especificações de quando as previsões devem ser iniciadas. No exemplo a seguir, primeiro você substituirá todos os `y_pred` valores `NaN`em por. A origem da previsão estará no final dos dados de treinamento nesse caso, como normalmente seria ao usar `predict()`. No entanto, se você substituiu apenas a `y_pred` segunda `NaN`metade de por, a função deixaria os valores numéricos na primeira metade sem modificações, mas `NaN` prevendo os valores na segunda metade. A função retorna os valores previstos e os recursos alinhados.

Você também pode usar o `forecast_destination` parâmetro na `forecast()` função para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcule RMSE (erro ao quadrado da média raiz) `actual_labels` entre os valores reais e os valores previstos em `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão geral do modelo foi determinada, a próxima etapa realista é usar o modelo para prever valores futuros desconhecidos. Forneça um conjunto de dados no mesmo formato que o conjunto `test_data` de teste, mas com DateTimes futuros, e o conjunto de previsão resultante é os valores previstos para cada etapa da série temporal. Suponha que os últimos registros de série temporal no conjunto de dados eram de 12/31/2018. Para prever a demanda do dia seguinte (ou quantos períodos forem necessários para prever, <= `max_horizon`), crie um único registro de série temporal para cada loja para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita as etapas necessárias para carregar esses dados futuros em um dataframe e, em `best_run.predict(test_data)` seguida, execute para prever valores futuros.

> [!NOTE]
> Os valores não podem ser previstos para o número `max_horizon`de períodos maiores que o. O modelo deve ser treinado novamente com um horizonte maior para prever valores futuros além do horizonte atual.

## <a name="next-steps"></a>Próximas etapas

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experimentos com o Machine Learning automatizado.
* Exiba a documentação de referência do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
