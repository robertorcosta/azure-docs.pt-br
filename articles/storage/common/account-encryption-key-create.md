---
title: Crie uma conta que suporte chaves gerenciadas pelo cliente para tabelas e filas
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento que suporte a configuração de chaves gerenciadas pelo cliente para tabelas e filas. Use o Azure CLI ou um modelo do Azure Resource Manager para criar uma conta de armazenamento que conta com a chave de criptografia da conta para criptografia do Azure Storage. Em seguida, você pode configurar as chaves gerenciadas pelo cliente para a conta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083561"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Crie uma conta que suporte chaves gerenciadas pelo cliente para tabelas e filas

O Azure Storage criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, o armazenamento em fila e o armazenamento de tabela saem de uma chave que é escopo do serviço e gerenciado pela Microsoft. Você também pode optar por usar chaves gerenciadas pelo cliente para criptografar dados de fila ou tabela. Para usar chaves gerenciadas pelo cliente com filas e tabelas, primeiro você deve criar uma conta de armazenamento que use uma chave de criptografia que é escopo para a conta, em vez de para o serviço. Depois de criar uma conta que usa a chave de criptografia da conta para dados de fila e tabela, você pode configurar chaves gerenciadas pelo cliente com o Azure Key Vault para essa conta de armazenamento.

Este artigo descreve como criar uma conta de armazenamento que dependa de uma chave que é escopo da conta. Quando a conta é criada pela primeira vez, a Microsoft usa a chave da conta para criptografar os dados na conta, e a Microsoft gerencia a chave. Posteriormente, você pode configurar chaves gerenciadas pelo cliente para a conta para tirar proveito desses benefícios, incluindo a capacidade de fornecer suas próprias chaves, atualizar a versão-chave, girar as chaves e revogar os controles de acesso.

## <a name="about-the-feature"></a>Sobre o recurso

Para criar uma conta de armazenamento que dependa da chave de criptografia da conta para o armazenamento de filas e tabelas, você deve primeiro se registrar para usar esse recurso com o Azure. Devido à capacidade limitada, esteja ciente de que pode levar vários meses até que os pedidos de acesso sejam aprovados.

Você pode criar uma conta de armazenamento que conta com a chave de criptografia da conta para o armazenamento de filas e tabelas nas seguintes regiões:

- Leste dos EUA
- Centro-Sul dos Estados Unidos
- Oeste dos EUA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registre-se para usar a chave de criptografia da conta

Para se registrar para usar a chave de criptografia da conta com o armazenamento de fila ou tabela, use o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para se registrar no PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para se registrar no Azure CLI, ligue para o comando [az feature register.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Verifique o status do seu cadastro

Para verificar o status do seu registro para armazenamento em fila ou tabela, use PowerShell ou Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para verificar o status do seu registro no PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para verificar o status do seu registro no Azure CLI, ligue para o comando [az feature.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Reregistre o provedor de recursos do Azure Storage

Depois que seu registro for aprovado, você deve recadastrar o provedor de recursos do Azure Storage. Use o PowerShell ou o Azure CLI para recadastrar o provedor de recursos.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para reregistrar o provedor de recursos no PowerShell, ligue para o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para recadastrar o provedor de recursos no Azure CLI, ligue para o comando [de registro do provedor az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Crie uma conta que use a chave de criptografia da conta

Você deve configurar uma nova conta de armazenamento para usar a chave de criptografia da conta para filas e tabelas no momento em que você criar a conta de armazenamento. O escopo da chave de criptografia não pode ser alterado após a criação da conta.

A conta de armazenamento deve ser do tipo v2 de uso geral. Você pode criar a conta de armazenamento e configurá-la para confiar na chave de criptografia da conta usando o Azure CLI ou um modelo do Azure Resource Manager.

> [!NOTE]
> Apenas o armazenamento de fila saque e tabela pode ser configurado opcionalmente para criptografar dados com a chave de criptografia da conta quando a conta de armazenamento é criada. O armazenamento blob e o Azure Files sempre usam a chave de criptografia da conta para criptografar dados.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para usar o PowerShell para criar uma conta de armazenamento que dependa da chave de criptografia da conta, certifique-se de ter instalado o módulo PowerShell do Azure, versão 3.4.0 ou posterior. Para obter mais informações, consulte [Instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento v2 de uso geral chamando o comando [New-AzStorageAccount,](/powershell/module/az.storage/new-azstorageaccount) com os parâmetros apropriados:

- Inclua `-EncryptionKeyTypeForQueue` a opção e `Account` defina seu valor para usar a chave de criptografia da conta para criptografar dados no armazenamento de filas.
- Inclua `-EncryptionKeyTypeForTable` a opção e `Account` defina seu valor para usar a chave de criptografia da conta para criptografar dados no armazenamento da tabela.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para o armazenamento geo-redundante de acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de filas e tabelas. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para usar o Azure CLI para criar uma conta de armazenamento que dependa da chave de criptografia da conta, certifique-se de ter instalado a versão 2.0.80 do Azure CLI ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento v2 de uso geral chamando o comando [az storage create,](/cli/azure/storage/account#az-storage-account-create) com os parâmetros apropriados:

- Inclua `--encryption-key-type-for-queue` a opção e `Account` defina seu valor para usar a chave de criptografia da conta para criptografar dados no armazenamento de filas.
- Inclua `--encryption-key-type-for-table` a opção e `Account` defina seu valor para usar a chave de criptografia da conta para criptografar dados no armazenamento da tabela.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para o armazenamento geo-redundante de acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de filas e tabelas. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modelo](#tab/template)

O exemplo JSON a seguir cria uma conta de armazenamento v2 de uso geral que é configurada para o armazenamento geo-redundante de acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de filas e tabelas. Lembre-se de substituir os valores do espaço reservado em suportes angulares por seus próprios valores:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Depois de criar uma conta que dependa da chave de criptografia da conta, consulte um dos seguintes artigos para configurar chaves gerenciadas pelo cliente com o Azure Key Vault:

- [Configure chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal Azure](storage-encryption-keys-portal.md)
- [Configure as chaves gerenciadas pelo cliente com o Azure Key Vault usando o PowerShell](storage-encryption-keys-powershell.md)
- [Configure as chaves gerenciadas pelo cliente com o Azure Key Vault usando o Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verifique a chave de criptografia da conta

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, ligue para o comando de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account#az-storage-account-show) Este comando retorna um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade `Account`de criptografia e verifique se ele está definido como .

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, ligue para o comando [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Este comando retorna um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `KeyType` campo para cada `Encryption` serviço dentro da propriedade `Account`e verifique se ele está definido como .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, chame o comando [de conta de armazenamento az.](/cli/azure/storage/account#az-storage-account-show) Este comando retorna um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade `Account`de criptografia e verifique se ele está definido como .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
