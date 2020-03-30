---
title: Consumir eventos de Machine Learning do Azure
titleSuffix: Azure Machine Learning
description: Neste artigo, saiba como usar o Azure Event Grid para se inscrever, reagir e cancelar a inscrição em eventos gerados pelo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139038"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Consumir eventos de Machine Learning do Azure (Preview)

O Azure Machine Learning gerencia todo o ciclo de vida do processo de aprendizagem de máquina, incluindo treinamento de modelos, implantação de modelos e monitoramento. Eventos de Machine Learning do Azure permitem que os aplicativos reajam a eventos durante o ciclo de vida do aprendizado de máquina, como a conclusão de corridas de treinamento, o registro e a implantação de modelos e a detecção de deriva de dados, usando o moderno sem servidor Arquiteturas. 

Esses eventos são publicados através [do Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Usando o portal Azure, Powershell ou Azure CLI, os clientes podem facilmente inscrever eventos [especificando um ou mais tipos de eventos e condições de filtragem](/azure/event-grid/event-filtering). Os clientes também têm a opção de construir uma ampla gama de manipuladores de eventos, como funções do Azure, aplicativos de lógica do Azure ou webhooks genéricos. O Azure Machine Learning, juntamente com o Azure Event Grid, fornece uma plataforma de entrega de eventos disponível, confiável e tolerante a falhas para você criar aplicativos orientados a eventos.

Para obter informações sobre como usar o Azure Machine Learning com a Event Grid, consulte [Criar fluxos de trabalho de aprendizagem de máquina orientados a eventos (Preview)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>O modelo do evento 

O Azure Event Grid lê eventos de fontes, como o Azure Machine Learning e outros serviços do Azure. Esses eventos são então enviados para manipuladores de eventos, como Azure Event Hubs, Azure Functions, Logic Apps e outros. O diagrama a seguir mostra como o Event Grid conecta fontes e manipuladores, mas não é uma lista abrangente de integrações suportadas.

![Modelo funcional da Grade de Eventos do Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [O que é a Grade de Eventos?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Tipos de eventos de Machine Learning do Azure

O Azure Machine Learning fornece eventos nos vários pontos do ciclo de vida do aprendizado de máquina: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Criado quando um experimento de aprendizagem de máquina é concluído |
| `Microsoft.MachineLearningServices.ModelRegistered` | Criado quando um modelo de aprendizagem de máquina é registrado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Levantada quando uma implantação de serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Levantado quando um trabalho de detecção de deriva de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Elevado quando um status de execução foi alterado, atualmente só é aumentado quando um status de execução é 'falhado' |

## <a name="subscribe-to-machine-learning-events"></a>Inscreva-se em eventos de Machine Learning

As assinaturas para eventos de Machine Learning do Azure são protegidas pelo RBAC (Role-Based Access Control). Somente [o contribuinte ou proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e excluir assinaturas de eventos.

As assinaturas do evento podem ser filtradas com base em uma variedade de condições. Filtros podem ser aplicados para assinaturas de evento ou durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da assinatura do evento ou [em um momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrar por tipo de evento
Uma assinatura de evento pode especificar um ou mais tipos de eventos de Machine Learning do Azure.

### <a name="filter-by-event-subject"></a>Filtro por assunto de evento
O Azure Event Grid suporta filtros de assunto baseados em __partidas__ e __termina com__ partidas, para que eventos com um assunto correspondente sejam entregues ao assinante. Diferentes eventos de aprendizagem de máquina têm formato de assunto diferente.

| Tipo de evento | Formato do assunto | Sujeito à amostra |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Filtragem avançada

O Azure Event Grid também suporta filtragem avançada com base em esquemade evento publicado. Detalhes do esquema do esquema de aprendizado de máquina do Azure podem ser encontrados no esquema de [eventos do Azure Event Grid para o Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Algumas amostras de filtragem avançada que você pode executar incluem:

* Para `Microsoft.MachineLearningServices.ModelRegistered` o evento, para filtrar o valor da tag do modelo:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Para saber mais sobre como aplicar filtros, consulte [Filter events for Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Consumir eventos de Machine Learning

Os aplicativos que lidam com eventos de Machine Learning devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma fonte específica, mas verificar o tópico da mensagem para garantir que ela venha do espaço de trabalho de aprendizado de máquina que você está esperando.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do sequenciador para entender a ordem de eventos em qualquer objeto específico.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * As operações de Aprendizado de Máquina do Azure com falha ou cancelamento não desencadearão um evento. Por exemplo, se uma implantação de modelo falhar, o Microsoft.MachineLearningServices.ModelDeployed não será acionado. Considere esse modo de falha ao projetar seus aplicativos. Você sempre pode usar o Azure Machine Learning SDK, CLI ou portal para verificar o status de uma operação e entender as razões detalhadas de falha.

O Azure Event Grid permite que os clientes construam manipuladores de mensagens desacoplados, que podem ser acionados por eventos de Machine Learning do Azure. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Aplicativos Lógicos do Azure
* Hubs de eventos do Azure
* Pipeline de fábrica de dados azure
* Webhooks genéricos, que podem ser hospedados na plataforma Azure ou em outros lugares

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Event Grid e dê uma chance aos eventos de Machine Learning do Azure:

- [Sobre a Grade de Eventos](../event-grid/overview.md)
- [Esquema de eventos do Azure Event Grid para aprendizado de máquina do Azure](../event-grid/event-schema-machine-learning.md)
- [Crie fluxos de trabalho orientados a eventos com o Azure Machine Learning](how-to-use-event-grid.md)
