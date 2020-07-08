---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391461"
---
| Recurso |[Plano de Consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|[Duração do tempo limite](../articles/azure-functions/functions-scale.md#timeout) padrão (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|[Duração do tempo limite](../articles/azure-functions/functions-scale.md#timeout) máximo (min) |10 | Não associado<sup>7</sup> | Não associado<sup>2</sup> | não associado | não associado |
| Máximo de conexões de saída (por instância) | 600 ativo (total de 1200) | não associado | não associado | não associado | não associado |
| Tamanho máximo da solicitação (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Depende do cluster |
| Tamanho máximo da cadeia de caracteres de consulta<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Depende do cluster |
| Comprimento máximo da URL de solicitação<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Depende do cluster |
|[ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Preços do AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Memória máxima (GB por instância) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Há suporte para qualquer nó |
| Aplicativos de funções por plano |100 |100 |Não associado<sup>4</sup> | não associado | não associado |
| [Planos do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos | - | - |
| Armazenamento<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | N/D |
| Domínios personalizados por aplicativo</a> |500<sup>6</sup> |500 |500 | 500 | N/D |
| domínio personalizado [Suporte a SSL](../articles/app-service/configure-ssl-bindings.md) |conexão SSL SNI não vinculada incluída | conexões SSL SNI não associadas e 1 IP SSL incluídas |conexões SSL SNI não associadas e 1 IP SSL incluídas | conexões SSL SNI não associadas e 1 IP SSL incluídas | N/D |

<sup>1</sup> por padrão, o tempo limite para o tempo de execução do Functions 1. x em um plano do serviço de aplicativo é não associado.  
<sup>2</sup> requer que o plano do serviço de aplicativo seja definido como [Always on](../articles/azure-functions/functions-scale.md#always-on). Pague com [tarifas](https://azure.microsoft.com/pricing/details/app-service/)padrão.  
<sup>3</sup> esses limites são [definidos no host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> o número real de aplicativos de funções que você pode hospedar depende da atividade dos aplicativos, do tamanho das instâncias de máquina e da utilização de recursos correspondente.  
<sup>5</sup> o limite de armazenamento é o tamanho total do conteúdo no armazenamento temporário em todos os aplicativos no mesmo plano do serviço de aplicativo. O plano de consumo usa os arquivos do Azure para armazenamento temporário.  
<sup>6</sup> quando seu aplicativo de funções estiver hospedado em um [plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan), somente a opção CNAME terá suporte. Para aplicativos de funções em um [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou um [plano do serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan), é possível mapear um domínio personalizado usando um registro CNAME ou um.  
<sup>7</sup> garantia de até 60 minutos.  
<sup>8</sup> trabalhadores são funções que hospedam aplicativos de clientes. Os trabalhadores estão disponíveis em três tamanhos fixos: um vCPU/3,5 GB de RAM; Duas vCPU/7 GB de RAM; Quatro vCPU/14 GB de RAM.
