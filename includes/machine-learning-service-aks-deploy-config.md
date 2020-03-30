---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485934"
---
As entradas `deploymentconfig.json` no mapa do documento para os parâmetros [de AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para aKS, o `aks`valor deve ser . |
| `autoScaler` | NA | Contém elementos de configuração para escala automática. Veja a tabela do autoscaler. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se para habilitar o autoscaling para o serviço web. `numReplicas`  =  `0`Se, `True`; caso contrário, `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de recipientes para usar ao fazer a auto-dimensionamento deste serviço web. Padrão, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de recipientes a serem usados ao fazer a auto-dimensionamento deste serviço web. Padrão, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Quantas vezes o autoscaler tenta dimensionar esse serviço web. Padrão, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A utilização-alvo (em percentual de 100) que o autoescalador deve tentar manter para este serviço web. Padrão, `70`. |
| `dataCollection` | NA | Contém elementos de configuração para coleta de dados. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se para habilitar a coleta de dados de modelo para o serviço web. Padrão, `False`. |
| `authEnabled` | `auth_enabled` | Ativar ou não a autenticação-chave para o serviço web. Ambos `tokenAuthEnabled` `authEnabled` e `True`não pode ser . Padrão, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Ativar ou não a autenticação de token para o serviço web. Ambos `tokenAuthEnabled` `authEnabled` e `True`não pode ser . Padrão, `False`. |
| `containerResourceRequirements` | NA | Recipiente para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados para este serviço web. Padrões`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) para alocar para este serviço web. Padrão`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Se para habilitar o registro de Insights de aplicativos para o serviço web. Padrão, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo para aplicar para marcar chamadas para o serviço web. Padrão, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | As solicitações simultâneas máximas por nó para este serviço web. Padrão, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo que uma solicitação permanecerá na fila (em milissegundos) antes que um erro de 503 seja devolvido. Padrão, `500`. |
| `numReplicas` | `num_replicas` | O número de contêineres a serem alocados para este serviço web. Sem valor padrão. Se este parâmetro não estiver definido, o autoscaler será ativado por padrão. |
| `keys` | NA | Contém elementos de configuração para chaves. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave auth primária para usar para este Webservice |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave auth secundária para usar neste Webservice |
| `gpuCores` | `gpu_cores` | O número de núcleos de GPU (réplica por contêiner) a serem alocados para este Webservice. O padrão é UTF-1. Só suporta valores numéres inteiros. |
| `livenessProbeRequirements` | NA | Contém elementos de configuração para os requisitos do teste de vida. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Quantas vezes (em segundos) para executar a sonda de vivacidade. Padrão para 10 segundos. O valor mínimo é 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos após o início do recipiente antes do início das sondas de vida. Padrão para 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após o qual a sonda de vida se esespera. Padrão para 2 segundos. O valor mínimo é 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Sucessos consecutivos mínimos para que a sonda de vida seja considerada bem sucedida depois de ter falhado. O valor padrão é 1. O valor mínimo é 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando um Pod é iniciado e o teste de vida falha, o Kubernetes tentará falharTempos limiares antes de desistir. Padrão para 3. O valor mínimo é 1. |
| `namespace` | `namespace` | O namespace do Kubernetes no que o webservice é implantado. Até 63 caracteres alfanuméricas minúsculas ('a'-'z', '0'-'9') e hífens ('-') caracteres. O primeiro e o último caracteres não podem ser hífens. |

O JSON a seguir é uma configuração de implantação de exemplo para uso com o CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
