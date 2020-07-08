---
title: Treinar automaticamente um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning
description: Saiba como usar o Azure Machine Learning para treinar um modelo de regressão de previsão de série temporal usando o machine learning automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 72b0a3074bfdfb6b6038f6c63eb01a7b33d45ea6
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959119"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treinar automaticamente um modelo de previsão de série temporal
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a configurar e treinar um modelo de regressão de previsão de série temporal usando o machine learning automatizado no [SDK Python do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Para uma experiência com pouco código, veja o [Tutorial: Prever a demanda com o machine learning automatizado](tutorial-automated-ml-forecast.md) para conhecer um exemplo de previsão de série temporal usando o machine learning automatizado no [Azure Machine Learning Studio](https://ml.azure.com/).

Configurar um modelo de previsão se parece com configurar um modelo de regressão padrão usando o machine learning automatizado, mas há algumas opções de configuração e etapas de pré-processamento para trabalhar com dados de série temporal. 

Por exemplo, você pode [configurar](#config) o tempo até o qual a previsão se estende (o horizonte da previsão), além de retardos e muito mais. O machine learning automatizado aprende um modelo único, mas geralmente ramificado internamente, para todos os itens no conjunto de dados e horizontes de previsão. Assim, mais dados estão disponíveis para estimar os parâmetros de modelo e se torna possível fazer generalizações para séries não vistas.

Os exemplos a seguir mostram como:

* Preparar dados para o modelo de série temporal
* Configurar parâmetros de série temporal específicos em um objeto [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Executar previsões com dados de série temporal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Ao contrário dos métodos de série temporal clássicos, no machine learning automatizado, os valores de série temporal passada são “dinamizados” para se tornarem dimensões adicionais para o regressor junto com outros preditores. Essa abordagem incorpora várias variáveis contextuais e sua relação entre si durante o treinamento. Como vários fatores podem influenciar uma previsão, esse método se alinha bem com os cenários reais de previsão. Por exemplo, em uma previsão de vendas, as interações de tendências históricas, a taxa de câmbio e o preço orientam o resultado das vendas. 

Os recursos extraídos dos dados de treinamento desempenham uma função crítica. E o machine learning automatizado executa etapas de pré-processamento padrão e gera recursos adicionais de série temporal para capturar efeitos sazonais e maximizar a precisão preditiva

## <a name="time-series-and-deep-learning-models"></a>Modelos de série temporal e aprendizado profundo

O aprendizado profundo do machine learning automatizado possibilita a previsão de dados de série temporal monovariável e multivariada.

Os modelos de aprendizado profundo têm três recursos intrínsecos:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Eles tem suporte a várias entradas e saídas
1. Eles podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências

Considerando dados maiores, modelos de aprendizado profundo, como o ForecastTCN da Microsoft, podem melhorar as pontuações do modelo resultante. Saiba como [configurar seu experimento para aprendizado profundo](#configure-a-dnn-enable-forecasting-experiment).

O machine learning automatizado oferece modelos de série temporal nativa e de aprendizado profundo aos usuários, como parte do sistema de recomendação. 

Modelos| Descrição | Benefícios
----|----|---
Prophet (versão prévia)|O Prophet funciona melhor com séries temporais com efeitos sazonais fortes e várias estações de dados históricos. Para aproveitar esse modelo, instale-o localmente usando o `pip install fbprophet` . | Precisão e rapidez, robusto a exceções, dados ausentes e alterações significativas na sua série temporal.
Auto-ARIMA (versão prévia)|A média de movimentação integrada de regressão automática (ARIMA) funciona melhor quando os dados são estáticos. Isso significa que suas propriedades estatísticas, como média e variância, são constantes em todo o conjunto. Por exemplo, se você jogar uma moeda hoje, amanhã ou daqui a um ano, a probabilidade de obter cara será sempre de 50%.| Ótimo para série monovariável, já que os valores anteriores são usados para prever os valores futuros.
ForecastTCN (versão prévia)| O ForecastTCN é um modelo de rede neural projetado para lidar com as tarefas de previsão mais exigentes, capturando tendências locais e globais não lineares nos seus dados, além de relações entre as séries temporais.|Pode aproveitar tendências complexas nos seus dados e ser rapidamente dimensionado para os maiores de conjuntos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* Este artigo pressupõe um conhecimento básico da configuração de um experimento de machine learning automatizado. Siga o [tutorial](tutorial-auto-train-models.md) ou as [instruções](how-to-configure-auto-train.md) para ver os padrões básicos de design de experimentos de machine learning automatizado.

## <a name="preparing-data"></a>Preparando dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão no machine learning automatizado é a inclusão de um recurso nos seus dados que representa uma série temporal válida. Uma série temporal regular tem uma frequência bem definida e consistente e um valor em cada ponto de amostra em um período de tempo contínuo. Considere o seguinte instantâneo de um arquivo `sample.csv`.

```output
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
```

Esse conjunto de dados é um exemplo simples de dados de vendas diárias de uma empresa que tem duas lojas diferentes, A e B. Além disso, há um recurso para `week_of_year` que permitirá que o modelo detecte sazonalidade semanal. O campo `day_datetime` representa uma série temporal limpa com frequência diária e o campo `sales_quantity` é a coluna de destino para executar previsões. Leia os dados em um dataframe do Pandas e use a função `to_datetime` para garantir que a série temporal seja um tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Nesse caso, os dados já estão classificados em ordem crescente pelo campo de tempo `day_datetime`. No entanto, ao configurar um experimento, verifique se a coluna de tempo desejada está classificada em ordem crescente para criar uma série temporal válida. Suponha que os dados contenham 1.000 registros e faça uma divisão determinística nos dados para criar conjuntos de dados de treinamento e teste. Identifique o nome da coluna do rótulo e defina-o no rótulo. Neste exemplo, o rótulo será `sales_quantity`. Em seguida, separe o campo de rótulo de `test_data` para formar o conjunto `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para prever valores futuros, verifique se todos os recursos usados no treinamento podem ser usados ao executar previsões para o horizonte pretendido. Por exemplo, ao criar uma previsão de demanda, incluir um recurso para o preço de ações atual poderia aumentar imensamente a precisão do treinamento. No entanto, se você pretende fazer uma previsão com horizonte longo, talvez não seja possível prever com precisão valores de ações futuros correspondentes a pontos de série temporal futuros, e a precisão do modelo poderá ser prejudicada.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Dados de treinamento e validação
Você pode especificar conjuntos de validação e treino separado diretamente no construtor `AutoMLConfig`.

### <a name="rolling-origin-cross-validation"></a>Validação cruzada de origem sem interrupção
Para a previsão de série temporal, a validação cruzada de origem sem interrupção (ROCV) é usada para dividir a série temporal de modo temporariamente consistente. O ROCV divide a série em dados de treinamento e de validação usando um ponto de tempo de origem. Deslizar a origem no tempo gera as dobras de validação cruzada.  

![texto alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Essa estratégia preservará a integridade dos dados da série temporal e eliminará o risco de vazamento de dados. O ROCV é usado automaticamente para prever tarefas passando juntos os dados de treinamento e de validação e definindo o número de dobras de validação cruzada usando `n_cross_validations`. Saiba como o machine learning automatizado aplica a validação cruzada para [evitar modelos com sobreajuste](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Saiba mais sobre o [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurar e executar o experimento

Para tarefas de previsão, o machine learning automatizado usa etapas de pré-processamento e estimativa específicas a dados de série temporal. As seguintes etapas de pré-processamento serão executadas:

* Detectar a frequência de amostragem da série temporal (por exemplo, por hora, diária, semanal) e criar registros novos para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores ausentes no destino (via preenchimento à frente) e colunas de recurso (usando valor mediano de coluna)
* Criar recursos baseados em agregação para habilitar efeitos fixos em diferentes séries
* Criar recursos baseados em tempo para auxiliar no aprendizado de padrões sazonais
* Codificar variáveis categóricas em quantidades numéricas

O objeto [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) define as configurações e os dados necessários para uma tarefa de machine learning automatizado. Como em um problema de regressão, você define parâmetros de treinamento padrão, como tipo de tarefa, número de iterações, dados de treinamento e número de validações cruzadas. Para tarefas de previsão, há parâmetros adicionais que afetam o experimento e precisam ser definidos. A tabela a seguir explica cada parâmetro com sua utilização.

| Nome&nbsp;do parâmetro | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna datetime nos dados de entrada usados para criar a série temporal e inferir sua frequência.|✓|
|`grain_column_names`|Nomes que definem grupos de séries individuais nos dados de entrada. Se a agregação não for definida, o conjunto de dados será considerado uma série temporal.||
|`max_horizon`|Define o horizonte de previsão máximo desejado em unidades de frequência de série temporal. As unidades são baseadas no intervalo de tempo dos seus dados de treinamento (por exemplo, mensalmente, semanalmente) em que o forecaster deve prever.|✓|
|`target_lags`|Número de linhas para retardar os valores de destino com base na frequência dos dados. O retardo é representado como uma lista ou um único inteiro. O retardo deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponde ou se correlaciona por padrão. Por exemplo, a previsão da demanda de um produto em um dado mês pode depender do preço de mercadorias específicas 3 meses antes disso. Neste exemplo, você pode querer retardar o destino (demanda) negativamente por 3 meses para que o modelo seja treinado na relação correta.||
|`target_rolling_window_size`|*n* períodos históricos para usar os valores previstos gerado, <= o tamanho do conjunto de treinamento. Se omitido, *n* será o tamanho total do conjunto de treinamento. Especifique esse parâmetro quando desejar considerar apenas certa quantidade de histórico no treinamento do modelo.||
|`enable_dnn`|Habilitar previsão de DNNs.||

Para saber mais, confira a [documentação de referência do ](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

Crie as configurações de série temporal como um objeto de dicionário. Defina o `time_column_name` para o campo `day_datetime` no conjunto de dados. Defina o parâmetro `grain_column_names` para garantir que **dois grupos de série temporal** sejam criados para os dados: um para o armazenamento A e B. Por último, defina `max_horizon` para 50 para fazer a previsão do conjunto de teste completo. Defina uma janela de previsão para 10 períodos com `target_rolling_window_size` e especifique um retardo único nos valores de destino para dois períodos à frente com o parâmetro `target_lags`. Recomendamos definir `max_horizon`, `target_rolling_window_size` e `target_lags` como “automático”, que detectará automaticamente esses valores para você. No exemplo abaixo, as configurações “automáticas” foram usadas para esses parâmetros. 

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

Ao definir `grain_column_names` no snippet de código acima, o AutoML criará dois grupos de série temporal separados, também conhecidos como séries temporais múltiplas. Se nenhuma agregação for definida, o AutoML assumirá que o conjunto de data é uma única série temporal. Para saber mais sobre série temporal única, consulte [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Crie um objeto `AutoMLConfig` padrão especificando o tipo de tarefa `forecasting` e envie o experimento. Após a conclusão do modelo, recupere a melhor iteração de execução.

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

Consulte os [notebooks de amostra de previsão](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para ver exemplos de código detalhados de configuração de previsão avançada, incluindo:

* [detecção de feriados e engenharia de recursos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [retardos configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [recursos de agregação de janela sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurar um experimento de previsão habilitado para DNN

> [!NOTE]
> O suporte do DNN para previsão no machine learning automatizado está em versão preliminar e não tem suporte para execuções locais.

Para aproveitar o DNNs para previsão, você precisará definir o parâmetro `enable_dnn` no AutoMLConfig como true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Saiba mais sobre [o AutoMLConfig](#configure-and-run-experiment).

Como alternativa, você pode selecionar a opção `Enable deep learning` no estúdio.
![alt text](./media/how-to-auto-train-forecast/enable_dnn.png)

Recomendamos usar um cluster de cálculo do AML com SKUs do GPU e pelo menos dois nós como destino de computação. Para permitir tempo suficiente para que o treinamento do DNN seja concluído, recomendamos definir o tempo limite do experimento por algumas horas, no mínimo.
Para mais informações sobre a computação do AML e tamanhos de VM que incluam GPUs, consulte a [documentação de computação do AML](how-to-set-up-training-targets.md#amlcompute) e a [documentação de tamanhos de máquina virtual otimizada para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Veja o [notebook de previsão de produção de bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para conhecer um exemplo de código detalhado que aproveita DNNs.

### <a name="target-rolling-window-aggregation"></a>Agregações de janelas sem interrupção como destino
Frequentemente, a melhor informação que um forecaster pode ter é um valor recente no destino. Criar estatísticas cumulativas do destino pode aumentar a precisão das suas previsões. Ter agregações de janelas sem interrupção como destino permite adicionar uma agregação sem interrupção de valores de dados como recursos. Para habilitar as janelas sem interrupção como destino, defina `target_rolling_window_size` para o tamanho de janela inteiro desejado. 

Um exemplo disso pode ser visto ao prever a demanda de energia. Você pode adicionar um recurso de janela sem interrupção de três dias para levar em consideração as alterações térmicas de espaços aquecidos. No exemplo a seguir, criamos essa janela de tamanho três definindo `target_rolling_window_size=3` no construtor `AutoMLConfig`. A tabela mostra a engenharia de recursos que ocorre quando a agregação de janela é aplicada. Colunas para mínimo, máximo e soma são geradas em uma janela deslizante de três com base nas configurações definidas. Cada linha tem um novo recurso calculado, no caso do carimbo de data/hora de 8 de setembro de 2017 4:00, os valores máximo, mínimo e soma são calculados usando os valores de demanda em 8 de setembro de 2017 1:00–3:00. Essa janela de três se desloca para preencher os dados das linhas restantes.

![texto alternativo](./media/how-to-auto-train-forecast/target-roll.svg)

Gerar e usar esses recursos adicionais como dados contextuais extras ajuda a tornar o modelo de treinamento mais preciso.

Veja um exemplo de código Python que aproveita o [recurso de agregação de janela sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Exibir o resumo de engenharia de recursos

Para os tipos de tarefa de série temporal no machine learning automatizado, você pode exibir detalhes do processo de engenharia de recursos. O código a seguir mostra cada recurso bruto junto com os seguintes atributos:

* Nome do recurso bruto
* Número de recursos de engenharia formados fora desse recurso bruto
* Tipo detectado
* Se o recurso foi descartado
* Lista de transformações de recursos para o recurso bruto

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsões com o melhor modelo

Use a melhor iteração de modelo para prever valores para o conjunto de dados de teste.

A função `forecast()` deve ser usada em vez de `predict()`, isso permitirá especificar quando as previsões devem iniciar. No exemplo a seguir, você primeiro substitui todos os valores em `y_pred` por `NaN`. Neste caso, a origem da previsão estará no final dos dados de treinamento, como normalmente seria ao usar `predict()`. No entanto, se você substituiu apenas a segunda metade de `y_pred` por `NaN`, a função manterá os valores numéricos na primeira metade sem modificações, mas preverá os valores de `NaN` na segunda metade. A função retorna os valores previstos e os recursos alinhados.

Você também pode usar o parâmetro `forecast_destination` na função `forecast()` para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcule a raiz do erro quadrático médio (RMSE) entre os valores reais de `actual_labels` e os valores previstos em `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão geral do modelo foi determinada, a próxima etapa mais realista é usar o modelo para prever valores futuros desconhecidos. Forneça um conjunto de dados no mesmo formato do conjunto de teste `test_data`, mas com datetimes futuros, e o conjunto de previsão resultante será os valores previstos para cada etapa da série temporal. Suponha que os últimos registros de série temporal no conjunto de dados eram de 31/12/2018. Para prever a demanda do dia seguinte (ou quantos períodos forem necessários prever, até `max_horizon`), crie um único registro de série temporal para cada loja para 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Repita as etapas necessárias para carregar esses dados futuros em um dataframe e, em seguida, execute `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> Não é possível prever valores para o número de períodos maiores que `max_horizon`. Você precisa treinar de novo o modelo com um horizonte maior se quiser prever valores futuros além do horizonte atual.

## <a name="next-steps"></a>Próximas etapas

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experimentos com o machine learning automatizado.
* Veja a documentação de referência do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
