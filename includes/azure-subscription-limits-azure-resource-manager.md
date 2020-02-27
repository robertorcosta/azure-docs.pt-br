---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618470"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Assinaturas por locatário de Azure Active Directory | Ilimitado. | Ilimitado. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |Ilimitado. |Ilimitado. |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por assinatura |980 |980 |
| Tamanho da solicitação da API Azure Resource Manager |4\.194.304 bytes. |4\.194.304 bytes. |
| Marcas por assinatura<sup>1</sup> |Ilimitado. |Ilimitado. |
| Cálculos de marca exclusivos por assinatura<sup>1</sup> | 10.000 | 10.000 |
| [Implantações em nível de assinatura](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por local | 800<sup>2</sup> | 800 |

<sup>1</sup> Você pode aplicar um número ilimitado de marcas por assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Resource Manager retorna uma [lista de nomes de marca exclusivos e valores](/rest/api/resources/tags) na assinatura somente quando o número de marcas é de 10.000 ou menos. Você ainda pode encontrar um recurso por marca quando o número excede 10.000.  

<sup>2</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações de nível de assinatura, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
