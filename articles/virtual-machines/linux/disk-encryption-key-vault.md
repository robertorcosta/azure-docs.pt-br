---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com criptografia de disco Azure
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970634"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

A Criptografia de Disco do Azure usa o Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../../key-vault/key-vault-get-started.md) e [Proteja seu cofre de chaves](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se você já usou a Criptografia de Disco Do Azure com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Criar e configurar um cofre de chaves para criptografia de disco Azure com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para obter detalhes.

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criando um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas avançadas de acesso do cofre de chaves.

Estas etapas são ilustradas nas seguintes partidas rápidas:

- [Criar e criptografar uma VM do Linux com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Crie e criptografe um VM Linux com o Azure PowerShell](disk-encryption-cli-quickstart.md)

Você também pode, se desejar, gerar ou importar uma chave de criptografia (KEK).

> [!Note]
> As etapas deste artigo são automatizadas nos pré-requisitos de criptografia de disco do [Azure, script CLI](https://github.com/ejarvi/ade-cli-getting-started) e [pré-requisitos do Azure Disk Encryption do PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e conecte-se ao Azure

As etapas deste artigo podem ser completadas com o [Azure CLI](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal Azure](https://portal.azure.com). 

Embora o portal esteja acessível através do seu navegador, o Azure CLI e o Azure PowerShell exigem instalação local; consulte [Azure Disk Encryption for Linux: instale ferramentas](disk-encryption-linux.md#install-tools-and-connect-to-azure) para detalhes.

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar o Azure CLI ou Azure PowerShell, você deve primeiro conectar-se à sua assinatura do Azure. Você faz isso [fazendo login com a Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), fazendo login com o [Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

*Se você já tiver um grupo de recursos, você pode pular para [Criar um cofre-chave](#create-a-key-vault).*

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

Crie um grupo de recursos usando o [grupo az criar](/cli/azure/group?view=azure-cli-latest#az-group-create) o comando Azure CLI, o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell ou a partir do [portal Azure](https://portal.azure.com).

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave

*Se você já tiver um cofre de chaves, você pode pular para [Definir políticas de acesso avançado do cofre de chaves](#set-key-vault-advanced-access-policies).*

Crie um cofre de chaves usando o [cofre de chave az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) o comando Azure CLI, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, o [portal Azure](https://portal.azure.com)ou um [modelo de Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Para garantir que os segredos de criptografia não ultrapassem as fronteiras regionais, o Azure Disk Encryption requer que o Key Vault e as VMs sejam co-localizados na mesma região. Crie e use um Key Vault que esteja na mesma região que as VMs a serem criptografadas. 

Cada Key Vault deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre de chaves usando o Azure CLI, adicione o sinalizador "--ativado-para-criptografia de disco".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves usando o Azure PowerShell, adicione o sinalizador "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode criar um cofre de chaves usando o [modelo Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, a localização do grupo de recursos, o nome do Cofre de Chaves, o ID do objeto, os termos legais e o contrato e clique em **Comprar**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas de acesso avançado do cofre de chaves

A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. 

Se você não habilitou o cofre de chaves para criptografia de disco, implantação ou implantação de modelo no momento da criação (como demonstrado na etapa anterior), você deve atualizar suas políticas de acesso avançado.  

### <a name="azure-cli"></a>CLI do Azure

Use [atualização de keyvault az](/cli/azure/keyvault#az-keyvault-update) para habilitar a criptografia de disco para o Cofre de chaves. 

 - **Habilite o cofre de chaves para criptografia de disco:** habilitado para criptografia de disco é necessária. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ative o cofre de chaves para implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é mencionado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilite o cofre de chaves para implantação de modelo, se necessário:** permitem que o Gerenciador de recursos para recuperar segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Use o cofre-chave PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para habilitar a criptografia de disco para o cofre de chaves.

  - ** Ative o Key Vault para criptografia de disco: ** EnabledForDiskEncryption é necessário para a criptografia do Azure Disk.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Ative o cofre de chaves para implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é mencionado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Ative o cofre de chaves para implantação de modelos, se necessário:** Permite que o Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves for mencionado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione seu cofre principal, vá para **Políticas de acesso**e clique para mostrar políticas de acesso **avançadas**.
2. Selecione a caixa rotulada **habilitar o acesso ao Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar acesso ao Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Salvar**.

    ![Cofre de chaves do Azure, políticas de acesso avançadas](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configure uma chave de criptografia (KEK)

Se você quiser usar uma chave de criptografia (KEK) para uma camada adicional de segurança para chaves de criptografia, adicione uma KEK ao seu cofre de chaves. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault.

Você pode gerar um novo KEK usando o comando [azure CLI az keyvault keyvault create,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) ou o [portal Azure](https://portal.azure.com/). Você deve gerar um tipo de chave RSA; A Criptografia de disco do Azure ainda não suporta o uso de chaves de curva elíptica.

Em vez disso, você pode importar um KEK do seu HSM de gerenciamento de chaves no local. Para obter mais informações, consulte [documentação do cofre de chaves](../../key-vault/key-vault-hsm-protected-keys.md). 

Os URLs KEK do cofre-chave devem ser versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

* Exemplo de uma URL secreta válida:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de uma URL KEK válida:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL do cofre de chaves aceitável:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves inaceitável:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Use o comando Azure CLI [az keyvault create para](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) gerar um novo KEK e armazená-lo no cofre principal.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Em vez disso, você pode importar uma chave privada usando o comando de importação de chave keyvault Azure CLI [az:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

Em ambos os casos, você fornecerá o nome do seu KEK para a criptografia Azure CLI [az vm habilitar](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --parâmetro de chave-criptografia-chave. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) do Azure PowerShell para gerar um novo KEK e armazená-lo no cofre principal.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, você pode importar uma chave privada usando o comando de [importação de chave az az azure](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) PowerShell.

Em ambos os casos, você fornecerá o ID do cofre da chave KEK e a URL do seu KEK para os parâmetros Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e -KeyEncryptionKeyUrl parâmetros. Observe que este exemplo pressupõe que você está usando o mesmo cofre de chaves tanto para a chave de criptografia de disco quanto para o KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Conheça [os cenários de criptografia de disco do Azure em VMs Linux](disk-encryption-linux.md)
- Saiba como [solucionar problemas na criptografia de disco do Azure](disk-encryption-troubleshooting.md)
- Leia os scripts de [exemplo de criptografia de disco do Azure](disk-encryption-sample-scripts.md)
