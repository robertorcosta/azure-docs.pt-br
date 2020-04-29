---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334890"
---
| Recurso | Limite |
| --- | --- |
| [Funções Web ou de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Pontos de extremidade de entrada de instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |
| [Pontos de extremidade de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |
| [Pontos de extremidade internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |

<sup>1</sup> Cada serviço de nuvem do Azure com funções Web ou de trabalho pode ter duas implantações, uma para produção e outra para preparo. Esse limite se refere ao número de funções distintas, ou seja, configuração. Esse limite não se refere ao número de instâncias por função, ou seja, dimensionamento.

