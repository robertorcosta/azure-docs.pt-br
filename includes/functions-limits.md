---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198336"
---
| Recurso | [Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plano premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plano de Serviço de Aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Expansão | Controlada por eventos | Controlada por eventos | [Escala manual/automática](../articles/app-service/manage-scale-up.md) | 
| Max instâncias | 200 | 100 | 10-20 |
|Duração [do tempo de tempo padrão](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|[Duração máxima do tempo (min)](../articles/azure-functions/functions-scale.md#timeout) |10 | <sup>8</sup> ilimitada | <sup>ilimitado 3</sup> |
| Conexões de saída máxima (por exemplo) | 600 ativos (1200 no total) | não associado | não associado |
| Tamanho máximo da solicitação (MB)<sup>4</sup> | 100 | 100 | 100 |
| Comprimento da seqüência de seqüência de consulta<sup>máxima 4</sup> | 4096 | 4096 | 4096 |
| Comprimento da URL de solicitação máxima<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 |
| Memória máxima (GB por instância) | 1.5 | 3.5-14 | 1.75-14 |
| Aplicativos de função por plano |100 |100 |<sup>5</sup> sem limites |
| [Planos de serviço de aplicativos](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos |
| Armazenamento<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domínios personalizados por aplicativo</a> |500<sup>7</sup> |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/configure-ssl-bindings.md) |conexão SNI SSL sem limites incluída | Conexões SNI SSL sni e 1 IP SSL incluídas |Conexões SNI SSL sni e 1 IP SSL incluídas | 

<sup>1</sup> Para obter limites específicos para as várias opções de plano de serviço de aplicativo, consulte os limites do [plano app service](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Por padrão, o tempo limite para o tempo de execução das funções 1.x em um plano de serviço de aplicativo é ilimitado.  
<sup>3</sup> Requer que o plano de serviço do aplicativo seja definido como [Always On](../articles/azure-functions/functions-scale.md#always-on). Pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/)padrão.  
<sup>4</sup> Estes limites são [definidos no host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> O número real de aplicativos de função que você pode hospedar depende da atividade dos aplicativos, do tamanho das instâncias da máquina e da utilização correspondente dos recursos.  
<sup>6</sup> O limite de armazenamento é o tamanho total do conteúdo em armazenamento temporário em todos os aplicativos do mesmo plano de Serviço de Aplicativo. O plano de consumo usa arquivos Azure para armazenamento temporário.  
<sup>7</sup> Quando seu aplicativo de função está hospedado em um [plano de consumo,](../articles/azure-functions/functions-scale.md#consumption-plan)apenas a opção CNAME é suportada. Para aplicativos de função em um [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou um plano de serviço [de aplicativo,](../articles/azure-functions/functions-scale.md#app-service-plan)você pode mapear um domínio personalizado usando um CNAME ou um registro A.  
<sup>8</sup> Garantido por até 60 minutos.