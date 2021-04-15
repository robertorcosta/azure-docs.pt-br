---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85601370"
---
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

- **Desabilitar a criptografia de disco com o Azure PowerShell:** para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Desabilitar a criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** Use o modelo [Desabilitar a criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) para desabilitar a criptografia.
     1. Clique em **Implantar no Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
