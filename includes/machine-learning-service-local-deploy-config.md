---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477265"
---
As entradas `deploymentconfig.json` no mapa do documento para os parâmetros [de LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para alvos locais, o `local`valor deve ser . |
| `port` | `port` | A porta local para expor o ponto final HTTP do serviço. |

Este JSON é uma configuração de implantação de exemplo para uso com o CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
