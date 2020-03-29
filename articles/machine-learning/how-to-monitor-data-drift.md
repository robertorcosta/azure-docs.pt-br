---
title: Detectar deriva de dados em implantações AKS
titleSuffix: Azure Machine Learning
description: Detecte o desvio de dados (visualização) no Azure Kubernetes Service implantado em modelos no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537000"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detectar deriva de dados (visualização) em modelos implantados no Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprende a monitorar a deriva de dados entre o conjunto de dados de treinamento e os dados de inferência de um modelo implantado. No contexto do aprendizado de máquina, modelos treinados de aprendizagem de máquina podem experimentar desempenho de predição degradado por causa da deriva. Com o Azure Machine Learning, você pode monitorar a deriva de dados e o serviço pode enviar um alerta de e-mail para você quando o drift for detectado.

## <a name="what-is-data-drift"></a>O que é deriva de dados?

No contexto da machine learning, o desvio de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, assim, monitorar a deriva de dados ajuda a detectar problemas de desempenho do modelo. 

## <a name="what-can-i-monitor"></a>O que posso monitorar?

Com o Azure Machine Learning, você pode monitorar as entradas de um modelo implantado no AKS e comparar esses dados com o conjunto de dados de treinamento para o modelo. Em intervalos regulares, os dados de inferência são [instantâneos e perfilados,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)em seguida, calculados com base no conjunto de dados para produzir uma análise de deriva de dados que: 

+ Mede a magnitude da deriva de dados, chamada coeficiente de deriva.
+ Mede a contribuição de deriva de dados por recurso, indicando quais características causaram deriva de dados.
+ Mede métricas de distância. Atualmente, Wasserstein e Energy Distance são computados.
+ Mede distribuições de recursos. Atualmente, estimação de densidade do kernel e histogramas.
+ Envie alertas para a deriva de dados por e-mail.

