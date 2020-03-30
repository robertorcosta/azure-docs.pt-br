---
title: Auto-train um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure Machine Learning para treinar um modelo de regressão de previsão de séries tempois usando aprendizado de máquina automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d4e36c0d3838af85768453496a51ecd295c22b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081838"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto-train um modelo de previsão de série temporal
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a treinar um modelo de regressão de previsão de séries tempois usando aprendizado automático de máquina no Azure Machine Learning. A configuração de um modelo de previsão é semelhante à configuração de um modelo padrão de regressão usando aprendizado de máquina automatizado, mas existem certas opções de configuração e etapas de pré-processamento para trabalhar com dados de séries tempo. Os exemplos a seguir mostram como:

* Preparar dados para modelagem de séries tempois
* Configure parâmetros específicos de [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) série sinuosa em um objeto
* Executar previsões com dados de séries tempois

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Você pode usar ML automatizado para combinar técnicas e abordagens e obter uma previsão recomendada de séries tempológicas de alta qualidade. Um experimento automatizado de séries tempois é tratado como um problema de regressão multivariada. Os valores passados da série temporal são "pivotados" para se tornarem dimensões adicionais para o regressor, juntamente com outros preditores.

Essa abordagem, ao contrário dos métodos clássicos das séries tempois, tem a vantagem de incorporar naturalmente múltiplas variáveis contextuais e sua relação entre si durante o treinamento. Em aplicações de previsão do mundo real, vários fatores podem influenciar uma previsão. Por exemplo, ao prever vendas, interações de tendências históricas, taxa de câmbio e preço impulsionam conjuntamente o resultado das vendas. Outro benefício é que todas as inovações recentes nos modelos de regressão aplicam-se imediatamente à previsão.

Você pode [configurar](#config) até onde no futuro a previsão deve se estender (o horizonte de previsão), bem como lags e muito mais. O ML automatizado aprende um único modelo, mas muitas vezes ramificado internamente para todos os itens no conjunto de dados e horizontes de previsão. Assim, mais dados estão disponíveis para estimar parâmetros do modelo e a generalização para séries invisíveis.

As características extraídas dos dados de treinamento desempenham um papel crítico. E, o ML automatizado executa etapas padrão de pré-processamento e gera recursos adicionais de séries tempois para capturar efeitos sazonais e maximizar a precisão preditiva.

## <a name="time-series-and-deep-learning-models"></a>Modelos de séries tempois e deep learning


O ML automatizado fornece aos usuários modelos de séries tempois nativas e de aprendizagem profunda como parte do sistema de recomendação. Estes alunos incluem:
+ Profeta
+ Auto-ARIMA
+ ForecastTCN

O aprendizado profundo do ML automatizado permite prever dados de séries tempois univariadas e multivariadas.

Modelos de aprendizagem profunda têm três capacidades intrínsecas:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Eles suportam várias entradas e saídas
1. Eles podem extrair automaticamente padrões em dados de entrada que se estendem por longas seqüências

Dado os dados maiores, modelos de aprendizagem profunda, como o ForecastTCN da Microsoft, podem melhorar as pontuações do modelo resultante. 

Os alunos de séries tempois nativas também são fornecidos como parte do ML automatizado. Prophet trabalha melhor com séries temporiais que têm fortes efeitos sazonais e várias temporadas de dados históricos. Prophet é preciso & rápido, robusto para outliers, dados ausentes e mudanças dramáticas em sua série temporal. 

A ARIMA é um método estatístico popular para previsão de séries tempois. Essa técnica de previsão é comumente utilizada em cenários de previsão de curto prazo onde os dados mostram evidências de tendências como ciclos, que podem ser imprevisíveis e difíceis de modelar ou prever. O Auto-ARIMA transforma seus dados em dados estacionários para obter resultados consistentes e confiáveis.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para criar o espaço de trabalho, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).
* Este artigo assume a familiaridade básica com a criação de um experimento automatizado de aprendizagem de máquina. Siga o [tutorial](tutorial-auto-train-models.md) ou [como](how-to-configure-auto-train.md) ver os padrões básicos de design de experimentos automatizados de aprendizagem de máquina.

## <a name="preparing-data"></a>Elaboração de dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão dentro do aprendizado automatizado de máquina é incluir um recurso em seus dados que representa uma série de tempo válida. Uma série de tempo regular tem uma freqüência bem definida e consistente e tem um valor em cada ponto amostral em um período de tempo contínuo. Considere o seguinte instantâneo `sample.csv`de um arquivo .

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

