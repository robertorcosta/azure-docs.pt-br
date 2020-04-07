---
title: Criptografia do lado do servidor dos discos gerenciados do Azure - Azure CLI
description: O Azure Storage protege seus dados criptografando-os em repouso antes de permê-los em clusters de armazenamento. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de seus discos gerenciados ou usar chaves gerenciadas pelo cliente para gerenciar criptografia com suas próprias chaves.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f7eb63d0bbdce86f4a7195430dc15d6873e9f6e6
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754307"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Criptografia do lado do servidor dos discos gerenciados do Azure

Os discos gerenciados do Azure criptografam automaticamente seus dados por padrão ao persistirem na nuvem. A criptografia do lado do servidor protege seus dados e ajuda você a cumprir seus compromissos de segurança organizacional e conformidade. Os dados em discos gerenciados do Azure são criptografados de forma transparente usando [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2.   

A criptografia não afeta o desempenho dos discos gerenciados. Não há custo adicional para a criptografia.

Para obter mais informações sobre os módulos criptográficos subjacentes aos discos gerenciados pelo Azure, consulte [API de criptografia: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode confiar em chaves gerenciadas pela plataforma para a criptografia do seu disco gerenciado ou gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, você pode especificar uma *chave gerenciada pelo cliente* para usar para criptografar e descriptografar todos os dados em discos gerenciados. 

As seções a seguir descrevem cada uma das opções para gerenciamento de chaves com mais detalhes.

## <a name="platform-managed-keys"></a>Chaves gerenciadas por plataforma

Por padrão, os discos gerenciados usam chaves de criptografia gerenciadas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos gerenciados, snapshots, imagens e novos dados gravados em discos gerenciados existentes são criptografados automaticamente com chaves gerenciadas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

Você pode optar por gerenciar a criptografia no nível de cada disco gerenciado, com suas próprias chaves. A criptografia do lado do servidor para discos gerenciados com chaves gerenciadas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Você pode importar [suas chaves RSA](../../key-vault/key-vault-hsm-protected-keys.md) para o seu Cofre de Chaves ou gerar novas chaves RSA no Azure Key Vault. 

Os discos gerenciados do Azure lidam com a criptografia e a descriptografia de forma totalmente transparente usando [criptografia de envelopes](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ele criptografa dados usando uma chave de criptografia de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que, por sua vez, é protegida usando suas chaves. O serviço de armazenamento gera chaves de criptografia de dados e as criptografa com chaves gerenciadas pelo cliente usando criptografia RSA. A criptografia do envelope permite que você gire (alterar) suas chaves periodicamente de acordo com suas políticas de conformidade sem afetar suas VMs. Quando você gira suas chaves, o serviço de armazenamento recriptografa as chaves de criptografia de dados com as novas chaves gerenciadas pelo cliente. 

Você tem que conceder acesso a discos gerenciados em seu Cofre de Chaves para usar suas chaves para criptografar e descriptografar o DEK. Isso permite que você tenha controle total de seus dados e chaves. Você pode desativar suas chaves ou revogar o acesso a discos gerenciados a qualquer momento. Você também pode auditar o uso da chave de criptografia com o monitoramento do Azure Key Vault para garantir que apenas discos gerenciados ou outros serviços confiáveis do Azure estejam acessando suas chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Quando você desativar ou excluir sua chave, quaisquer VMs com discos usando essa chave serão automaticamente desligados. Depois disso, as VMs não serão utilizáveis a menos que a chave esteja ativada novamente ou você atribua uma nova chave.

Para discos ultra, quando você desativar ou excluir uma chave, quaisquer VMs com discos ultra usando a chave não serão automaticamente desligados. Depois de desalocar e reiniciar as VMs, os discos pararão de usar a chave e, em seguida, as VMs não voltarão a funcionar. Para colocar as VMs novamente on-line, você deve atribuir uma nova chave ou ativar a chave existente.

O diagrama a seguir mostra como os discos gerenciados usam o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Fluxo de trabalho gerenciado em disco e chaves gerenciadas pelo cliente. Um administrador cria um Azure Key Vault, cria um conjunto de criptografia de disco e configura o conjunto de criptografia de disco. O Conjunto está associado a uma VM, que permite que o disco faça uso do Azure AD para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A lista a seguir explica o diagrama com ainda mais detalhes:

1. Um administrador do Azure Key Vault cria recursos de cofre chave.
1. O admin do cofre chave importa suas chaves RSA para key vault ou gera novas chaves RSA no Key Vault.
1. Esse administrador cria uma instância do recurso Disk Encryption Set, especificando um ID do Cofre de Chaves do Azure e uma URL chave. O Disk Encryption Set é um novo recurso introduzido para simplificar o gerenciamento de chaves para discos gerenciados. 
1. Quando um conjunto de criptografia de disco é criado, uma [identidade gerenciada atribuída ao sistema](../../active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Active Directory (AD) e associada ao conjunto de criptografia de disco. 
1. O administrador do cofre chave do Azure concede então a permissão de identidade gerenciada para executar operações no cofre-chave.
1. Um usuário de VM cria discos associando-os ao conjunto de criptografia de disco. O usuário de VM também pode habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para os recursos existentes, associando-os ao conjunto de criptografia de disco. 
1. Os discos gerenciados usam a identidade gerenciada para enviar solicitações ao Cofre de Chaves do Azure.
1. Para ler ou escrever dados, os discos gerenciados enviam solicitações ao Azure Key Vault para criptografar (embrulhar) e descriptografar (desembrulhar) a chave de criptografia de dados para executar a criptografia e a descriptografia dos dados. 

Para revogar o acesso às chaves gerenciadas pelo cliente, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os dados da conta de armazenamento, já que a chave de criptografia é inacessível pelo Azure Storage.

### <a name="supported-regions"></a>Regiões com suporte

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não será possível desativá-lo.
    Se você precisar contornar isso, você deve [copiar todos os dados](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) para um disco gerenciado totalmente diferente que não esteja usando chaves gerenciadas pelo cliente.
- Apenas [as teclas RSA "macias" e "duras"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) do tamanho 2048 são suportadas, sem outras chaves ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e devem estar na mesma assinatura.
- Os snapshots criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Imagens personalizadas criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente não podem ser usadas na galeria de imagens compartilhadas.
- Todos os recursos relacionados às suas chaves gerenciadas pelo cliente (Azure Key Vaults, conjuntos de criptografia de disco, VMs, discos e snapshots) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem ser mover para outra assinatura.
- Se você usar o portal Azure para criar seu conjunto de criptografia de disco, você não poderá usar instantâneos por enquanto.
- Discos gerenciados criptografados usando chaves gerenciadas pelo cliente também não podem ser criptografados com criptografia de disco do Azure.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurando seu cofre de chaves do Azure e diskEncryptionSet

1. Certifique-se de que você instalou a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entrou em uma conta do Azure com [az login](/cli/azure/reference-index).

1. Crie uma instância do Azure Key Vault e da chave de criptografia.

    Ao criar a instância do Cofre de Chaves, você deve ativar a proteção de exclusão suave e purga. A exclusão suave garante que o Cofre de Chaves mantenha uma chave excluída para um determinado período de retenção (padrão de 90 dias). A proteção contra expurgo garante que uma chave excluída não possa ser excluída permanentemente até que o período de retenção seja apagado. Essas configurações protegem você de perder dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

    > [!IMPORTANT]
    > Não caso a região, se você fizer isso, você pode ter problemas ao atribuir discos adicionais ao recurso no portal Azure.

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

1.    Conceda ao recurso DiskEncryptionSet acesso ao cofre principal. 

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do diskEncryptionSet em seu Diretório Ativo do Azure. Se você tiver um erro como "Não é possível encontrar o objeto Active Directory" ao executar o seguinte comando, espere alguns minutos e tente novamente.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie uma VM usando uma imagem do Marketplace, criptografando o Sistema Operacional e os discos de dados com chaves gerenciadas pelo cliente

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


#### <a name="encrypt-existing-unattached-managed-disks"></a>Criptografe discos gerenciados não conectados existentes 

Os discos existentes não devem ser anexados a uma VM em execução para que você os criptografe usando o seguinte script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual usando uma imagem do Marketplace, criptografando o Sistema Operacional e os discos de dados com chaves gerenciadas pelo cliente

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio criptografado usando criptografia do lado do servidor com chaves gerenciadas pelo cliente e anexe-a a uma VM

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Alterar a chave de um DiskEncryptionSet para girar a chave para todos os recursos que fazem referência ao DiskEncryptionSet

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
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura as chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos sob as cobertas. Se você posteriormente mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte [Transferir uma assinatura entre os diretórios AD do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura as chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos sob as cobertas. Se você posteriormente mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte [Transferir uma assinatura entre os diretórios AD do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Criptografia do lado do servidor versus criptografia de disco Azure

[A criptografia de disco do Azure para máquinas virtuais e conjuntos](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) de escala de máquinas virtuais aproveita o recurso [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para criptografar discos gerenciados com chaves gerenciadas pelo cliente dentro da VM convidada.  A criptografia do lado do servidor com chaves gerenciadas pelo cliente melhora no ADE, permitindo que você use quaisquer tipos e imagens do Sistema operacional para suas VMs criptografando dados no serviço de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Explorar os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../../key-vault/key-vault-overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs VMware para o Azure usando o PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
