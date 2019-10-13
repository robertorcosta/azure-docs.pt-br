---
title: Mover as máquinas virtuais do Azure para uma nova assinatura ou grupo de recursos | Microsoft Docs
description: Use Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: 443d6f2bcbb61d9106b079a4e63c48bb433d19c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286727"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover diretrizes para máquinas virtuais

Este artigo descreve os cenários que atualmente não têm suporte e as etapas para mover as máquinas virtuais com o backup.

## <a name="scenarios-not-supported"></a>Cenários sem suporte

Ainda não há suporte para os cenários a seguir:

* Managed Disks no Zonas de Disponibilidade não podem ser movidos para uma assinatura diferente.
* Conjuntos de dimensionamento de máquinas virtuais com Load Balancer SKU padrão ou IP público SKU Standard não podem ser movidos.
* As máquinas virtuais criadas a partir dos recursos do Marketplace com os planos anexados não podem ser movidas entre grupos de recursos ou assinaturas. Desprovisionar a máquina virtual na assinatura atual e implantá-la novamente na nova assinatura.
* As máquinas virtuais em uma rede virtual existente não podem ser movidas para uma nova assinatura quando você não está movendo todos os recursos na rede virtual.
* As máquinas virtuais de baixa prioridade e os conjuntos de dimensionamento de máquinas virtuais de baixa prioridade não podem ser movidos entre grupos de recursos ou assinaturas.
* As máquinas virtuais em um conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o backup do Azure

Para mover máquinas virtuais configuradas com o Backup do Azure, use a seguinte solução alternativa:

* Localize o local de sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se estiver no portal do Azure, marque "Mostrar tipos ocultos"
* Se estiver no PowerShell, use o cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Se estiver na CLI, use o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
* Após a conclusão da exclusão, você pode mover o cofre e a máquina virtual para a assinatura de destino. Após a movimentação, você poderá continuar backups sem perda de dados.
* Para saber mais sobre como mover cofres do Serviço de Recuperação para o backup, veja [Limitações dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
