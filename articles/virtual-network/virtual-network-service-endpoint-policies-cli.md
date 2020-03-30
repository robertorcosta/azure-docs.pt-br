---
title: Restringir a exfiltração de dados ao Armazenamento Azure - Azure CLI
description: Neste artigo, você aprende como limitar e restringir a exfiltração de dados de rede virtual aos recursos do Azure Storage com políticas de ponto final de serviço de rede virtual usando o Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271178"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gerencie a exfiltração de dados para contas de armazenamento do Azure com políticas de ponto final de serviço de rede virtual usando o Cli do Azure

As políticas de ponto final de serviço de rede virtual permitem que você aplique controle de acesso em contas do Azure Storage a partir de uma rede virtual sobre pontos finais de serviço. Esta é uma chave para proteger suas cargas de trabalho, gerenciar quais contas de armazenamento são permitidas e onde a exfiltração de dados é permitida.
Neste artigo, você aprenderá como:

* Crie uma rede virtual e adicione uma sub-rede.
* Habilite o ponto final de serviço para o armazenamento Azure.
* Crie duas contas de armazenamento do Azure e permita o acesso da rede a ela a partir da sub-rede criada acima.
* Crie uma política de ponto final de serviço para permitir o acesso apenas a uma das contas de armazenamento.
* Implante uma máquina virtual (VM) na sub-rede.
* Confirme o acesso à conta de armazenamento permitida a partir da sub-rede.
* Confirmar o acesso é negado à conta de armazenamento não permitida da sub-rede.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, consulte [Install Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local eastus.*

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma rede virtual com uma sub-rede com [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço 

Neste exemplo, um ponto final de serviço para *Microsoft.Storage* é criado para a sub-rede *Private*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso à rede de uma sub-rede

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O seguinte exemplo cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança de rede à sub-rede *Privada* com [az network vnet subnet update](/cli/azure/network/vnet/subnet). O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *Privada*:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Crie regras de segurança com [az network nsg rule create](/cli/azure/network/nsg/rule). A regra que segue permite o acesso de saída para os endereços IP públicos atribuídos ao serviço de Armazenamento do Microsoft Azure: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Cada grupo de segurança da rede contém várias [regras de segurança padrão](security-overview.md#default-security-rules). A regra a seguir substitui uma regra de segurança padrão que permite o acesso de saída a todos os endereços IP públicos. A `destination-address-prefix "Internet"` opção nega o acesso de saída a todos os endereços IP públicos. A regra anterior substitui essa regra, devido à sua prioridade mais alta, o que permite acesso aos endereços IP públicos do Armazenamento do Microsoft Azure.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

A regra a seguir permite que o tráfego SSH entre na sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. O SSH é permitido à sub-rede para que a conectividade possa ser testada em uma etapa posterior.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede às contas do Azure Storage

Esta seção lista etapas para restringir o acesso à rede de uma conta do Azure Storage a partir da sub-rede dada em uma rede Virtual via ponto final de serviço.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie duas contas de armazenamento Azure [com a criação de uma conta de armazenamento az](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depois que as contas de armazenamento forem criadas, recupere a seqüência de conexão para as contas de armazenamento em uma variável com [a cadeia de exibição de conexão](/cli/azure/storage/account)da conta de armazenamento az . A cadeia de conexão é usada para criar um compartilhamento de arquivos em uma etapa posterior.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Exiba o conteúdo da variável e observe o valor para **AccountKey** retornado na saída, porque ele é usado em uma etapa posterior.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar um compartilhamento de arquivos na conta de armazenamento

Crie um compartilhamento de arquivos na conta de armazenamento com [az storage share create](/cli/azure/storage/share). Em uma etapa posterior, esse compartilhamento de arquivos é montado para confirmar o acesso à rede para ele.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Negar todo o acesso da rede à conta de armazenamento

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *Negar* com [az storage account update](/cli/azure/storage/account). Depois que o acesso à rede for negado, a conta de armazenamento não estará acessível em nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Permitir o acesso à rede a partir da sub-rede virtual

Permita o acesso à rede para a conta de armazenamento a partir da sub-rede *Privada* com [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar política para permitir o acesso à conta de armazenamento válida

As políticas do Azure Service Endpoint só estão disponíveis para o Armazenamento Azure. Então, vamos ativar o Service Endpoint para *Microsoft.Storage* nesta sub-rede para esta configuração de exemplo.

As políticas de ponto final de serviço são aplicadas sobre os pontos finais do serviço. Começaremos criando uma política de ponto final de serviço. Em seguida, criaremos as definições de diretiva esta política para que as contas do Azure Storage sejam listadas em branco para esta sub-rede

Criar uma política de ponto de extremidade de serviço

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Salve o RECURSO URI para a conta de armazenamento permitida em uma variável. Antes de executar o comando abaixo, substitua * \<o>de id de sua assinatura* pelo valor real do seu ID de assinatura.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Criar & adicionar uma definição de diretiva para permitir que a conta do Azure Storage acima seja à política de ponto final do serviço

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

E atualize a sub-rede virtual para associar a ela a política de ponto final de serviço criada na etapa anterior

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validar a restrição de acesso às contas do Azure Storage

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Para testar o acesso da rede a uma conta de armazenamento, implante uma VM na sub-rede.

Crie uma VM na sub-rede *Private* com [acriação de az vm](/cli/azure/vm). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Após a criação, anote o **publicIpAddress** na saída retornada. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

### <a name="confirm-access-to-storage-account"></a>Confirmar acesso à conta de armazenamento

SSH para a VM *myVmPrivate*. Substitua * \<publicIpAddress>* pelo endereço IP público do seu *vm myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Monte o compartilhamento de arquivos do Azure para o diretório que você criou. Antes de executar o * \<* comando abaixo, substitua o>de chave de conta de armazenamento pelo valor da *AccountKey* de **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Você receberá o aviso `user@myVmPrivate:~$`. O compartilhamento de arquivos do Azure montado com êxito para */mnt/Retention/ MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso é negado para a conta de armazenamento

A partir do mesmo *vm myVmPrivate,* crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Tente montar o compartilhamento de arquivos do Azure da conta de armazenamento *não permitidastorageacc* para o diretório que você criou. Este artigo presume que você implantou a versão mais recente do Ubuntu. Se você estiver usando versões anteriores do Ubuntu, consulte [Montar no Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre como montar compartilhamentos de arquivos. 

Antes de executar o * \<* comando abaixo, substitua o>de chave de conta de armazenamento pelo valor da *AccountKey* de **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado, `mount error(13): Permission denied` e você recebe um erro, porque essa conta de armazenamento não está na lista de permitir a política de ponto final de serviço que aplicamos à sub-rede. 

Saia da sessão SSH para a VM *myVmPublic*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aplicou uma política de ponto final de serviço sobre um ponto final de serviço de rede virtual do Azure para o Azure Storage. Você criou contas de armazenamento Azure e acesso limitado à rede a apenas certas contas de armazenamento (e, portanto, negou outras) a partir de uma sub-rede virtual. Para saber mais sobre as políticas de ponto final de serviço, consulte [visão geral das políticas de pontos finais do Serviço](virtual-network-service-endpoint-policies-overview.md).
