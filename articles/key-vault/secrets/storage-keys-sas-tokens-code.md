---
title: Buscar tokens de assinatura de acesso compartilhado no código | Azure Key Vault
description: O recurso de conta de armazenamento gerenciado fornece uma integração direta, entre Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232586"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Buscar tokens de assinatura de acesso compartilhado no código

Você pode gerenciar sua conta de armazenamento com tokens de SAS (assinatura de acesso compartilhado) armazenados no cofre de chaves. Para obter mais informações, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS](../../storage/common/storage-sas-overview.md).

Este artigo fornece exemplos de código .NET que busca um token SAS e executa operações com ele. Para obter informações sobre como criar e armazenar tokens SAS, consulte [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](overview-storage-keys.md) ou [gerenciar chaves de conta de armazenamento com Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código busca um token SAS do cofre de chaves, usa-o para criar uma nova conta de armazenamento e cria um novo cliente de serviço BLOB.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Se o token de assinatura de acesso compartilhado estiver prestes a expirar, você poderá buscar o token de assinatura de acesso compartilhado do cofre de chaves e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Próximas etapas
- Saiba como [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS](../../storage/common/storage-sas-overview.md).
- Saiba como [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](overview-storage-keys.md) ou [Azure PowerShell](overview-storage-keys-powershell.md).
- Consulte [exemplos de chave da conta de armazenamento gerenciado](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
