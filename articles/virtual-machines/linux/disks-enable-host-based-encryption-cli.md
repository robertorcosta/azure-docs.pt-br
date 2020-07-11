---
title: Habilitar criptografia de ponta a ponta usando criptografia em discos gerenciados por CLI do Azure de host
description: Use a criptografia no host para habilitar a criptografia de ponta a ponta em seus Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 705f9f3055d40d23c9ec5e24cfccfc0c96e114a5
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235790"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-cli"></a>Habilitar criptografia de ponta a ponta usando criptografia no host-CLI do Azure

Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. Para obter informações conceituais sobre criptografia no host, bem como outros tipos de criptografia de disco gerenciado, consulte [criptografia em criptografia de host de ponta a ponta para os dados da VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regiões compatíveis

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Você também pode encontrar os tamanhos de VM programaticamente. Para saber como recuperá-los programaticamente, consulte a seção [localizando tamanhos de VM com suporte](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Pré-requisitos

Para poder usar a criptografia no host para suas VMs ou conjuntos de dimensionamento de máquinas virtuais, você deve obter o recurso habilitado em sua assinatura. Envie um email para encryptionAtHost@microsoft . com com suas IDs de assinatura para obter o recurso habilitado para suas assinaturas.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Criar um Azure Key Vault e DiskEncryptionSet

Depois que o recurso estiver habilitado, você precisará configurar um Azure Key Vault e um DiskEncryptionSet, se ainda não tiver feito isso.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Habilitar a criptografia no host para discos anexados a VMs e conjuntos de dimensionamento de máquinas virtuais

Você pode habilitar a criptografia no host definindo uma nova propriedade EncryptionAtHost em securityProfile de VMs ou conjuntos de dimensionamento de máquinas virtuais usando a API versão **2020-06-01** e superior.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Scripts de exemplo

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Habilitar a criptografia no host para discos anexados a uma VM com chaves gerenciadas pelo cliente

Crie uma VM com discos gerenciados usando o URI de recurso do DiskEncryptionSet criado anteriormente.

Substitua `<yourPassword>` , `<yourVMName>` , `<yourVMSize>` ,,, `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` e `<yourRegion>` , em seguida, execute o script.

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "diskEncryptionSetId=/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" "region=<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Habilitar a criptografia no host para discos anexados a uma VM com chaves gerenciadas pela plataforma

Substitua `<yourPassword>` , `<yourVMName>` , `<yourVMSize>` , `<yourResourceGroupName>` e `<yourRegion>` , em seguida, execute o script.

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "region=<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Localizando tamanhos de VM com suporte

Não há suporte para tamanhos de VM herdados. Você pode encontrar a lista de tamanhos de VM com suporte por meio de:

Chamar a [API de SKUs de recursos](https://docs.microsoft.com/rest/api/compute/resourceskus/list) e verificar se a `EncryptionAtHostSupported` funcionalidade está definida como **true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Ou, chamando o cmdlet [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) do PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e configurou esses recursos, você pode usá-los para proteger seus discos gerenciados. O link a seguir contém scripts de exemplo, cada um com um cenário respectivo, que você pode usar para proteger seus discos gerenciados.

[Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
