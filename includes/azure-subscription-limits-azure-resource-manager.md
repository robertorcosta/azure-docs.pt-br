---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334632"
---
| Recurso | Limite |
| --- | --- |
| Assinaturas por inquilino do Azure Active Directory | Ilimitado. |
| [Co-administradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |Ilimitado. |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por assinatura |980 |
| Azure Resource Manager API solicitar tamanho |4.194.304 bytes. |
| Tags por assinatura<sup>1</sup> |50 |
| Cálculos exclusivos de tag por assinatura<sup>1</sup> | 10.000 |
| [Implantações em nível de assinatura](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por local | 800<sup>2</sup> |

<sup>1</sup> Você pode aplicar até 50 tags diretamente em uma assinatura. No entanto, a assinatura pode conter um número ilimitado de tags que são aplicadas a grupos de recursos e recursos dentro da assinatura. O número de tags por grupo de recursos ou recursos é limitado a 50. O Gerenciador de recursos retorna uma [lista de nomes e valores exclusivos](/rest/api/resources/tags) da tag na assinatura somente quando o número de tags for de 10.000 ou menos. Você ainda pode encontrar um recurso por tag quando o número exceder 10.000.  

<sup>2</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações de nível de assinatura, use [remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [sub-exclusão de implantação az](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
