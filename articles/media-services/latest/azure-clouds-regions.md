---
title: Nuvens e regiões nas quais os serviços de mídia do Azure v3 estão disponíveis
description: Este artigo discute as nuvens e regiões do Azure em que os Serviços de Mídia do Azure v3 estão disponíveis.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382963"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem

Os Serviços de Mídia do Azure v3 estão disponíveis por meio do manifesto do Azure Resource Manager no Azure global, no Azure Governamental, no Azure na Alemanha e no Azure China 21Vianet. No entanto, nem todos os recursos dos Serviços de Mídia estão disponíveis em todas as nuvens do Azure. Este documento descreve as disponibilidades dos principais componentes do Serviços de Mídia v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de recursos em nuvens do Azure

| Recurso|Regiões Globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [LiveEvents](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="regionsgeographieslocations"></a>Regiões/geografias/locais

[Regiões nas quais o serviço dos serviços de mídia do Azure está implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nome do código da região

Quando precisa fornecer o parâmetro de **localização**, você precisa fornecer o nome do código de região como o valor da **localização**. Para obter o nome do código da região na qual a conta está localizada e para a qual a chamada deverá ser encaminhada, execute a seguinte linha na [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue para a região do Azure que tenha o *displayName* que você está procurando e use seu valor *name* para o parâmetro **location**.

Por exemplo, para a região Oeste dos EUA 2 (exibida abaixo) do Azure, você usará "westus2" para o parâmetro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Pontos de extremidade  

É importante conhecer os pontos de extremidade a seguir ao conectar as contas de Serviços de Mídia em diferentes nuvens nacionais do Azure.

### <a name="global-azure"></a>Azure Global

|Pontos de extremidade||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autenticação | `https://login.microsoftonline.com/` |
| Público do token | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Pontos de extremidade||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autenticação | `https://login.microsoftonline.us/` |
| Público do token | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Alemanha

| Pontos de extremidade ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autenticação | `https://login.microsoftonline.de/` |
| Público do token | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Pontos de extremidade||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autenticação | `https://login.chinacloudapi.cn/` |
| Público do token |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Confira também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
