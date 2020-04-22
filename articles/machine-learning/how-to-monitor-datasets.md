---
title: Analisar e monitorar a deriva de dados em conjuntos de dados (visualização)
titleSuffix: Azure Machine Learning
description: Crie monitores de conjuntos de dados do Azure Machine Learning (visualização), monitore a deriva de dados nos conjuntos de dados e configure alertas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682716"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Detectar deriva de dados (visualização) em conjuntos de dados
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como criar monitores de conjunto de dados de Machine Learning do Azure (visualização), monitorar a deriva de dados e mudanças estatísticas nos conjuntos de dados e configurar alertas.

Com monitores de conjunto de dados de aprendizado de máquina do Azure, você pode:
* **Analise a deriva em seus dados** para entender como ele muda com o tempo.
* **Monitore os dados do modelo** para obter diferenças entre o treinamento e o serviço de conjuntos de dados.
* **Monitore novos dados** para obter diferenças entre qualquer linha de base e conjunto de dados de destino.
* **Os recursos do perfil nos dados** para rastrear como as propriedades estatísticas mudam ao longo do tempo.
* **Configure alertas sobre deriva de dados** para avisos antecipados para possíveis problemas. 

Métricas e insights estão disponíveis através do recurso [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associado ao espaço de trabalho Azure Machine Learning.

> [!Important]
> Observe que o monitoramento de dados com o SDK está disponível em todas as edições, enquanto o monitoramento de dados passa pelo estúdio na web é apenas a edição Enterprise.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com monitores de conjunto de dados, você precisa:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Um [espaço de trabalho de aprendizado de máquina do Azure.](how-to-manage-workspace.md)
* O [Azure Machine Learning SDK for Python é instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.
* Dados estruturados (tabular) com um carimbo de tempo especificado no caminho do arquivo, nome do arquivo ou coluna nos dados.

## <a name="what-is-data-drift"></a>O que é deriva de dados?

No contexto da machine learning, o desvio de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, assim, monitorar a deriva de dados ajuda a detectar problemas de desempenho do modelo.

As causas do desvio de dados incluem: 

- Mudanças no processo a montante, como a substituição de um sensor que muda as unidades de medição de centímetros para centímetros. 
- Problemas de qualidade de dados, como um sensor quebrado sempre lendo 0.
- Deriva natural nos dados, como a temperatura média mudando com as estações do ano.
- Mudança na relação entre características, ou mudança de covariado. 

Com os monitores do conjunto de dados Azure Machine Learning, você pode configurar alertas que auxiliam na detecção de deriva de dados em conjuntos de dados ao longo do tempo. 

### <a name="dataset-monitors"></a>Monitores de conjunto de dados 

Você pode criar um monitor de conjunto de dados para detectar e alertar para a deriva de dados em novos dados em um conjunto de dados, analisar dados históricos para drift e perfilar novos dados ao longo do tempo. O algoritmo de deriva de dados fornece uma medida geral de mudança nos dados e indicação de quais características são responsáveis por uma investigação mais aprofundada. Os monitores do conjunto de dados produzem uma série `timeseries` de outras métricas, estabelecendo novos dados no conjunto de dados. O alerta personalizado pode ser configurado em todas as métricas geradas pelo monitor através do [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Monitores de conjunto de dados podem ser usados para capturar rapidamente problemas de dados e reduzir o tempo para depurar o problema, identificando causas prováveis.  

Conceitualmente, existem três cenários principais para configurar monitores de conjunto de dados no Azure Machine Learning.

Cenário | Descrição
---|---
Monitorando os dados de serviço de um modelo para deriva dos dados de treinamento do modelo | Os resultados desse cenário podem ser interpretados como o monitoramento de um proxy para a precisão do modelo, dado que a precisão do modelo se degrada se os dados de serviço se desviarem dos dados de treinamento.
Monitoramento de um conjunto de dados de séries tempontos para deriva de um período de tempo anterior. | Este cenário é mais geral e pode ser usado para monitorar conjuntos de dados envolvidos a montante ou a jusante da construção de modelos.  O conjunto de dados de destino deve ter uma coluna de carimbo de tempo, enquanto o conjunto de dados da linha de base pode ser qualquer conjunto de dados tabular que tenha recursos em comum com o conjunto de dados de destino.
Realizando análises em dados passados. | Esse cenário pode ser usado para entender dados históricos e informar decisões em configurações para monitores de conjunto de dados.

## <a name="how-dataset-can-monitor-data"></a>Como o conjunto de dados pode monitorar dados

Usando o Azure Machine Learning, o desvio de dados é monitorado através de conjuntos de dados. Para monitorar a deriva de dados, um conjunto de dados de linha de base - geralmente o conjunto de dados de treinamento para um modelo - é especificado. Um conjunto de dados de destino - geralmente dados de entrada do modelo - é comparado ao longo do tempo com o conjunto de dados da linha de base. Essa comparação significa que seu conjunto de dados de destino deve ter uma coluna de carimbo de tempo especificada.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Defina `timeseries` o traço no conjunto de dados de destino

O conjunto de dados `timeseries` de destino precisa ter o traço definido nele, especificando a coluna carimbo de ponto-hora, seja a partir de uma coluna nos dados ou de uma coluna virtual derivada do padrão de caminho dos arquivos. Isso pode ser feito através do estúdio Python SDK ou Azure Machine Learning. Uma coluna representando um carimbo de tempo de "grão fino" deve ser especificada para adicionar `timeseries` traço ao conjunto de dados. Se seus dados forem divididos em estrutura de pastas com informações de tempo, como '{yyyy/MM/dd}', você pode criar uma coluna virtual através da configuração do padrão de caminho e defini-los como o carimbo de tempo "grão grosseiro" para melhorar a importância da funcionalidade da série temporal. 

#### <a name="python-sdk"></a>SDK do Python

O [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) método [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) da classe define a coluna de carimbo de hora para o conjunto de dados. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Para obter um exemplo `timeseries` completo de uso do traço dos conjuntos de dados, consulte o [notebook de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) ou a [documentação sDK dos conjuntos](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)de dados .

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Se você criar seu conjunto de dados usando o estúdio Azure Machine Learning, certifique-se de que o caminho para seus dados contenha informações de carimbo de data e hora, inclua todas as subpastas com dados e defina o formato de partição. 

No exemplo a seguir, todos os dados sob a subpasta *NoaaIsdFlorida/2019* são tomados e o formato de partição especifica o ano, mês e dia do carimbo de tempo. 

[![Formato de partição](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nas configurações **de Esquemaa,** especifique a coluna carimbo de tempo de uma coluna virtual ou real no conjunto de dados especificado:

![Timestamp](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Configurações do monitor do conjunto de dados

Depois de criar seu conjunto de dados com as configurações de carimbo de tempo especificadas, você está pronto para configurar o monitor do conjunto de dados.

As várias configurações do monitor do conjunto de dados são divididas em três grupos: **informações básicas, configurações do monitor** e **configurações de recarga**.

### <a name="basic-info"></a>Informações básicas

Esta tabela contém configurações básicas usadas para o monitor do conjunto de dados.

| Configuração | Descrição | Dicas | Mutável | 
| ------- | ----------- | ---- | ------- | 
| Nome | Nome do monitor do conjunto de dados. | | Não |
| Conjunto de dados da linha de base | Conjunto de dados tabular que será usado como linha de base para comparação do conjunto de dados de destino ao longo do tempo. | O conjunto de dados da linha de base deve ter características em comum com o conjunto de dados de destino. Geralmente, a linha de base deve ser definida como conjunto de dados de treinamento de um modelo ou uma fatia do conjunto de dados de destino. | Não |
| Conjunto de dados de destino | Conjunto de dados tabular especifique a coluna carimbo de tempo especificada que será analisada para deriva de dados. | O conjunto de dados de destino deve ter características em `timeseries` comum com o conjunto de dados da linha de base, e deve ser um conjunto de dados, a qual novos dados são anexados. Dados históricos no conjunto de dados-alvo podem ser analisados, ou novos dados podem ser monitorados. | Não | 
| Frequência | A frequência que será usada para agendar o trabalho do pipeline e analisar dados históricos se estiver executando um backfill. As opções incluem diária, semanal ou mensal. | Ajuste esta configuração para incluir um tamanho comparável de dados à linha de base. | Não | 
| Recursos | Lista de recursos que serão analisados para deriva de dados ao longo do tempo. | Defina-se para os recursos de saída de um modelo para medir a deriva do conceito. Não inclua características que naturalmente se afastam ao longo do tempo (mês, ano, índice, etc.). Você pode reabastecer e monitorar de deriva de dados existente depois de ajustar a lista de recursos. | Sim | 
| Destino de computação | Alvo de computação do Azure Machine Learning para executar os trabalhos de monitor do conjunto de dados. | | Sim | 

### <a name="monitor-settings"></a>Configurações do monitor

Essas configurações são para o pipeline de monitor de conjunto de dados programado, que será criado. 

| Configuração | Descrição | Dicas | Mutável | 
| ------- | ----------- | ---- | ------- |
| Habilitar | Habilite ou desative o cronograma no pipeline do monitor do conjunto de dados | Desative o cronograma para analisar dados históricos com a configuração de preenchimento de recarga. Ele pode ser ativado após a criação do monitor do conjunto de dados. | Sim | 
| Latency | O tempo, em horas, leva para os dados chegarem no conjunto de dados. Por exemplo, se levar três dias para os dados chegarem no SQL DB, o conjunto de dados encapsula, defina a latência para 72. | Não é possível ser alterado depois que o monitor do conjunto de dados é criado | Não | 
| Endereços de email | Endereços de e-mail para alertar com base na violação do limite percentual de deriva de dados. | Os e-mails são enviados através do Azure Monitor. | Sim | 
| Limite | Limite percentual de deriva de dados para alerta de e-mail. | Outros alertas e eventos podem ser definidos em muitas outras métricas no recurso application insights associado do espaço de trabalho. | Sim | 

### <a name="backfill-settings"></a>Configurações de recarga

Essas configurações são para executar um backfill em dados passados para métricas de deriva de dados.

| Configuração | Descrição | Dicas |
| ------- | ----------- | ---- |
| Data de início | Data de início do trabalho de preenchimento. | | 
| Data de término | Data final do trabalho de preenchimento. | A data de término não pode ser superior a 31*unidades de freqüência a partir da data de início. Em um monitor de conjunto de dados existente, as métricas podem ser repreenchidas para analisar dados históricos ou substituir métricas por configurações atualizadas. |

## <a name="create-dataset-monitors"></a>Criar monitores de conjunto de dados 

Crie monitores de conjunto de dados para detectar e alertar para a deriva de dados em um novo conjunto de dados com o estúdio Azure Machine Learning ou o Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Para configurar alertas no monitor do conjunto de dados, o espaço de trabalho que contém o conjunto de dados para o que você deseja criar deve ter recursos de edição Enterprise. 

Depois que a funcionalidade do espaço de trabalho for confirmada, navegue até a página inicial do estúdio e selecione a guia Conjuntos de dados à esquerda. Selecione monitores Dataset.

![Lista de monitores](./media/how-to-monitor-datasets/monitor-list.png)

Clique no botão **+Criar monitor** e continuar através do assistente clicando em **Next**.

![Assistente](./media/how-to-monitor-datasets/wizard.png)

O monitor de conjunto de dados resultante aparecerá na lista. Selecione-o para ir à página de detalhes do monitor.

### <a name="from-python-sdk"></a>De Python SDK

Consulte a documentação de referência do [Python SDK no desvio de dados](/python/api/azureml-datadrift/azureml.datadrift) para obter detalhes completos. 

O exemplo a seguir mostra como criar um monitor de conjunto de dados usando o Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Para um exemplo completo `timeseries` de configuração de um conjunto de dados e detector de deriva de dados, consulte nosso [caderno de exemplo](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Entendendo os resultados da deriva de dados

O monitor de dados produz dois grupos de resultados: visão geral do Drift e detalhes de recursos. A animação a seguir mostra os gráficos disponíveis do monitor de deriva com base no recurso e métrica selecionados. 

![Vídeo de demonstração](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Visão geral do drift

A seção **visão geral drift** contém insights de alto nível sobre a magnitude da deriva de dados e quais características devem ser investigadas posteriormente. 

| Métrica | Descrição | Dicas | 
| ------ | ----------- | ---- | 
| Magnitude da deriva de dados | Dado como uma porcentagem entre a linha de base e o conjunto de dados de destino ao longo do tempo. Variando de 0 a 100 onde 0 indica conjuntos de dados idênticos e 100 indica que o recurso de deriva de dados do Azure Machine Learning pode diferenciar completamente os dois conjuntos de dados. | O ruído no percentual preciso medido é esperado devido às técnicas de aprendizado de máquina que estão sendo usadas para gerar essa magnitude. | 
| Contribuição à deriva por recurso | A contribuição de cada recurso no conjunto de dados-alvo para a magnitude de deriva medida. |  Devido à mudança de covariado, a distribuição subjacente de um recurso não precisa necessariamente mudar para ter uma importância de recurso relativamente alta. | 

A imagem a seguir é um exemplo de gráficos vistos na **visão geral** do Drift no estúdio Azure Machine Learning, resultante de um backfill de [Noaa Integrated Surface Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Os dados foram `stationName contains 'FLORIDA'`amostrados, sendo janeiro de 2019 utilizados como conjunto de dados de linha de base e todos os dados de 2019 utilizados como alvo.
 
![Visão geral do drift](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Detalhes do recurso

A seção **Detalhes do Recurso** contém insights de nível de recurso sobre a alteração na distribuição do recurso selecionado, bem como outras estatísticas, ao longo do tempo. 

O conjunto de dados de destino também é perfilado ao longo do tempo. A distância estatística entre a distribuição de linha de base de cada recurso é comparada com a do conjunto de dados alvo ao longo do tempo, que é conceitualmente semelhante à magnitude da deriva de dados, com a exceção de que essa distância estatística é para uma característica individual. Min, max e média também estão disponíveis. 

No estúdio Azure Machine Learning, se você clicar em um ponto de dados no gráfico, a distribuição do recurso que está sendo mostrado será ajustada de acordo. Por padrão, ele mostra a distribuição do conjunto de dados da linha de base e a distribuição da execução mais recente do mesmo recurso. 

Essas métricas também podem ser recuperadas no `get_metrics()` Python SDK através do método em um `DataDriftDetector` objeto. 

#### <a name="numeric-features"></a>Características numéricas 

Os recursos numéricos são perfilados em cada execução do monitor do conjunto de dados. Os seguintes são expostos no estúdio Azure Machine Learning. A densidade de probabilidade é mostrada para a distribuição.

| Métrica | Descrição |  
| ------ | ----------- |  
| Distância de Wasserstein | Quantidade mínima de trabalho para transformar a distribuição da linha de base na distribuição-alvo. |
| Valor médio | Valor médio do recurso. |
| Valor mínimo | Valor mínimo do recurso. |
| Valor máximo | Valor máximo do recurso. |

![Detalhes do recurso nuéter](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Características categóricas 

Os recursos numéricos são perfilados em cada execução do monitor do conjunto de dados. Os seguintes são expostos no estúdio Azure Machine Learning. Um histograma é mostrado para a distribuição.

| Métrica | Descrição |  
| ------ | ----------- |  
| Distância euclidiana | Distância geométrica entre a linha de base e as distribuições de alvo. |
| Valores únicos | Número de valores únicos (cardinalidade) do recurso. |


![Detalhes do recurso categóricos](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Métricas, alertas e eventos

As métricas podem ser consultadas no recurso [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associado ao seu espaço de trabalho de aprendizado de máquina. O que dá acesso a todos os recursos do Application Insights, incluindo a configuração de regras de alerta personalizadas e grupos de ação para desencadear uma ação como, uma função de e-mail/SMS/Push/Voice ou Azure. Consulte a documentação completa do Application Insights para obter detalhes. 

Para começar, navegue até o portal Azure e selecione a página **Visão Geral** do seu espaço de trabalho.  O recurso de insights de aplicativos associado está na extrema direita:

[![Visão geral do portal do Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecione Logs (Analytics) em Monitoramento no painel esquerdo:

![Visão geral dos insights dos aplicativos](./media/how-to-monitor-datasets/ai-overview.png)

As métricas do monitor `customMetrics`do conjunto de dados são armazenadas como . Você pode gravar e executar uma consulta depois de configurar um monitor de conjunto de dados para visualizá-los:

[![Consulta de análise de log](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Depois de identificar métricas para configurar regras de alerta, crie uma nova regra de alerta:

![Nova regra de alerta](./media/how-to-monitor-datasets/alert-rule.png)

Você pode usar um grupo de ação existente ou criar um novo para definir a ação a ser tomada quando as condições definidas forem atendidas:

![Novo grupo de ação](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Solução de problemas

Limitações e problemas conhecidos:

* O intervalo de tempo dos trabalhos de recarga é limitado a 31 intervalos da configuração de freqüência do monitor. 
* Limitação de 200 recursos, a menos que uma lista de recursos não seja especificada (todos os recursos usados).
* O tamanho da computação deve ser grande o suficiente para lidar com os dados. 
* Certifique-se de que seu conjunto de dados tenha dados dentro da data de início e término de uma determinada execução do monitor.
* Os monitores de conjunto de dados só funcionarão em conjuntos de dados que contenham 50 linhas ou mais. 

As colunas, ou características, no conjunto de dados são classificadas como categóricas ou numéricas com base nas condições da tabela a seguir. Se o recurso não atender a essas condições - por exemplo, uma coluna de string de tipo com >100 valores únicos - o recurso é descartado do nosso algoritmo de deriva de dados, mas ainda é perfilado. 

| Tipo de recurso | Tipo de dados | Condição | Limitações | 
| ------------ | --------- | --------- | ----------- |
| Categóricos | string, bool, int, flutuar | O número de valores únicos no recurso é inferior a 100 e menos de 5% do número de linhas. | Nulo é tratado como sua própria categoria. | 
| Numérico | int, flutuar | Os valores no recurso são de um tipo de dados numérico e não atendem à condição para um recurso categórico. | Recurso descartado se >15% dos valores forem nulos. | 

## <a name="next-steps"></a>Próximas etapas

* Dirija-se ao [estúdio Azure Machine Learning](https://ml.azure.com) ou ao notebook [Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) para configurar um monitor de conjunto de dados.
* Veja como configurar a deriva de dados em [modelos implantados no Azure Kubernetes Service](how-to-monitor-data-drift.md).
* Configure monitores de deriva de conjunto de dados com [grade de eventos](how-to-use-event-grid.md). 
