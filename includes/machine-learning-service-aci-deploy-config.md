---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486026"
---
As entradas `deploymentconfig.json` no mapa do documento para os parâmetros [de AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para a ACI, `ACI`o valor deve ser . |
| `containerResourceRequirements` | NA | Recipiente para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU para alocar. Padrões`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) para alocar para este serviço web. Padrão`0.5` |
| `location` | `location` | A região do Azure para implantar este Webservice. Se não especificado, o local do Espaço de Trabalho será usado. Mais detalhes sobre as regiões disponíveis podem ser encontrados aqui: [Regiões ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se para habilitar auth para este Webservice. Padrão para Falso |
| `sslEnabled` | `ssl_enabled` | Se para habilitar o SSL para este Webservice. Usa False como padrão. |
| `appInsightsEnabled` | `enable_app_insights` | Se deve ativar o AppInsights para este Webservice. Padrão para Falso |
| `sslCertificate` | `ssl_cert_pem_file` | O arquivo cert necessário se o SSL estiver habilitado |
| `sslKey` | `ssl_key_pem_file` | O arquivo-chave necessário se o SSL estiver ativado |
| `cname` | `ssl_cname` | O nome cname para se SSL está habilitado |
| `dnsNameLabel` | `dns_name_label` | O rótulo de nome dns para o ponto final de pontuação. Se não especificado, um rótulo de nome dns exclusivo será gerado para o ponto final de pontuação. |

O JSON a seguir é uma configuração de implantação de exemplo para uso com o CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```