Este conjunto de dados é um exemplo simples de dados diários de vendas para uma `week_of_year` empresa que possui duas lojas diferentes, A e B. Além disso, há um recurso para que permitirá que o modelo detecte sazonalidade semanal. O `day_datetime` campo representa uma série de tempo limpo `sales_quantity` com frequência diária, e o campo é a coluna-alvo para executar previsões. Leia os dados em um dataframe `to_datetime` do Pandas e use `datetime` a função para garantir que a série temporal seja um tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Neste caso, os dados já estão classificados `day_datetime`ascendentes pelo campo do tempo . No entanto, ao configurar um experimento, certifique-se de que a coluna de tempo desejada seja classificada em ordem crescente para construir uma série de tempo válida. Suponha que os dados contenham 1.000 registros e faça uma divisão determinística nos dados para criar conjuntos de dados de treinamento e teste. Identifique o nome da coluna do rótulo e defina-o como rótulo. Neste exemplo, o rótulo `sales_quantity`será . Em seguida, separe o campo de `test_data` etiqueta para formar o `test_target` conjunto.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para prever valores futuros, certifique-se de que todos os recursos usados no treinamento possam ser usados ao executar previsões para o seu horizonte pretendido. Por exemplo, ao criar uma previsão de demanda, incluindo um recurso para o preço atual das ações poderia aumentar maciçamente a precisão do treinamento. No entanto, se você pretende prever com um horizonte longo, você pode não ser capaz de prever com precisão os valores futuros de ações correspondentes a pontos futuros de série sotempo, e a precisão do modelo pode sofrer.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configure e execute experimentos

Para tarefas de previsão, o aprendizado automatizado de máquina usa etapas de pré-processamento e estimativa específicas para dados de séries tempo-tempo. As seguintes etapas de pré-processamento serão executadas:

* Detecte a freqüência da amostra de séries temporias (por exemplo, por hora, diariamente, semanal) e crie novos registros de pontos de tempo ausentes para tornar a série contínua.
* Imputar valores ausentes no destino (via preenchimento de avanço) e colunas de recursos (usando valores medianos de coluna)
* Crie recursos baseados em grãos para permitir efeitos fixos em diferentes séries
* Crie recursos baseados no tempo para ajudar a aprender padrões sazonais
* Codificar variáveis categóricas para quantidades numéricas

