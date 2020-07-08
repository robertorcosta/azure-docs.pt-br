---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838854"
---
| Recurso | Limite |
| --- | --- |
| [Funções Web ou de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Pontos de extremidade de entrada de instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |
| [Pontos de extremidade de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |
| [Pontos de extremidade internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |

<sup>1</sup> Cada serviço de nuvem do Azure com funções Web ou de trabalho pode ter duas implantações, uma para produção e outra para preparo. Esse limite se refere ao número de funções distintas, ou seja, configuração. Esse limite não se refere ao número de instâncias por função, ou seja, dimensionamento.

