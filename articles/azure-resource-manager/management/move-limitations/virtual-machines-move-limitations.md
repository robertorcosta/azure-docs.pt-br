---
title: Mover VMs do Azure para uma nova assinatura ou grupo de recursos
description: Use Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: e812f2cee44fc48dccbd8ab66a3343e087790803
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063093"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover diretrizes para máquinas virtuais

Este artigo descreve os cenários que atualmente não têm suporte e as etapas para mover as máquinas virtuais com o backup.

## <a name="scenarios-not-supported"></a>Cenários sem suporte

Ainda não há suporte para os cenários a seguir:

* Managed Disks no Zonas de Disponibilidade não podem ser movidos para uma assinatura diferente.
* Conjuntos de dimensionamento de máquinas virtuais com Load Balancer SKU padrão ou IP público SKU Standard não podem ser movidos.
* As máquinas virtuais criadas por meio de recursos do Marketplace com planos anexados não podem ser movidas entre assinaturas. Desprovisionar a máquina virtual na assinatura atual e implantá-la novamente na nova assinatura.
* As máquinas virtuais em uma rede virtual existente não podem ser movidas para uma nova assinatura quando você não está movendo todos os recursos na rede virtual.
* As máquinas virtuais de baixa prioridade e os conjuntos de dimensionamento de máquinas virtuais de baixa prioridade não podem ser movidos entre grupos de recursos ou assinaturas.
* As máquinas virtuais em um conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Você não pode mover uma máquina virtual integrada com um cofre de chaves para implementar [Azure Disk Encryption para VMs do Linux](../../../virtual-machines/linux/disk-encryption-overview.md) ou [Azure Disk Encryption para VMs do Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Para mover a VM, você deve desabilitar a criptografia.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o backup do Azure

Para mover as máquinas virtuais configuradas com o backup do Azure, você deve excluir os pontos de restauração do cofre.

Se a [exclusão reversível](../../../backup/backup-azure-security-feature-cloud.md) estiver habilitada para sua máquina virtual, você não poderá mover a máquina virtual enquanto esses pontos de restauração forem mantidos. Desabilite a [exclusão reversível](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) ou aguarde 14 dias depois de excluir os pontos de restauração.

### <a name="portal"></a>Portal

1. Interrompa temporariamente o backup e retenha os dados de backup.
2. Para mover as máquinas virtuais configuradas com o backup do Azure, execute as seguintes etapas:

   1. Localize o local da sua máquina virtual.
   2. Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` . Por exemplo, *AzureBackupRG_westus2_1*
   3. Na portal do Azure, marque **Mostrar tipos ocultos**.
   4. Localize o recurso com o tipo **Microsoft. Compute/restorePointCollections** que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
   6. Depois que a operação de exclusão for concluída, você poderá mover sua máquina virtual.

3. Mova a VM para o grupo de recursos de destino.
4. Retome o backup.

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
