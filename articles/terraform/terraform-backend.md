---
title: Tutorial – Armazenar o estado Terraform no Armazenamento do Azure
description: Uma introdução ao armazenamento do estado do Terraform no Armazenamento do Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 374936c39221d79d59fc8a54dc2bc4a49800240d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078572"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Tutorial: Armazene o estado Terraform no armazenamento do Azure

O estado terraform é usado para reconciliar recursos implantados com configurações Terraform. O estado permite que o Terraform saiba quais recursos do Azure adicionar, atualizar ou excluir. Por padrão, o estado Terraform é armazenado localmente ao executar o comando `terraform apply`. Essa configuração não é ideal pelos seguintes motivos:

- O estado local não funciona bem em um ambiente de equipe ou colaborativo.
- O estado Terraform pode incluir informações confidenciais.
- Armazenar o estado localmente aumenta a chance de exclusão inadvertida.

O Terraform dá suporte à persistência de estado em armazenamento remoto. Um desses back-end compatíveis é o Armazenamento do Azure. Este documento mostra como configurar e usar o Armazenamento do Azure para essa finalidade.

## <a name="configure-storage-account"></a>Configurar conta de armazenamento

Antes de usar o Armazenamento do Azure como um back-end, você deve criar uma conta de armazenamento. A conta de armazenamento pode ser criada com o portal do Azure, o PowerShell, o CLI do Azure ou o próprio Terraform. Use a amostra a seguir para configurar a conta de armazenamento com a CLI do Azure.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso ao armazenamento. Esses valores são necessários ao configurar o estado remoto.

## <a name="configure-state-back-end"></a>Configurar o back-end de estado

O back-end de estado Terraform é configurado quando você executa o comando `terraform init`. Os seguintes dados são necessários para configurar o back-end de estado:

- **storage_account_name**: O nome da conta de Armazenamento do Azure.
- **container_name**: O nome do contêiner de blob.
- **key**: O nome do arquivo de repositório de estado a ser criado.
- **access_key**: A chave de acesso de armazenamento.

Cada um desses valores pode ser especificado no arquivo de configuração ou na linha de comando. Recomendamos que você use uma variável de ambiente para o valor `access_key`. Usar uma variável de ambiente impede que a chave seja gravada no disco.

Crie uma variável de ambiente denominada `ARM_ACCESS_KEY` com o valor da chave de acesso do Armazenamento do Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso da conta do Armazenamento do Azure, armazene-a no Cofre de Chaves do Azure. A variável de ambiente pode então ser definida usando um comando semelhante ao seguinte. Para obter mais informações sobre o Azure Key Vault, confira a [documentação do Azure Key Vault](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar o Terraform para usar o back-end, as seguintes etapas precisam ser executadas:
- Inclua um bloco de configuração `backend` com um tipo de `azurerm`.
- Adicione um valor de `storage_account_name` ao bloco de configuração.
- Adicione um valor de `container_name` ao bloco de configuração.
- Adicione um valor de `key` ao bloco de configuração.

O exemplo a seguir configura um back-end do Terraform e cria o grupo de recursos do Azure.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Inicialize a configuração executado as seguintes etapas:

1. Execute o comando `terraform init`.
1. Execute o comando `terraform apply`.

Você poderá encontrar o arquivo de estado no Azure Storage Blob.

## <a name="state-locking"></a>Estado de bloqueio

Os blobs de Armazenamento do Azure são bloqueados automaticamente antes de qualquer operação que grava o estado. Esse padrão impede operações de estado simultâneas, o que pode causar danos. 

Para obter mais informações, confira [Bloqueio de Estado](https://www.terraform.io/docs/state/locking.html) na documentação do Terraform.

Você pode ver o bloqueio ao examinar o blob por meio do portal do Azure ou em outras ferramentas de gerenciamento do Azure.

![BLOBs do Azure com bloqueio](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Criptografia em repouso

Os dados armazenados em um Blob do Azure são criptografados antes de serem persistidos. Quando necessário, o Terraform recupera o estado do back-end e o armazena na memória local. Usando esse padrão, o estado nunca é gravado em seu disco local.

Para obter mais informações sobre a criptografia de Armazenamento do Azure, confira [Criptografia do serviço de Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)