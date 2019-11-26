---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a01455da36e8ab573773fec2a6da1f7903b8ea20
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224384"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| VMs por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |25.000 por região. |
| Total de núcleos da VM por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Entre em contato com o suporte. |
| VM por série, como Dv2 e F, núcleos por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Entre em contato com o suporte. |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por assinatura |Ilimitado. |Ilimitado. |
| [Contas de armazenamento](../articles/storage/common/storage-quickstart-create-account.md) por região e assinatura |250 |250 |
| [Grupos de recursos](../articles/azure-resource-manager/resource-group-overview.md) por assinatura |980 |980 |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2\.000 por região. |2\.000 por região. |
| Tamanho da solicitação da API Azure Resource Manager |4\.194.304 bytes. |4\.194.304 bytes. |
| Marcas por assinatura<sup>2</sup> |Ilimitado. |Ilimitado. |
| Cálculos de marca exclusivos por assinatura<sup>2</sup> | 10.000 | 10.000 |
| [Serviços de nuvem](../articles/cloud-services/cloud-services-choose-me.md) por assinatura |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por assinatura |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Implantações em nível de assinatura](../articles/azure-resource-manager/deploy-to-subscription.md) por local | 800<sup>4</sup> | 800 |

<sup>1</sup> Os limites padrão variam de acordo com o tipo de categoria de oferta, como avaliação gratuita e pago conforme o uso, e por série, como Dv2, F e G. Por exemplo, o padrão para assinaturas de Enterprise Agreement é 350.

<sup>2</sup> Você pode aplicar um número ilimitado de marcas por assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Resource Manager retorna uma [lista de nomes de marca exclusivos e valores](/rest/api/resources/tags) na assinatura somente quando o número de marcas é de 10.000 ou menos. Você ainda pode encontrar um recurso por marca quando o número excede 10.000.  

<sup>3</sup> Esses recursos não são mais necessários com os grupos de recursos do Azure e o Resource Manager.

<sup>4</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações de nível de assinatura, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Os núcleos de máquina virtual têm um limite de total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Esses limites são aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Essa assinatura pode implantar 30 VMs a1 ou 30 VMs D1 ou uma combinação dos dois para não exceder um total de 30 núcleos. Um exemplo de uma combinação é de 10 VMs a1 e 20 VMs D1.  
> <!-- -->
> 
> 

