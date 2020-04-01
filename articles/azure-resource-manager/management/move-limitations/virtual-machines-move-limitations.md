---
title: Mova as VMs do Azure para um novo grupo de assinatura ou recursos
description: Use o Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478656"
---
# <a name="move-guidance-for-virtual-machines"></a>Mova orientação para máquinas virtuais

Este artigo descreve os cenários que não são suportados no momento e as etapas para mover máquinas virtuais com backup.

## <a name="scenarios-not-supported"></a>Cenários não suportados

Ainda não há suporte para os cenários a seguir:

* Discos gerenciados em Zonas de Disponibilidade não podem ser movidos para uma assinatura diferente.
* Os conjuntos de escala de máquina virtual com balanceador de carga SKU padrão ou IP público Padrão SKU não podem ser movidos.
* As máquinas virtuais criadas a partir dos recursos do Marketplace com os planos anexados não podem ser movidas entre grupos de recursos ou assinaturas. Desprovisione a máquina virtual na assinatura atual e implante novamente na nova assinatura.
* Máquinas virtuais em uma rede virtual existente não podem ser movidas para uma nova assinatura quando você não está movendo todos os recursos na rede virtual.
* Máquinas virtuais de baixa prioridade e conjuntos de escala de máquinas virtuais de baixa prioridade não podem ser movidos entre grupos de recursos ou assinaturas.
* As máquinas virtuais em um conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com backup do Azure

Para mover máquinas virtuais configuradas com o Azure Backup, você deve excluir os pontos de restauração do cofre.

Se [a exclusão suave](../../../backup/backup-azure-security-feature-cloud.md) estiver ativada para sua máquina virtual, você não poderá mover a máquina virtual enquanto esses pontos de restauração forem mantidos. Desabilite [a exclusão suave](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete) ou espere 14 dias após a exclusão dos pontos de restauração.

### <a name="portal"></a>Portal

1. Selecione a máquina virtual configurada para backup.

1. No painel esquerdo, selecione **Backup**.

1. Selecione **Stop backup**.

1. Selecione **Excluir dados de volta**.

1. Depois que a exclusão estiver concluída, você pode mover o cofre e a máquina virtual para a assinatura de destino. Após a mudança, você pode continuar backups.

### <a name="powershell"></a>PowerShell

* Localize o local de sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se estiver no PowerShell, use o cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.

### <a name="azure-cli"></a>CLI do Azure

* Localize o local de sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se estiver na CLI, use o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.

## <a name="next-steps"></a>Próximas etapas

* Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).

* Para saber mais sobre como mover cofres do Serviço de Recuperação para o backup, veja [Limitações dos serviços de recuperação](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
