---
title: Use o Azure CLI para criar uma delegação de usuários SAS para um contêiner ou blob
titleSuffix: Azure Storage
description: Aprenda a criar uma delegação de usuários SAS com credenciais do Azure Active Directory usando o Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371982"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Crie uma delegação de usuários SAS para um contêiner ou blob com o Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (Azure AD) para criar uma delegação de usuários SAS para um contêiner ou blob com o Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instalar a versão mais recente da CLI do Azure

Para usar o Azure CLI para garantir um SAS com credenciais Azure AD, primeiro certifique-se de que você instalou a versão mais recente do Azure CLI. Para obter mais informações sobre a instalação do Azure CLI, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

Para criar um SAS de delegação de usuário usando o Azure CLI, certifique-se de que você tenha instalado a versão 2.0.78 ou posterior. Para verificar sua versão instalada, use o `az --version` comando.

## <a name="sign-in-with-azure-ad-credentials"></a>Faça login com credenciais azure AD

Faça login na CLI do Azure com suas credenciais azure AD. Para obter mais informações, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Atribuir permissões com rbac

Para criar uma delegação de usuários SAS do Azure PowerShell, a conta Azure AD usada para entrar no Azure CLI deve ser atribuída a uma função que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** action. Essa permissão permite que a conta Azure AD solicite a chave de delegação do *usuário*. A chave de delegação do usuário é usada para assinar o SAS da delegação de usuários. A função que fornece a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** deve ser atribuída ao nível da conta de armazenamento, do grupo de recursos ou da assinatura.

Se você não tiver permissões suficientes para atribuir funções RBAC a um diretor de segurança Azure AD, você pode precisar pedir ao proprietário ou administrador da conta para atribuir as permissões necessárias.

O exemplo a seguir atribui a função **Decontribuinte de dados do Blob de armazenamento,** que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** A função é escopo no nível da conta de armazenamento.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções incorporadas que incluem o **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** consulte [Funções incorporadas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Use credenciais Ad do Azure para garantir um SAS

Quando você cria uma delegação de usuário SAS com o Azure CLI, a chave de delegação do usuário que é usada para assinar o SAS é criada para você implicitamente. O tempo de início e o tempo de validade que você especifica para o SAS também são usados como o tempo de início e o tempo de validade para a chave de delegação do usuário.

Como o intervalo máximo sobre o qual a chave de delegação do usuário é válida é de 7 dias a partir da data de início, você deve especificar um tempo de validade para o SAS que é dentro de 7 dias a partir do horário de início. O SAS é inválido após o vencimento da chave de delegação do usuário, de modo que um SAS com um prazo de validade superior a 7 dias ainda será válido apenas por 7 dias.

Ao criar uma delegação de `--auth-mode login` `--as-user parameters` usuários SAS, o e são necessários. Especifique `--auth-mode` o *login* para o parâmetro para que as solicitações feitas ao Azure Storage sejam autorizadas com suas credenciais Azure AD. Especifique o `--as-user` parâmetro para indicar que o SAS devolvido deve ser uma delegação de usuário SAS.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Crie uma delegação de usuários SAS para um contêiner

Para criar uma delegação de usuários SAS para um contêiner com o Azure CLI, chame o comando [az storage de gerar-sas.](/cli/azure/storage/container#az-storage-container-generate-sas)

As permissões suportadas para uma delegação de usuário SAS em um contêiner incluem Adicionar, Criar, Excluir, Listar, Ler e Gravar. As permissões podem ser especificadas de forma única ou combinada. Para obter mais informações sobre essas permissões, consulte [Criar uma delegação de usuário SAS](/rest/api/storageservices/create-user-delegation-sas).

O exemplo a seguir retorna um token SAS da delegação de usuário para um contêiner. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

O token SAS da delegação de usuário devolvido será semelhante a:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Crie uma delegação de usuários SAS para uma bolha

Para criar uma delegação de usuários SAS para uma bolha com o Azure CLI, chame o comando [az storage blob generate-sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

As permissões suportadas para uma delegação de usuário SAS em uma bolha incluem Adicionar, Criar, Excluir, Ler e Gravar. As permissões podem ser especificadas de forma única ou combinada. Para obter mais informações sobre essas permissões, consulte [Criar uma delegação de usuário SAS](/rest/api/storageservices/create-user-delegation-sas).

A seguinte sintaxe devolve uma delegação de usuário SAS para uma bolha. O exemplo especifica `--full-uri` o parâmetro, que retorna o URI bolha com o token SAS anexado. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

A delegação de usuário SAS URI retornada será semelhante a:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Uma delegação de usuários SAS não suporta a definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma delegação de usuários SAS

Para revogar uma delegação de usuários SAS da CLI do Azure, chame o comando [az storage account revoke-delegation-keys.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Este comando revoga todas as chaves de delegação de usuário associadas à conta de armazenamento especificada. Todas as assinaturas de acesso compartilhadas associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Tanto a chave de delegação do usuário quanto as atribuições de função RBAC são armazenadas em cache pelo Azure Storage, de modo que pode haver um atraso entre quando você inicia o processo de revogação e quando uma delegação de usuário existente SAS se torna inválida.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma delegação de usuários SAS (Rest API)](/rest/api/storageservices/create-user-delegation-sas)
- [Obter operação chave da delegação do usuário](/rest/api/storageservices/get-user-delegation-key)
