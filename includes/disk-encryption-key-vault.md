---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: e145c7af3ff3cdd6d3cca2a7b0da0fdc23a418b7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245247"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

*Se você já tiver um grupo de recursos, poderá pular para [criar um cofre de chaves](#create-a-key-vault).*

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

Crie um grupo de recursos usando o comando [AZ Group create](/cli/azure/group?view=azure-cli-latest#az-group-create) CLI do Azure, o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell ou o [portal do Azure](https://portal.azure.com).

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave

*Se você já tiver um cofre de chaves, poderá pular para [definir as políticas de acesso avançado do cofre de chaves](#set-key-vault-advanced-access-policies).*

Crie um cofre de chaves usando o comando [AZ keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) CLI do Azure, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) do Azure PowerShell, o [portal do Azure](https://portal.azure.com)ou um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Para garantir que os segredos de criptografia não ultrapassem os limites regionais, Azure Disk Encryption exige que o Key Vault e as VMs sejam colocalizadas na mesma região. Crie e use um Key Vault que esteja na mesma região que as VMs a serem criptografadas. 

Cada Key Vault deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre de chaves usando CLI do Azure, adicione o sinalizador "--habilitado-para-criptografia de disco".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves usando Azure PowerShell, adicione o sinalizador "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode criar um cofre de chaves usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, o nome do Key Vault, a ID do objeto, os termos legais e o contrato e clique em **comprar**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir políticas de acesso avançado do cofre de chaves

A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. 

Se você não habilitou o cofre de chaves para criptografia de disco, implantação ou implantação de modelo no momento da criação (conforme demonstrado na etapa anterior), você deve atualizar suas políticas de acesso avançadas.  

### <a name="azure-cli"></a>CLI do Azure

Use [atualização de keyvault az](/cli/azure/keyvault#az-keyvault-update) para habilitar a criptografia de disco para o Cofre de chaves. 

 - **Habilitar o Key Vault para criptografia de disco:** Enabled-for-disk-encryption é necessário. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Habilitar o Key Vault para a implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilitar o Key Vault para implantação de modelo, se necessário:** Permite que o Resource Manager recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Use o cmdlet do PowerShell do cofre de chaves [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para habilitar a criptografia de disco para o cofre de chaves.

  - **Habilitar o Key Vault para criptografia de disco:** EnabledForDiskEncryption é necessário para o Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Habilitar o Key Vault para a implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Habilitar o Key Vault para implantação de modelo, se necessário:** Permite que o Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves é referenciado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione o cofre de chaves, acesse **políticas de acesso**e **clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **habilitar o acesso ao Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar acesso ao Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Salvar**.

    ![Cofre de chaves do Azure, políticas de acesso avançadas](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurar uma chave de criptografia de chave (KEK)

Se você quiser usar uma chave de criptografia (KEK) para uma camada adicional de segurança para chaves de criptografia, adicione uma KEK ao seu cofre de chaves. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault.

Você pode gerar um novo KEK usando o comando CLI do Azure [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) ou o [portal do Azure](https://portal.azure.com/). Você deve gerar um tipo de chave RSA; Azure Disk Encryption ainda não dá suporte ao uso de chaves de curva elíptica.

Em vez disso, você pode importar um KEK do HSM de gerenciamento de chaves local. Para obter mais informações, consulte [documentação do cofre de chaves](/key-vault/key-vault-hsm-protected-keys). 

As URLs de KEK do cofre de chaves devem ter controle de versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

* Exemplo de uma URL secreta válida: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de uma URL KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL do cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves inaceitável: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Use o comando CLI do Azure [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) para gerar um novo Kek e armazená-lo em seu cofre de chaves.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Em vez disso, você pode importar uma chave privada usando o comando CLI do Azure [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

Em ambos os casos, você fornecerá o nome de seu KEK para o parâmetro CLI do Azure [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Use o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) para gerar um novo Kek e armazená-lo em seu cofre de chaves.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, você pode importar uma chave privada usando o comando Azure PowerShell [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

Em ambos os casos, você fornecerá a ID do seu cofre de chaves KEK e a URL de seu KEK para os parâmetros Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e-KeyEncryptionKeyUrl. Observe que este exemplo pressupõe que você esteja usando o mesmo cofre de chaves para a chave de criptografia de disco e o KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