O [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objeto define as configurações e dados necessários para uma tarefa automatizada de aprendizado de máquina. Semelhante a um problema de regressão, você define parâmetros de treinamento padrão como tipo de tarefa, número de iterações, dados de treinamento e número de validações cruzadas. Para as tarefas de previsão, existem parâmetros adicionais que devem ser definidos que afetam o experimento. A tabela a seguir explica cada parâmetro e seu uso.

| Nome&nbsp;do parâmetro | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna datetime nos dados de entrada usados para construir a série temporal e inferir sua frequência.|✓|
|`grain_column_names`|Nome(s) definindo grupos de séries individuais nos dados de entrada. Se o grão não for definido, o conjunto de dados é assumido como uma série temporal.||
|`max_horizon`|Define o horizonte máximo de previsão desejado em unidades de freqüência de séries tempois. As unidades são baseadas no intervalo de tempo de seus dados de treinamento, por exemplo, mensalmente, semanalmente que o preditor deve prever.|✓|
|`target_lags`|Número de linhas para defasar os valores-alvo com base na frequência dos dados. O lag é representado como uma lista ou inteiro único. O lag deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponder ou correlacionar por padrão. Por exemplo, ao tentar prever a demanda por um produto, a demanda em qualquer mês pode depender do preço de commodities específicas 3 meses antes. Neste exemplo, você pode querer atrasar negativamente a meta (demanda) em 3 meses para que o modelo esteja treinando na relação correta.||
|`target_rolling_window_size`|*n* períodos históricos a serem utilizados para gerar valores previstos, <= tamanho do conjunto de treinamento. Se omitido, *n* é o tamanho completo do conjunto de treinamento. Especifique este parâmetro quando você só quiser considerar uma certa quantidade de história ao treinar o modelo.||
|`enable_dnn`|Habilite dnns de previsão.||

Consulte a [documentação de referência](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter mais informações.

Crie as configurações da série temporal como um objeto de dicionário. Defina `time_column_name` o `day_datetime` campo no conjunto de dados. Defina `grain_column_names` o parâmetro para garantir que **dois grupos de séries tempois separados** sejam criados para os dados; um para a loja A e `max_horizon` B. Por último, defina o para 50, a fim de prever para todo o conjunto de testes. Defina uma janela de `target_rolling_window_size`previsão para 10 períodos com , e `target_lags` especifique um único atraso nos valores-alvo por dois períodos à frente com o parâmetro. Recomenda-se definir `max_horizon`e `target_rolling_window_size` `target_lags` "auto" que detectará automaticamente esses valores para você. No exemplo abaixo, foram utilizadas configurações "auto" para esses parâmetros. 

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

Ao definir `grain_column_names` o trecho no trecho de código acima, o AutoML criará dois grupos de séries tempois separados, também conhecidos como séries tempontos múltiplas. Se nenhum grão for definido, o AutoML assumirá que o conjunto de dados é uma única série temporal. Para saber mais sobre séries tempois únicas, consulte o [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Agora crie `AutoMLConfig` um objeto padrão, especificando o `forecasting` tipo de tarefa e envie o experimento. Depois que o modelo terminar, recupere a melhor iteração de execução.

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

Consulte os [cadernos de amostra de previsão](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de código detalhados da configuração avançada de previsão, incluindo:

* [detecção e featurização de feriados](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem de rolamento](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [lags configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [rolando janelas características agregadas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configure um experimento de previsão de habilitação DNN

> [!NOTE]
> O suporte ao DNN para previsão em Machine Learning automatizado está no Preview e não é suportado para corridas locais.

Para aproveitar dnns para previsão, você precisará `enable_dnn` definir o parâmetro no AutoMLConfig como verdadeiro. 

Recomendamos o uso de um cluster AML Compute com SKUs gpu e pelo menos dois nós como alvo de computação. Para permitir tempo suficiente para que o treinamento dnn seja concluído, recomendamos definir o tempo de intervalo do experimento para um mínimo de algumas horas.
Para obter mais informações sobre os tamanhos de maquete e VM que incluem GPU's, consulte a [documentação aml computada](how-to-set-up-training-targets.md#amlcompute) e a [documentação de tamanhos de máquinas virtuais otimizados da GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Exibir o [caderno Previsão de Produção de Bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para obter um exemplo de código detalhado aproveitando DNNs.

### <a name="view-feature-engineering-summary"></a>Exibir resumo de engenharia de recursos

Para tipos de tarefas em séries temporias em aprendizado automático de máquina, você pode visualizar detalhes do processo de engenharia de recursos. O código a seguir mostra cada recurso bruto juntamente com os seguintes atributos:

* Nome do recurso bruto
* Número de recursos projetados formados a partir desta característica bruta
* Tipo detectado
* Se o recurso foi descartado
* Lista de transformações de recursos para o recurso bruto

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsão com melhor modelo

Use a melhor iteração do modelo para prever valores para o conjunto de dados do teste.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternativamente, você pode `forecast()` usar `predict()`a função em vez de , o que permitirá especificações de quando as previsões devem começar. No exemplo a seguir, você `y_pred` primeiro `NaN`substitui todos os valores com . A origem da previsão será no final dos dados de treinamento `predict()`neste caso, como normalmente seria quando se utiliza. No entanto, se você substituísse apenas a segunda metade com `y_pred` `NaN`, a função deixaria os `NaN` valores numéricos no primeiro semestre sem modificações, mas previu os valores no segundo semestre. A função retorna tanto os valores previstos quanto os recursos alinhados.

Você também pode `forecast_destination` usar o `forecast()` parâmetro na função para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcular RMSE (erro quadrado médio `actual_labels` raiz) entre os valores `predict_labels`reais e os valores previstos em .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão geral do modelo foi determinada, o próximo passo mais realista é usar o modelo para prever valores futuros desconhecidos. Forneça um conjunto de dados no `test_data` mesmo formato do conjunto de testes, mas com datas futuras, e o conjunto de previsão resultante são os valores previstos para cada etapa de série-tempo. Suponha que os últimos registros da série temporal no conjunto de dados foram para 31/12/2018. Para prever a demanda para o dia seguinte (ou quantos períodos você precisa prever, <= `max_horizon`), criar um único recorde de séries temporem para cada loja para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita as etapas necessárias para carregar esses `best_run.predict(test_data)` dados futuros em um quadro de dados e, em seguida, execute para prever valores futuros.

> [!NOTE]
> Os valores não podem ser previstos para um número de períodos maiores que o `max_horizon`. O modelo deve ser retreinado com um horizonte maior para prever valores futuros além do horizonte atual.

## <a name="next-steps"></a>Próximas etapas

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender como criar experimentos com aprendizado de máquina automatizado.
* Exibir o [Azure Machine Learning SDK para obter](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) documentação de referência python.
