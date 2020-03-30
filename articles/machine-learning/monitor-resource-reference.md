---
title: Monitoramento de referência de dados | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Conheça os dados e recursos coletados para o Azure Machine Learning e disponível no Azure Monitor. O Azure Monitor coleta e fornece dados sobre o espaço de trabalho do Azure Machine Learning e permite visualizar métricas, definir alertas e analisar dados registrados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927560"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referência de dados de monitoramento de aprendizado de máquina do Azure

Conheça os dados e recursos coletados pelo Azure Monitor a partir do seu espaço de trabalho azure Machine Learning. Consulte [Monitorando o Azure Machine Learning](monitor-azure-machine-learning.md) para obter detalhes sobre a coleta e análise de dados de monitoramento.

## <a name="resource-logs"></a>Logs de recursos

A tabela a seguir lista as propriedades dos registros de recursos do Azure Machine Learning quando eles são coletados no Azure Monitor Logs ou no Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Propriedade | Descrição |
|:--- |:---|
| TimeGenerated | Tempo em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| JobId | ID do Trabalho submetido |
| ExperimentId | ID do Experimento |
| ExperimentName | Nome do Experimento |
| ID de assinatura do cliente | SubscriptionId onde Experiment e Job como submetido |
| Workspacename | Nome do espaço de trabalho de aprendizagem de máquina |
| ClusterName | Nome do Cluster |
| ProvisioningState | Estado da submissão do Trabalho |
| ResourceGroupName | Nome do grupo de recursos |
| JobName | Nome do Trabalho |
| ClusterId | ID do cluster |
| EventType | Tipo de evento job, por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceeded, etc. |
| ExecutionState | Estado do trabalho (a Corrida), por exemplo, Enfileirado, Correndo, Bem Sucedido, Falhou |
| ErrorDetails | Detalhes do erro do trabalho |
| CriaçãoApiVersion | Versão api usada para criar o trabalho |
| ClusterResourceGroupNome | Nome do grupo de recursos do cluster |
| Contagem de trabalhadores | Contagem de trabalhadores da TF |
| Contagem de servidores do TFParameter | Contagem do servidor de parâmetros TF |
| Tooltype | Tipo de ferramenta utilizada |
| Contêiner runin | Bandeira descrevendo se o trabalho deve ser executado dentro de um contêiner |
| JobErrorMessage | mensagem detalhada de erro de Trabalho |
| NodeId | ID do nó criado onde o trabalho está sendo executado |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ProvisioningState | Estado de provisionamento do cluster |
| ClusterName | Nome do cluster |
| ClusterType | Tipo do cluster |
| CreatedBy | Usuário que criou o cluster |
| CoreCount | Contagem dos núcleos no cluster |
| VmSize | Tamanho vm do cluster |
| VmPriority | Prioridade dos ádenos criados dentro de um cluster Dedicated/LowPriority |
| ScalingType | Tipo de manual/auto de dimensionamento de cluster |
| InitialNodeCount | Contagem inicial de nó do cluster |
| MínimoNodeCount | Contagem mínima de nó do cluster |
| MáximoNodeCount | Contagem máxima de nó do cluster |
| Opção de alocação nodeDe | Como o nó deve ser desalocado |
| Publicador | Editor do tipo cluster |
| Oferta | Oferta com a qual o cluster é criado |
| Sku | Sku do Nó/VM criado dentro do cluster |
| Versão | Versão da imagem usada enquanto O Nó/VM é criado |
| SubnetId | SubnetId do cluster |
| AllocationState | Estado de alocação de cluster |
| CurrentNodeCount | Contagem de nó atual do cluster |
| Contagem de targetnode | Contagem de nó de destino do cluster enquanto dimensiona para cima/para baixo |
| EventType | Tipo de evento durante a criação de clusters. |
| NodeIdleTimeSecondsbeforeScaleDown | Tempo ocioso em segundos antes do cluster ser reduzido |
| PreemptedNodeCount | Contagem de nó supérdia do cluster |
| IsResizeGrow | Bandeira indicando que o cluster está aumentando |
| VmFamilyName | Nome da família VM dos nódulos que podem ser criados dentro do cluster |
| DeixandoNocontagem | Deixando a contagem de nó do cluster |
| UnusableNodeCount | Contagem de nós inutilizáveis do cluster |
| IdleNodeCount | Contagem de nó ocioso do cluster |
| RunningNodeCount | Contagem de nó em execução do cluster |
| PreparandoNoConde | Preparando a contagem de nós do cluster |
| Cotas Alocadas | Cota alocada para o cluster |
| Cotas Utilizadas | Cota utilizada do cluster |
| AlocaçãoTempo de transição do estado | Tempo de transição de um estado para outro |
| ClusterErrorCodes | Código de erro recebido durante a criação ou dimensionamento do cluster |
| CriaçãoApiVersion | Versão api usada durante a criação do cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ClusterName | Nome do cluster |
| NodeId | ID do nó de cluster criado |
| VmSize | Tamanho VM do nó |
| VmFamilyName | Família Vm a qual o nó pertence |
| VmPriority | Prioridade do nó criado Dedicado/LowPriority |
| Publicador | Editor da imagem vm, por exemplo, microsoft-dsvm |
| Oferta | Oferta associada à criação da VM |
| Sku | Sku do Nó/VM criado |
| Versão | Versão da imagem usada enquanto O Nó/VM é criado |
| ClusterCreationTime | Tempo em que o cluster foi criado |
| RedimensioneStartTime | Tempo em que a escala de cluster para cima/para baixo começou |
| RedimensionarEndTime | Tempo em que a escala de cluster para cima/para baixo terminou |
| NodeAllocationTime | Tempo em que o Nó foi alocado |
| NodeBootTime | Tempo em que Node foi inicializado |
| StartTaskStartTime | Tempo em que a tarefa foi atribuída a um nó e começou |
| StartTaskEndTime | Tempo em que a tarefa atribuída a um nó terminou |
| Totale2etimeinseconds | O nó de tempo total estava ativo |

