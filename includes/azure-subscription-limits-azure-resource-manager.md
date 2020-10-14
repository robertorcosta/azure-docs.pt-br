---
title: arquivo de inclusão
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334632"
---
| Recurso | Limite |
| --- | --- |
| Assinaturas por locatário do Azure Active Directory | Ilimitado. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |Ilimitado. |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por assinatura |980 |
| Tamanho da solicitação de API do Azure Resource Manager |4\.194.304 bytes. |
| Marcas por assinatura<sup>1</sup> |50 |
| Cálculos de marca exclusivos por assinatura<sup>1</sup> | 10.000 |
| [Implantações de nível de assinatura](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por local | 800<sup>2</sup> |

<sup>1</sup>Você pode aplicar até 50 marcas diretamente a uma assinatura. No entanto, a assinatura pode conter um número ilimitado de marcas aplicadas a recursos e grupos de recursos na assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Resource Manager retorna uma [lista de valores e nomes de marca exclusivos](/rest/api/resources/tags) na assinatura somente quando o número de marcas é menor ou igual a 10 mil. Você ainda poderá encontrar um recurso por marca quando o número exceder 10 mil.  

<sup>2</sup>Caso você atinja o limite de 800 implantações, exclua do histórico as implantações que não são mais necessárias. Para excluir implantações de nível de assinatura, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
