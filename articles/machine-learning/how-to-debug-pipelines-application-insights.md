---
title: Depurar e solucionar problemas de pipelines de aprendizado de máquina em Insights de Aplicativos
titleSuffix: Azure Machine Learning
description: Adicione o registro aos seus pipelines de treinamento e pontuação em lote e visualize os resultados registrados no Application Insights.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: b3e4bf19a7ec153f85483f3c5028e468e06ed7f0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982354"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Depurar e solucionar problemas de pipelines de aprendizado de máquina em Insights de Aplicativos
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A biblioteca python [OpenCensus](https://opencensus.io/quickstart/python/) pode ser usada para direcionar logs para Insights de aplicativos de seus scripts. Agregar logs de pipeline em um só lugar permite que você construa consultas e diagnostice problemas. O uso do Application Insights permitirá rastrear logs ao longo do tempo e comparar registros de pipeline em corridas.

Ter seus logs em um lugar uma vez fornecerá um histórico de exceções e mensagens de erro. Como o Application Insights se integra aos Alertas do Azure, você também pode criar alertas com base em consultas do Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as etapas para criar um espaço de trabalho [de Aprendizado de Máquina do Azure](./how-to-manage-workspace.md) e crie seu primeiro [pipeline](./how-to-create-your-first-pipeline.md)
* [Configure seu ambiente de desenvolvimento](./how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning.
* Instale localmente o pacote [OpenCensus Azure Monitor Exporter:](https://pypi.org/project/opencensus-ext-azure/)
  ```python
  pip install opencensus-ext-azure
  ```
* Crie uma [instância do Application Insights](../azure-monitor/app/opencensus-python.md) (este doc também contém informações sobre como obter a seqüência de conexões para o recurso)

## <a name="getting-started"></a>Introdução

Esta seção é uma introdução específica para usar o OpenCensus a partir de um pipeline de Machine Learning do Azure. Para obter um tutorial detalhado, consulte [Exportadores do OpenCensus Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adicione um PythonScriptStep ao seu Pipeline Azure ML. Configure sua [Configuração de Execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) com a dependência do opencensus-ext-azure. Configure `APPLICATIONINSIGHTS_CONNECTION_STRING` a variável de ambiente.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Crie um arquivo chamado `sample_step.py`. Importe a classe AzureLogHandler para fazer logs de rota para Insights de aplicativos. Você também precisará importar a biblioteca Python Logging.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Em seguida, adicione o AzureLogHandler ao logger python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Registro com dimensões personalizadas
 
Por padrão, os logs encaminhados ao Application Insights não terão contexto suficiente para rastrear até a execução ou experimento. Para tornar os registros acionáveis para o diagnóstico de problemas, são necessários campos adicionais. 

Para adicionar esses campos, dimensões personalizadas podem ser adicionadas para fornecer contexto a uma mensagem de log. Um exemplo é quando alguém quer exibir logs em vários passos na mesma execução do pipeline.

As Dimensões Personalizadas compõem um dicionário de pares de valor-chave (armazenados como string, string). O dicionário é então enviado para o Application Insights e exibido como uma coluna nos resultados da consulta. Suas dimensões individuais podem ser usadas como [parâmetros de consulta.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Contexto útil para incluir

| Campo                          | Raciocínio/Exemplo                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Pode consultar logs para aqueles com o mesmo parent_run_id para ver logs ao longo do tempo para todas as etapas, em vez de ter que mergulhar em cada etapa individual                                        |
| step_id                        | Pode consultar logs para aqueles com o mesmo step_id para ver onde um problema ocorreu com um escopo estreito apenas para a etapa individual                                                        |
| step_name                      | Pode consultar logs para ver o desempenho do passo ao longo do tempo. Também ajuda a encontrar um step_id para corridas recentes sem mergulhar no portal UI                                          |
| experiment_name                | Pode consultar através de logs para ver o desempenho do experimento ao longo do tempo. Também ajuda a encontrar um parent_run_id ou step_id para corridas recentes sem mergulhar no portal UI                   |
| run_url                 | Pode fornecer um link diretamente de volta para a corrida para investigação. |

**Outros campos úteis**

Esses campos podem exigir instrumentação de código adicional e não são fornecidos pelo contexto de execução.

| Campo                   | Raciocínio/Exemplo                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se usar CI/CD para implantar, este campo pode correlacionar logs à versão de código que forneceu a lógica de passo e pipeline. Este link pode ajudar ainda a diagnosticar problemas ou identificar modelos com características específicas (valores de log/métrico) |
| run_type                       | Pode diferenciar entre diferentes tipos de modelo, ou treinamento vs. corridas de pontuação |

### <a name="creating-a-custom-dimensions-dictionary"></a>Criando um dicionário de dimensões personalizadas

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Considerações de registro do OpenCensus Python

O OpenCensus AzureLogHandler é usado para direcionar os logs python para o Application Insights. Como resultado, as nuances de registro python devem ser consideradas. Quando um logger é criado, ele tem um nível de registro padrão e mostrará registros maiores ou iguais a esse nível. Uma boa referência para o uso de recursos de registro Python é o [Livro de Receitas de Registro](https://docs.python.org/3/howto/logging-cookbook.html).

A `APPLICATIONINSIGHTS_CONNECTION_STRING` variável ambiente é necessária para a biblioteca OpenCensus. Recomendamos definir essa variável de ambiente em vez de passá-la como parâmetro de pipeline para evitar passar em torno de strings de conexão de texto simples.

## <a name="querying-logs-in-application-insights"></a>Consultando logs no Application Insights

Os registros encaminhados para o Application Insights serão exibidos em 'traces' ou 'exceções'. Certifique-se de ajustar sua janela de tempo para incluir a execução do pipeline.

![Resultado da consulta de insights do aplicativo](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

O resultado no Application Insights mostrará a mensagem de log e o nível, o caminho do arquivo e o número da linha de código. Ele também mostrará quaisquer dimensões personalizadas incluídas. Nesta imagem, o dicionário customDimensions mostra os pares de teclas/valor da amostra de [código](#creating-a-custom-dimensions-dictionary)anterior .

### <a name="additional-helpful-queries"></a>Consultas úteis adicionais

Algumas das consultas abaixo usam 'customDimensions.Level'. Esses níveis de gravidade correspondem ao nível com o que o registro Python foi originalmente enviado. Para obter informações adicionais sobre consulta, consulte [Consultas de log do Monitor do Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Caso de uso                                                               | Consulta                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Registrar resultados para dimensões personalizadas específicas, por exemplo 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Os resultados de todos os treinamentos serão executados nos últimos 7 dias                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Resultados de log com gravidadeErro de nível dos últimos 7 dias              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Contagem de resultados de log com gravidadeErro de nível nos últimos 7 dias     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Próximas etapas

Uma vez que você tenha logs na instância do Application Insights, eles podem ser usados para definir [os alertas do Azure Monitor](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) com base nos resultados da consulta.

Você também pode adicionar resultados de consultas a um [Painel Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) para obter informações adicionais.
