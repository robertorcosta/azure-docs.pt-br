---
title: Conta de armazenamento gerenciado do Azure Key Vault - versão PowerShell
description: O recurso de conta de armazenamento gerenciado fornece uma integração direta, entre Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81431261"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Buscar tokens de assinatura de acesso compartilhado no código

Você pode gerenciar sua conta de armazenamento com os [tokens de assinatura de acesso compartilhado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) em seu cofre de chaves. Este artigo fornece exemplos de código C# que busca um token SAS e executa operações com ele.  Para obter informações sobre como criar e armazenar tokens SAS, consulte [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](overview-storage-keys.md) ou [gerenciar chaves de conta de armazenamento com Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código busca um token SAS do cofre de chaves, usa-o para criar uma nova conta de armazenamento e cria um novo cliente de serviço BLOB.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se o token de assinatura de acesso compartilhado estiver prestes a expirar, você poderá buscar o token de assinatura de acesso compartilhado do cofre de chaves e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Próximas etapas
- Saiba como [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](overview-storage-keys.md) ou [Azure PowerShell](overview-storage-keys-powershell.md).
- Consulte [exemplos de chave da conta de armazenamento gerenciado](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Referência do PowerShell do Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
