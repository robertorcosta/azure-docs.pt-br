---
title: Agende os pipelines de Machine Learning do Azure
titleSuffix: Azure Machine Learning
description: Agende os pipelines de aprendizado de máquina do Azure usando o Azure Machine Learning SDK para Python. Os pipelines programados permitem automatizar tarefas rotineiras e demoradas, como processamento de dados, treinamento e monitoramento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: fed411ea171274513308ec3efa68da80e4d25f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116750"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Agende pipelines de aprendizado de máquina com O Azure Machine Learning SDK para Python

Neste artigo, você aprenderá como programar programáticamente um pipeline para rodar no Azure. Você pode optar por criar um cronograma com base no tempo decorrido ou nas alterações do sistema de arquivos. Os horários baseados no tempo podem ser usados para cuidar de tarefas rotineiras, como o monitoramento de desvio de dados. Os horários baseados em alterações podem ser usados para reagir a alterações irregulares ou imprevisíveis, como o upload de novos dados ou a edição de dados antigos. Depois de aprender a criar horários, você aprenderá como recuperá-los e desativá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um ambiente Python no qual o Azure Machine Learning SDK for Python é instalado. Para obter mais informações, consulte [Criar e gerenciar ambientes reutilizáveis para treinamento e implantação com o Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho de Aprendizado de Máquina com um pipeline publicado. Você pode usar o que é construído em [Criar e executar pipelines de aprendizado de máquina com o Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicialize o espaço de trabalho & obtenha dados

Para agendar um pipeline, você precisará de uma referência ao seu espaço de trabalho, ao identificador do seu pipeline publicado e ao nome do experimento no qual deseja criar o cronograma. Você pode obter esses valores com o seguinte código:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Crie um cronograma

Para executar um pipeline em uma base recorrente, você criará um cronograma. Um `Schedule` associa um oleoduto, um experimento, e um gatilho. O gatilho pode`ScheduleRecurrence` ser um que descreve a espera entre corridas ou um caminho datastore que especifica um diretório para observar as alterações. Em ambos os casos, você precisará do identificador de pipeline e do nome do experimento para criar o cronograma.

Na parte superior do seu `Schedule` arquivo `ScheduleRecurrence` python, importe as classes e:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Crie um cronograma baseado no tempo

A `ScheduleRecurrence` construtora tem `frequency` um argumento obrigatório que deve ser uma das seguintes cordas: "Minuto", "Hora", "Dia", "Semana" ou "Mês". Também requer um `interval` argumento inteiro especificando quantas `frequency` unidades devem decorrer entre as partidas do cronograma. Os argumentos opcionais permitem que você seja mais específico sobre os horários de partida, conforme detalhado nos [documentos do ScheduleRecurrence SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Crie `Schedule` um que comece uma corrida a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Crie um cronograma baseado em alterações

Os pipelines acionados por alterações de arquivos podem ser mais eficientes do que os horários baseados no tempo. Por exemplo, você pode querer executar uma etapa de pré-processamento quando um arquivo é alterado ou quando um novo arquivo é adicionado a um diretório de dados. Você pode monitorar quaisquer alterações em um datastore ou alterações dentro de um diretório específico dentro do datastore. Se você monitorar um diretório específico, as alterações dentro dos subdiretórios desse diretório _não_ desencadearão uma execução.

Para criar um arquivo `Schedule`reativo, `datastore` você deve definir o parâmetro na chamada para [Agendar.criar](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorar uma pasta, defina o `path_on_datastore` argumento.

O `polling_interval` argumento permite especificar, em minutos, a frequência em que o datastore é verificado para alterações.

Se o pipeline foi construído com um Parâmetro [de Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) [DataPath,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) você pode definir `data_path_parameter_name` essa variável para o nome do arquivo alterado definindo o argumento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar um cronograma

Além dos argumentos discutidos anteriormente, você `status` pode `"Disabled"` definir o argumento para criar um cronograma inativo. Finalmente, `continue_on_step_failure` o permite que você passe um Booleano que irá substituir o comportamento padrão de falha do pipeline.

## <a name="view-your-scheduled-pipelines"></a>Veja seus pipelines programados

No seu navegador da Web, navegue até o Azure Machine Learning. Na seção **Endpoints** do painel de navegação, escolha **pontos finais do Pipeline**. Isso leva você a uma lista dos pipelines publicados no Workspace.

![Página de pipelines da AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Nesta página você pode ver informações resumidas sobre todos os pipelines no Espaço de Trabalho: nomes, descrições, status e assim por diante. Faça exercícios clicando no seu oleoduto. Na página resultante, há mais detalhes sobre o seu pipeline e você pode detalhar em corridas individuais.

## <a name="deactivate-the-pipeline"></a>Desative o oleoduto

Se você `Pipeline` tiver um que seja publicado, mas não agendado, você pode desabilitá-lo com:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se o oleoduto estiver agendado, você deve cancelar o horário primeiro. Recupere o identificador do cronograma do portal ou executando:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Uma vez `schedule_id` que você deseja desativar, execute:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se você `Schedule.list(ws)` correr de novo, você deve obter uma lista vazia.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou o Azure Machine Learning SDK para Python para agendar um pipeline de duas maneiras diferentes. Um cronograma se repete com base no tempo decorrido do relógio. O outro cronograma é executado se um `Datastore` arquivo for modificado em um diretório especificado ou dentro de um diretório nessa loja. Você viu como usar o portal para examinar o oleoduto e corridas individuais. Finalmente, você aprendeu como desativar um cronograma para que o pipeline pare de funcionar.

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Usar o Azure Machine Learning Pipelines para pontuação do lote](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [pipelines](concept-ml-pipelines.md)
* Saiba mais sobre [como explorar o Azure Machine Learning com a Jupyter](samples-notebooks.md)

