---
title: Exportar ou excluir dados do workspace
titleSuffix: Azure Machine Learning
description: Saiba como exportar ou excluir seu espaço de trabalho com o Azure Machine Learning Studio, CLI, SDK e APIs REST autenticadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.custom: seodec18
ms.openlocfilehash: 50234eb356314039b8023f0442207ae0b762676e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191627"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou excluir dados do workspace no serviço do Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

No Azure Machine Learning, você pode exportar ou excluir seus dados de workspace com a API REST autenticada. Este artigo informa como fazer isso.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controlar seus dados de workspace

Os dados no produto armazenados por Azure Machine Learning estão disponíveis para exportação e exclusão por meio do Azure Machine Learning Studio, CLI, SDK e APIs REST autenticadas. É possível acessar os dados telemétricos por meio do Portal de Privacidade do Azure. 

No Azure Machine Learning, os dados pessoais são formados por informações do usuário em documentos de histórico de execução e registros telemétricos de algumas interações do usuário com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Excluir dados de workspace com a API REST

Para excluir os dados, as chamadas da API a seguir podem ser feitas com o verbo HTTP DELETE. Eles são autorizados devido ao cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`.  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, confira [usar REST para gerenciar recursos do ml](how-to-manage-rest.md) e a [documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Excluir um workspace inteiro

Use essa chamada para excluir um workspace inteiro.  
> [!WARNING]
> Todas as informações serão excluídas e o workspace não será mais utilizável.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Excluir modelos

Use essa chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

É possível excluir modelos individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Excluir ativos

Use essa chamada para obter uma lista dos ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Os ativos individuais podem ser excluídos com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Excluir imagens

Use essa chamada para obter uma lista das imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

É possível excluir imagens individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Excluir serviços

Use essa chamada para obter uma lista das serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

É possível excluir serviços individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Exportar dados de serviço com a API REST

Para exportar os dados, as chamadas da API a seguir podem ser feitas com o verbo HTTP GET. Eles são autorizados devido ao cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, confira [usar REST para gerenciar recursos de ml](how-to-manage-rest.md) e [documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).   

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Exportar informações do Workspace

Use essa chamada para obter uma lista de todos os workspaces:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informações sobre um workspace individual podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Exportar informações de computação

Todos os destinos de computação anexados a um workspace podem ser obtidos por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Informações sobre um único destino de computação podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Exportar dados do histórico de execução

Use essa chamada para obter uma lista de todos os experimentos e suas informações:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Todas as execuções de um experimento específico podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

É possível obter os itens do histórico de execução com:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Todas as métricas de execução de um experimento podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Uma única métrica de execução pode ser obtida por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Exportar artefatos

Use essa chamada para obter uma lista dos artefatos e seus caminhos:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Exportar notificações

Use essa chamada para obter uma lista de tarefas armazenadas:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Notificações para uma única tarefa podem ser obtidas por:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportar armazenamentos de dados

Use essa chamada para obter uma lista de armazenamentos de dados:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Armazenamentos de dados individuais podem ser obtidos por:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportar modelos

Use essa chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

É possível obter modelos individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportar ativos

Use essa chamada para obter uma lista dos ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Ativos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Exportar imagens

Use essa chamada para obter uma lista das imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportar serviços

Use essa chamada para obter uma lista das serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

É possível obter serviços individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Exportar Experimentos de Pipeline

Experimentos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportar Gráficos de Pipeline

Gráficos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportar Módulos de Pipeline

Os módulos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportar Modelos de Pipeline

Modelos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportar Fontes de Dados de Pipeline

Fontes de Dados podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Excluir ativos no designer

No designer em que você criou seu experimento, exclua ativos individuais:

1. Ir para o designer

    ![Excluir ativos](./media/how-to-export-delete-data/delete-experiment.png)

1. Na lista, selecione o rascunho de pipeline individual a ser excluído.

1. Selecione **Excluir**.

### <a name="delete-datasets-in-the-designer"></a>Excluir conjuntos de itens no designer

Para excluir conjuntos de valores no designer, use o portal do Azure ou Gerenciador de Armazenamento para navegar até contas de armazenamento conectadas e excluir conjuntos de os mesmos. O cancelamento do registro de conjuntos de valores no designer remove apenas o ponto de referência no armazenamento.

