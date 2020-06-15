---
title: Criptografia do lado do servidor dos Managed Disks do Azure - CLI do Azure
description: O Armazenamento do Microsoft Azure protege os dados com criptografia em repouso, antes de enviá-los para clusters de armazenamento. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia dos discos gerenciados ou usar chaves gerenciadas pelo cliente para gerenciar a criptografia com suas próprias chaves.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: a27f37f9c69dcadd1234faf67e23eaaa46d33f7a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651035"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Criptografia do lado do servidor dos discos gerenciados do Azure

Os discos gerenciados do Azure criptografam automaticamente os dados por padrão ao enviá-los para a nuvem. A criptografia do lado do servidor (SSE) ajuda a proteger os dados e atender aos compromissos de conformidade e segurança de sua organização. 

Os dados nos discos gerenciados são criptografados de maneira transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis, e são compatíveis com o FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos gerenciados do Azure, veja [API de Criptografia: Próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

A criptografia não afeta o desempenho dos discos gerenciados e não há custo adicional para a criptografia. 

> [!NOTE]
> Discos temporários não são discos gerenciados e não são criptografados por SSE. Para obter mais informações sobre discos temporários, confira [Visão geral de discos gerenciados: funções de disco](managed-disks-overview.md#disk-roles).

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode contar com chaves gerenciadas pela plataforma para a criptografia do disco gerenciado ou gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os dados nos discos gerenciados. 

As seções a seguir descrevem mais detalhadamente cada uma das opções de gerenciamento de chaves.

## <a name="platform-managed-keys"></a>Chaves gerenciadas pela plataforma

Por padrão, os discos gerenciados usam chaves de criptografia gerenciadas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos gerenciados, instantâneos, imagens e novos dados gravados em discos gerenciados existentes são criptografados automaticamente em repouso com chaves gerenciadas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

Você pode optar por gerenciar a criptografia no nível de cada disco gerenciado com suas próprias chaves. A criptografia do lado do servidor para discos gerenciados com chaves gerenciadas pelo cliente oferece uma experiência integrada com o Azure Key Vault. É possível importar [as chaves RSA](../../key-vault/keys/hsm-protected-keys.md) para o Key Vault ou gerar novas chaves RSA no Azure Key Vault. 

Os discos gerenciados do Azure tratam a criptografia e a descriptografia de maneira totalmente transparente com o uso da [criptografia de envelope](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ela criptografa dados usando uma DEK (chave de criptografia de dados) baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 que, por sua vez, é protegida com suas chaves. O serviço de armazenamento gera chaves de criptografia de dados e as criptografa com chaves gerenciadas pelo cliente usando a criptografia RSA. A criptografia de envelope permite alternar (mudar) as chaves periodicamente de acordo com as políticas de conformidade, sem afetar as VMs. Quando você alterna as chaves, o serviço de armazenamento criptografa novamente as chaves de criptografia de dados com as novas chaves gerenciadas pelo cliente. 

É necessário conceder acesso aos discos gerenciados no Key Vault para usar as chaves para criptografar e descriptografar o DEK. Isso permite o controle total de dados e chaves. É possível desabilitar as chaves ou revogar o acesso aos discos gerenciados a qualquer momento. Você também pode auditar o uso da chave de criptografia com monitoramento do Azure Key Vault para garantir que apenas os discos gerenciados ou outros serviços confiáveis do Azure acessem as chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Ao desabilitar ou excluir a chave, todas as VMs com discos que usam essa chave serão desligadas automaticamente. Depois disso, as VMs não serão utilizáveis, a menos que a chave seja habilitada novamente ou você atribua uma nova chave.

Ao desabilitar ou excluir uma chave, as VMs com discos ultra que usam a chave não serão desligadas automaticamente. Depois de desalocar e reiniciar as VMs, os discos deixarão de usar a chave e as VMs não ficarão online novamente. Para colocar as VMs online novamente, você deve atribuir uma nova chave ou habilitar a chave existente.

O diagrama a seguir mostra como os discos gerenciados usam o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Fluxo de trabalho de chaves gerenciadas pelo cliente e discos gerenciados. Um administrador cria um Azure Key Vault e, em seguida, cria um conjunto de criptografia de disco e configura o conjunto de criptografia de disco. O conjunto está associado a uma VM, o que permite que o disco use o Azure Active Directory para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A lista a seguir explica o diagrama em mais detalhes:

1. Um administrador do Azure Key Vault cria os recursos do Key Vault.
1. O administrador do Key Vault importa as chaves RSA para o Key Vault ou gera novas chaves RSA no Key Vault.
1. Esse administrador cria uma instância do recurso de conjunto de criptografia de disco, especificando uma ID do Azure Key Vault e uma URL da chave. O conjunto de criptografia de disco é um novo recurso que simplifica o gerenciamento de chaves dos discos gerenciados. 
1. Quando um conjunto de criptografia de disco é criado, uma [identidade gerenciada atribuída ao sistema](../../active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Active Directory (AD) e associada ao conjunto de criptografia de disco. 
1. O administrador do Azure Key Vault concede a permissão de identidade gerenciada para realizar operações no Key Vault.
1. Um usuário da VM cria discos associando-os ao conjunto de criptografia de disco. O usuário da VM também pode habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para recursos existentes, associando-as ao conjunto de criptografia de disco. 
1. Os discos gerenciados usam a identidade gerenciada para enviar solicitações ao Azure Key Vault.
1. Para leitura ou gravação de dados, os discos gerenciados enviam solicitações ao Azure Key Vault para criptografar (encapsular) e descriptografar (desencapsular) a chave de criptografia de dados, a fim de criptografar e descriptografar os dados. 

Para revogar o acesso às chaves gerenciadas pelo cliente, confira [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia não é acessível pelo Armazenamento do Microsoft Azure.

### <a name="supported-regions"></a>Regiões com suporte

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não é possível desabilitá-lo.
    Se uma solução alternativa for necessária, você deve [copiar todos os dados](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) para um disco gerenciado totalmente diferente que não use chaves gerenciadas pelo cliente.
- Somente [chaves RSA "soft" e "hard"](../../key-vault/keys/about-keys.md) de tamanho 2048 são compatíveis, nenhuma outra chave ou tamanho.
- Os discos criados a partir de imagens personalizadas criptografadas com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e estar na mesma assinatura.
- Os instantâneos criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Todos os recursos relacionados às chaves gerenciadas pelo cliente (Azure Key Vaults, conjuntos de criptografia de disco, VMs, discos e instantâneos) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem migrar para outra assinatura.
- Se você usar o portal do Azure para criar o conjunto de criptografia de disco, não poderá usar instantâneos por enquanto.
- Discos gerenciados criptografados com chaves gerenciadas pelo cliente também não podem ser criptografados com Azure o Disk Encryption.
- Para obter informações sobre o uso de chaves gerenciadas pelo cliente com galerias de imagens compartilhadas, confira [Preview: Use chaves gerenciadas pelo cliente para criptografar imagens](../image-version-encryption.md).

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuração do Azure Key Vault e DiskEncryptionSet

1. Certifique-se de que você instalou a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entrou em uma conta do Azure com [az login](/cli/azure/reference-index).

1. Crie uma instância do Azure Key Vault e a chave de criptografia.

    Ao criar a instância do Key Vault, habilite a exclusão temporária e a proteção de limpeza. A exclusão temporária garante que a Key Vault mantenha uma chave excluída para determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não seja excluída permanentemente até que o período de retenção termine. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

    > [!IMPORTANT]
    > Não use “camel case” (misturar maiúsculas e minúsculas). Isso pode causar problemas ao atribuir discos adicionais ao recurso no portal do Azure.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Crie uma instância de um DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Conceda o acesso ao recurso DiskEncryptionSet para o Key Vault. 

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do DiskEncryptionSet no Azure Active Directory. Se você receber um erro como "não é possível encontrar o objeto Active Directory" ao executar o comando a seguir, aguarde alguns minutos e tente novamente.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie uma VM usando uma imagem do Marketplace, criptografando o sistema operacional e os discos de dados com chaves gerenciadas pelo cliente

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-managed-disks"></a>Criptografe os discos gerenciados existentes 

Os discos existentes não devem ser anexados a uma VM em execução para que você possa criptografá-los usando o seguinte script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de dimensionamento de máquinas virtuais usando uma imagem do Marketplace, criptografando o sistema operacional e os discos de dados com chaves gerenciadas pelo cliente

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio criptografado usando a criptografia do lado do servidor com chaves gerenciadas pelo cliente e anexe-o a uma VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Mude a chave de um DiskEncryptionSet para alternar a chave de todos os recursos que fazem referência ao DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Encontre o status da criptografia do lado do servidor de um disco

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente contam com as identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Ao configurar chaves gerenciadas pelo cliente, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, em seguida, você migrar a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure Active Directory para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo locatário. Portanto, as chaves gerenciadas pelo cliente podem deixar de funcionar. Para obter mais informações, confira [Transferência de uma assinatura entre diretórios do Azure Active Directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente contam com as identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Ao configurar chaves gerenciadas pelo cliente, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, em seguida, você migrar a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure Active Directory para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo locatário. Portanto, as chaves gerenciadas pelo cliente podem deixar de funcionar. Para obter mais informações, confira [Transferência de uma assinatura entre diretórios do Azure Active Directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Criptografia do lado do servidor versus criptografia de disco do Azure

[O Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada.  A criptografia do lado do servidor com chaves gerenciadas pelo cliente aprimora o ADE, permitindo usar quaisquer tipos de sistema operacional e imagens para as VMs, criptografando dados no serviço de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Conheça os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs VMware para o Azure usando o PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
