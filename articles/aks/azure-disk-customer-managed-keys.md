---
title: Use uma chave gerenciada pelo cliente para criptografar discos Azure no Azure Kubernetes Service (AKS)
description: Traga suas próprias chaves (BYOK) para criptografar os discos AKS OS e Data.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596497"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga suas próprias chaves (BYOK) com discos Azure no Azure Kubernetes Service (AKS)

O Azure Storage criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para controle adicional sobre chaves de criptografia, você pode fornecer [chaves gerenciadas pelo cliente][customer-managed-keys] para usar para criptografia em repouso para o Sistema Operacional e discos de dados para seus clusters AKS.

> [!NOTE]
> Os clusters AKS baseados no BYOK Linux e no Windows estão disponíveis em [regiões do Azure][supported-regions] que suportam criptografia lateral do servidor de discos gerenciados do Azure.

## <a name="before-you-begin"></a>Antes de começar

* Este artigo pressupõe que você está criando um *novo cluster AKS*.

* Você deve ativar a proteção de exclusão e purga suave para *o Azure Key Vault* ao usar o Key Vault para criptografar discos gerenciados.

* Você precisa da versão Azure CLI 2.0.79 ou posterior e da extensão aks-preview 0.4.26

> [!IMPORTANT]
> Os recursos de visualização do AKS são opt-in de autoatendimento. As visualizações são fornecidas "como está" e "conforme disponível" e são excluídas dos contratos de nível de serviço e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente na melhor base de esforço. Como tal, esses recursos não são destinados ao uso da produção. Para obter uma infromation adicional, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS](support-policies.md)
> * [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instale a mais recente extensão de pré-visualização AKS CLI

Para usar as chaves gerenciadas pelo cliente, você precisa da versão CLI *de visualização aks* 0.4.26 ou superior. Instale a extensão *aks-preview* Azure CLI usando o comando [az extension add][az-extension-add] e verifique se há atualizações disponíveis usando o comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância do Azure Key Vault

Use uma instância do Azure Key Vault para armazenar suas chaves.  Você pode usar opcionalmente o portal Azure para [configurar chaves gerenciadas pelo cliente com o Azure Key Vault][byok-azure-portal]

Crie um novo *grupo de recursos,* crie uma nova instância *do Key Vault* e habilite a proteção de exclusão e purga suave.  Certifique-se de usar os nomes da mesma região e do grupo de recursos para cada comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Criar uma instância de um DiskEncryptionSet

Substitua *myKeyVaultName* pelo nome do seu cofre de chaves.  Você também precisará de uma *chave* armazenada no Azure Key Vault para concluir as seguintes etapas.  Armazene sua chave existente no Cofre de Chaves que você criou nas etapas anteriores ou [gere uma nova chave][key-vault-generate] e substitua *o myKeyName* abaixo pelo nome da sua chave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Conceda ao DiskEncryptionSet acesso ao cofre de chaves

Use o DiskEncryptionSet e os grupos de recursos criados nas etapas anteriores e conceda ao recurso DiskEncryptionSet acesso ao Cofre de Chaves do Azure.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Crie um novo cluster AKS e criptografe o disco do sistema operacional

Crie um **novo grupo de recursos** e um cluster AKS e use sua chave para criptografar o disco do SISTEMA OPERACIONAL. As chaves gerenciadas pelo cliente só são suportadas em versões Kubernetes maiores que 1,17. 

> [!IMPORTANT]
> Certifique-se de criar um novo grupo de resoruce para o seu cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Quando novos pools de nós são adicionados ao cluster criado acima, a chave gerenciada pelo cliente fornecida durante a criação é usada para criptografar o disco do SISTEMA OPERACIONAL.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Criptografe seu disco de dados de cluster AKS

Você também pode criptografar os discos de dados AKS com suas próprias chaves.

> [!IMPORTANT]
> Certifique-se de ter as credenciais AKS adequadas. O principal do Serviço precisará ter acesso ao grupo de recursos onde o conjunto de criptografia de disco é implantado. Caso contrário, você terá um erro sugerindo que o diretor do serviço não tem permissões.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Crie um arquivo chamado **byok-azure-disk.yaml** que contém as seguintes informações.  Substitua myAzureSubscriptionId, myResourceGroup e myDiskEncrptionSetName por seus valores e aplique o inhame.  Certifique-se de usar o grupo de recursos onde o DiskEncryptionSet está implantado.  Se você usar o Azure Cloud Shell, este arquivo pode ser criado usando vi ou nano como se estivesse trabalhando em um sistema virtual ou físico:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Em seguida, execute esta implantação em seu cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitações

* BYOK está disponível apenas no MOMENTO em GA e Preview em certas [regiões do Azure][supported-regions]
* Sistema de criptografia de disco do OS suportado com Kubernetes versão 1.17 e acima   
* Disponível apenas em regiões onde o BYOK é suportado
* A criptografia com chaves gerenciadas pelo cliente atualmente é apenas para novos clusters AKS, os clusters existentes não podem ser atualizados
* O cluster AKS usando conjuntos de escala de máquinavirtual são necessários, sem suporte para conjuntos de disponibilidade de máquinas virtuais


## <a name="next-steps"></a>Próximas etapas

Revise [as práticas recomendadas para segurança de cluster AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
