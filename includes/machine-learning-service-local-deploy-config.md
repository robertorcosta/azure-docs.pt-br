---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477265"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para destinos locais, o valor deve ser `local` . |
| `port` | `port` | A porta local na qual expor o ponto de extremidade HTTP do serviço. |

Este JSON é um exemplo de configuração de implantação para uso com a CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