### <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma coletadas para o Azure Machine Learning Todas as métricas são armazenadas no namespace **Azure Machine Learning Workspace**.

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Falha na implantação do modelo | Contagem | O número de implantações de modelos que falharam. |
| Implantação do modelo iniciado | Contagem | O número de implantações de modelos começou. |
| Implantação de modelo sucedendo | Contagem | O número de implantações de modelos que foram bem sucedidos. |
| Falha no registro do modelo | Contagem | O número de registros de modelos que falharam. |
| Registro de modelo sucedendo | Contagem | O número de registros de modelos que tiveram sucesso. |

**Cota**

As informações de cotas são apenas para computação de Machine Learning do Azure.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | Contagem | O número de núcleos de computação ativos. |
| Nódulos ativos | Contagem | O número de nódulos ativos. |
| Núcleos ociosos | Contagem | O número de núcleos de computação ociosos. |
| Nódulos ociosos | Contagem | O número de nódulos de computação ociosos. |
| Deixando núcleos | Contagem | O número de núcleos de saída. |
| Deixando nódulos | Contagem | O número de nódulos que saem. |
| Núcleos pré-empáticos | Contagem | O número de núcleos pré-emptos. |
| Nódulos pré-empáticos | Contagem | O número de nódulos pré-emptos. |
| Percentual de utilização de cotas | Porcentagem | O percentual da cota utilizada. |
| Total de núcleos | Contagem | Os núcleos totais. |
| Total de nódulos | Contagem | Os nódulos totais. |
| Núcleos inutilizáveis | Contagem | O número de núcleos inutilizáveis. |
| Nós inutilizáveis | Contagem | O número de nós inutilizáveis. |

A seguir estão as dimensões que podem ser usadas para filtrar métricas de cotas:

| Dimensão | Métricas disponíveis com | Descrição |
| ---- | ---- | ---- |
| Nome do cluster | Todas as métricas de cotas | O nome da instância de computação. |
| Nome da família VM | Percentual de utilização de cotas | O nome da família VM usado pelo cluster. |
| Prioridade VM | Percentual de utilização de cotas | A prioridade da VM.

**Executar**

Informações sobre corridas de treinamento.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Corridas concluídas | Contagem | O número de corridas completas. |
| Executa com falha | Contagem | O número de corridas fracassadas. |
| Corridas iniciadas | Contagem | O número de corridas iniciadas. |

As seguintes dimensões que podem ser usadas para filtrar métricas de execução:

| Dimensão | Descrição |
| ---- | ---- |
| Type de computação | O tipo de computação que a execução usou. |
| PipelineStepType | O tipo de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) usado na corrida. |
| PublicadoPipelineId | O ID do oleoduto publicado usado na corrida. |
| Tipo de execução | O tipo de corrida. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimento | Não-oleoduto saem. |
| PipelineRun | Uma corrida de gasodutos, que é o pai de um StepRun. |
| Steprun | Uma corrida para um passo de oleoduto. |
| Passos reutilizados | Uma corrida para uma etapa de pipeline que reutiliza uma corrida anterior. |

## <a name="see-also"></a>Consulte também

- Consulte [Monitorando o Azure Machine Learning](monitor-azure-machine-learning.md) para obter uma descrição do monitoramento do Azure Machine Learning.
- Consulte [Monitorando os recursos do Azure com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) para obter detalhes sobre o monitoramento dos recursos do Azure.
