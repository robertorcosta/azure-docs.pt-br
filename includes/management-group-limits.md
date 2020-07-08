---
title: arquivo de inclusão
description: incluir arquivo
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335118"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gerenciamento por diretório | 10.000 |
| Assinaturas por grupo de gerenciamento | Ilimitado. |
| Níveis da hierarquia do grupo de gerenciamento | Nível raiz mais 6 níveis<sup>1</sup> |
| Grupo de gerenciamento pai direto por grupo de gerenciamento | Um |
| [Implantações no nível do grupo de gerenciamento](../articles/azure-resource-manager/templates/deploy-to-management-group.md) por local | 800<sup>2</sup> |

<sup>1</sup> Os 6 níveis não incluem o nível de assinatura.

<sup>2</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações no nível do grupo de gerenciamento, use [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
