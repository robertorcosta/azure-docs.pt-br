---
title: incluir arquivo
description: incluir arquivo
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335118"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gestão por diretório | 10.000 |
| Assinaturas por grupo de gestão | Ilimitado. |
| Níveis de hierarquia de grupos de gestão | Nível raiz mais 6 níveis<sup>1</sup> |
| Grupo de gestão direta de pais por grupo de gestão | Um |
| [Implantações de nível de grupo de gerenciamento](../articles/azure-resource-manager/templates/deploy-to-management-group.md) por local | 800<sup>2</sup> |

<sup>1</sup> Os 6 níveis não incluem o nível de assinatura.

<sup>2</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações de nível de grupo de gerenciamento, use [remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
