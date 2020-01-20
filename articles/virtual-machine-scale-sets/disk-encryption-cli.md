---
title: Criptografar discos para conjuntos de dimensionamento do Azure com CLI do Azure
description: Saiba como usar o Azure PowerShell para criptografar as instâncias de VM e discos conectados em conjuntos de escala de máquinas virtuais do Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279069"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Criptografar o sistema operacional e OS discos de dados anexados em um conjunto de dimensionamento de máquinas virtuais com o CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia de início rápido mostra como usar o CLI do Azure para criar e criptografar um conjunto de dimensionamento de máquinas virtuais. Para obter mais informações sobre como aplicar a criptografia de disco do Azure a um conjunto de dimensionamento de máquinas virtuais, consulte [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que está definido para atualizar automaticamente à medida que alterações são aplicadas e gera chaves SSH caso elas não existam em *~/.ssh/id_rsa*. Um disco de dados de 32Gb é anexado a cada instância VM e a [extensão de Script personalizado](../virtual-machines/linux/extensions-customscript.md) do Azure é usada para preparar os discos de dados com [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves do Azure habilitado para criptografia de disco

O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, crie um cofre de chaves com [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) na mesma assinatura e região do conjunto de dimensionamento e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Essa etapa só é necessária se você tiver um Key Vault existente que você deseje usar com a criptografia de disco. Ignore esta etapa se você tiver criado um Key Vault na seção anterior.

Defina seu próprio *keyvault_name* exclusivo. Em seguida, atualize o KeyVault com [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) e defina a política de acesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar as instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a ID de recursos do Key Vault com [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Essas variáveis são usadas para iniciar o processo de criptografia com [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Pode levar um minuto ou dois para iniciar o processo de criptografia.

Como o conjunto de dimensionamento atualiza a política no conjunto de dimensionamento criado em uma etapa anterior definida como *automática*, as instâncias de VM iniciam automaticamente o processo de criptografia. Em conjuntos de dimensionamento em que a política de atualização é manual, inicie a política de criptografia nas instâncias de VM com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Habilitar criptografia usando KEK para encapsular a chave

Você também pode usar uma chave de criptografia de chave para aumentar a segurança ao criptografar o conjunto de dimensionamento de máquinas virtuais.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:</br>
/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome]</br></br>
> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em:</br>
https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID]

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Para verificar o status da criptografia de disco, use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando instâncias de VM são criptografadas, o código relata *EncryptionState/encrypted*, conforme mostrado no seguinte exemplo de saída:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Desabilitar criptografia

Se você não quiser mais usar discos de instâncias de VM criptografadas, você pode desabilitar a criptografia com [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) da seguinte maneira:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Próximos passos

- Neste artigo, você usou a CLI do Azure para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar modelos de [Azure PowerShell](disk-encryption-powershell.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar ter Azure Disk Encryption aplicado depois que outra extensão for provisionada, você poderá usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md). 
- Um exemplo de arquivo em lotes de ponta a ponta para criptografia de disco de dados do conjunto de dimensionamento Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Este exemplo cria um grupo de recursos e o conjunto de dimensionamento Linux, monta um disco de dados de 5 GB e criptografa o conjunto de dimensionamento de máquinas virtuais.