> [!Note]
> Este serviço está em (visualização) e limitado em opções de configuração. Consulte nossa Documentação de [API](https://docs.microsoft.com/python/api/azureml-datadrift/) e [Notas de Lançamento](azure-machine-learning-release-notes.md) para obter detalhes e atualizações. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Como o deriva de dados é monitorado no Azure Machine Learning

Usando o Azure Machine Learning, o desvio de dados é monitorado através de conjuntos de dados ou implantações. Para monitorar a deriva de dados, um conjunto de dados de linha de base - geralmente o conjunto de dados de treinamento para um modelo - é especificado. Um segundo conjunto de dados - geralmente dados de entrada de modelo coletados a partir de uma implantação - é testado contra o conjunto de dados da linha de base. Ambos os conjuntos de dados são perfilados e inseridos no serviço de monitoramento de deriva de dados. Um modelo de aprendizagem de máquina é treinado para detectar diferenças entre os dois conjuntos de dados. O desempenho do modelo é convertido para o coeficiente de deriva, que mede a magnitude da deriva entre os dois conjuntos de dados. [Utilizando-se a interpretável do modelo,](how-to-machine-learning-interpretability.md)as características que contribuem para o coeficiente de deriva são computadas. A partir do perfil do conjunto de dados, as informações estatísticas sobre cada recurso são rastreadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O SDK do Azure Machine Learning para Python instalado. Use as instruções em [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para fazer o seguinte:

    - Criar um ambiente Miniconda
    - Instalar o SDK do Azure Machine Learning para Python

- Um [espaço de trabalho de aprendizado de máquina do Azure.](how-to-manage-workspace.md)

- Um [arquivo de configuração](how-to-configure-environment.md#workspace)de espaço de trabalho .

- Instale o SDK de deriva de dados usando o seguinte comando:

    ```shell
    pip install azureml-datadrift
    ```

- Crie um conjunto de [dados a](how-to-create-register-datasets.md) partir dos dados de treinamento do seu modelo.

- Especifique o conjunto de dados de treinamento ao [registrar](concept-model-management-and-deployment.md) o modelo. O exemplo a `datasets` seguir demonstra o uso do parâmetro para especificar o conjunto de dados do treinamento:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Habilite](how-to-enable-data-collection.md) a coleta de dados do modelo para coletar dados da `modeldata` implantação do modelo e confirmar que os dados estão sendo coletados no recipiente blob.

## <a name="configure-data-drift"></a>Configurar deriva de dados
Para configurar o desvio de dados para o seu experimento, importe dependências como visto no exemplo python a seguir. 

Este exemplo demonstra [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) a configuração do objeto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Enviar uma execução do DataDriftDetector

Com `DataDriftDetector` o objeto configurado, você pode enviar uma [execução de deriva de dados](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) em uma determinada data para o modelo. Como parte da execução, habilite os `drift_threshold` alertas do DataDriftDetector definindo o parâmetro. Se o [datadrift_coefficient](#visualize-drift-metrics) estiver `drift_threshold`acima do dado, um e-mail será enviado.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualize métricas de deriva

<a name="metrics"></a>

Depois de enviar sua execução do DataDriftDetector, você poderá ver as métricas de deriva salvas em cada iteração de execução para uma tarefa de deriva de dados:


|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Calculada de forma semelhante ao coeficiente de correlação de Matthew, mas esta saída é um número real que varia de 0 a 1. No contexto de deriva, 0 indica não deriva e 1 indica deriva máxima.|
datadrift_contribution|Característica importância de recursos que contribuem para a deriva.|

Existem várias maneiras de visualizar métricas de deriva:

* Use `RunDetails`o [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Use [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) a função `datadrift` em qualquer objeto de execução.
* Veja as métricas da seção **Modelos** do seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).

O exemplo python a seguir demonstra como traçar métricas relevantes de deriva de dados. Você pode usar as métricas retornadas para construir visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Veja os dados de deriva detectados pelo Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Agendar varreduras de deriva de dados 

Quando você habilita a detecção de deriva de dados, um DataDriftDetector é executado na freqüência programada especificada. Se o datadrift_coefficient chegar `drift_threshold`ao dado, um e-mail é enviado a cada execução agendada. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

A configuração do detector de deriva de dados pode ser vista em **Models** na guia **Detalhes** em seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).

[![Azure Machine Learning estúdio Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Veja os resultados em seu estúdio de Machine Learning do Azure

Para ver os resultados em seu espaço de trabalho no [estúdio Azure Machine Learning,](https://ml.azure.com)navegue até a página do modelo. Na aba de detalhes do modelo, a configuração de deriva de dados é mostrada. Uma guia **de deriva de dados** está agora disponível visualizando as métricas de deriva de dados. 

[![Azure Machine Learning estúdio Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Recebendo alertas de deriva

Ao definir o limiar de alerta do coeficiente drift e fornecer um endereço de e-mail, um alerta de e-mail [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é enviado automaticamente sempre que o coeficiente de deriva estiver acima do limite. 

Para que você configure alertas e ações personalizadas, todas as métricas de deriva de dados são armazenadas no recurso [Application Insights](how-to-enable-app-insights.md) que foi criado juntamente com o espaço de trabalho do Azure Machine Learning. Você pode seguir o link no alerta de e-mail para a consulta do Application Insights.

![Alerta de e-mail de desvio de dados](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Retreinar seu modelo após drift

Quando a deriva de dados impacta negativamente o desempenho do seu modelo implantado, é hora de retreinar o modelo. Para isso, proceda com as seguintes etapas.

* Investigue os dados coletados e prepare dados para treinar o novo modelo.
* Divida-o em dados de trem/teste.
* Treine o modelo novamente usando os novos dados.
* Avaliar o desempenho do modelo recém-gerado.
* Implantar novo modelo se o desempenho for melhor que o modelo de produção.

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo completo de uso de deriva de dados, consulte o [notebook de deriva de dados Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Este Jupyter Notebook demonstra o uso [de um Conjunto de Dados Abertos do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para treinar um modelo para prever o tempo, implantá-lo no AKS e monitorar a deriva de dados. 

* Detectar deriva de dados com [monitores de conjunto de dados](how-to-monitor-datasets.md).

* Gostaríamos muito de suas perguntas, comentários ou sugestões à medida que a deriva de dados se move em direção à disponibilidade geral. Use o botão de feedback do produto abaixo! 
