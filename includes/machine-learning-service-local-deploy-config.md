---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557601"
---
As entradas no documento `deploymentconfig.json` são mapeadas para os parâmetros de [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A seguinte tabela descreve o mapeamento entre as entidades no documento JSON e os parâmetros do método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para destinos locais, o valor precisa ser `local`. |
| `port` | `port` | A porta local na qual expor o ponto de extremidade HTTP do serviço. |

O seguinte JSON é um exemplo de configuração de implantação a ser usada com a CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```