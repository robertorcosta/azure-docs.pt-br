---
title: Use o PowerShell para criar um SAS de delegação de usuário para um contêiner ou blob
titleSuffix: Azure Storage
description: Aprenda a criar uma delegação de usuários SAS com credenciais do Azure Active Directory usando o PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536166"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Crie uma delegação de usuários SAS para um contêiner ou blob com powerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (Azure AD) para criar uma delegação de usuários SAS para um contêiner ou blob com o Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Para criar um SAS de delegação de usuário com o PowerShell, instale a versão 1.10.0 ou posterior do módulo Az.Storage. Siga estas etapas para instalar a versão mais recente do módulo:

1. Desinstale as instalações anteriores do Azure PowerShell:

    - Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.
    - Remova todos os módulos `%Program Files%\WindowsPowerShell\Modules` **Azure** de .

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Certifique-se de que você instalou a versão 3.2.0 do Azure PowerShell ou posterior. Execute o seguinte comando para instalar a versão mais recente do módulo PowerShell de armazenamento azure:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Feche e reabra a janela do PowerShell.

Para verificar qual versão do módulo Az.Storage está instalada, execute o seguinte comando:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Para obter mais informações sobre a instalação do Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Faça login no Azure PowerShell com o Azure AD

Ligue para o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para fazer login com sua conta Azure AD:

```powershell
Connect-AzAccount
```

Para obter mais informações sobre como fazer login com a PowerShell, consulte [Faça login com o Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Atribuir permissões com rbac

Para criar uma delegação de usuários SAS do Azure PowerShell, a conta Azure AD usada para entrar no PowerShell deve ser atribuída a uma função que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateDelegationKey.** Essa permissão permite que a conta Azure AD solicite a chave de delegação do *usuário*. A chave de delegação do usuário é usada para assinar o SAS da delegação de usuários. A função que fornece a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** deve ser atribuída ao nível da conta de armazenamento, do grupo de recursos ou da assinatura. Para obter mais informações sobre as permissões RBAC para criar um SAS de delegação de usuário, consulte as **permissões de atribuição com** a seção RBAC em [Criar uma delegação de usuário SAS](/rest/api/storageservices/create-user-delegation-sas).

Se você não tiver permissões suficientes para atribuir funções RBAC a um diretor de segurança Azure AD, você pode precisar pedir ao proprietário ou administrador da conta para atribuir as permissões necessárias.

O exemplo a seguir atribui a função **Decontribuinte de dados do Blob de armazenamento,** que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** A função é escopo no nível da conta de armazenamento.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções incorporadas que incluem o **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** consulte [Funções incorporadas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Use credenciais Ad do Azure para garantir um SAS

Quando você cria uma delegação de usuário SAS com o Azure PowerShell, a chave de delegação do usuário que é usada para assinar o SAS é criada para você implicitamente. O tempo de início e o tempo de validade que você especifica para o SAS também são usados como o tempo de início e o tempo de validade para a chave de delegação do usuário. 

Como o intervalo máximo sobre o qual a chave de delegação do usuário é válida é de 7 dias a partir da data de início, você deve especificar um tempo de validade para o SAS que é dentro de 7 dias a partir do horário de início. O SAS é inválido após o vencimento da chave de delegação do usuário, de modo que um SAS com um prazo de validade superior a 7 dias ainda será válido apenas por 7 dias.

Para criar um SAS de delegação de usuário para um contêiner ou blob com o Azure PowerShell, primeiro crie um novo objeto de contexto de armazenamento azure, especificando o `-UseConnectedAccount` parâmetro. O `-UseConnectedAccount` parâmetro especifica que o comando cria o objeto de contexto a conta Azure AD com a qual você entrou.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Crie uma delegação de usuários SAS para um contêiner

Para devolver um token SAS de delegação de usuário para um contêiner, ligue para o comando [New-AzStorageContainerSASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) passando no objeto de contexto de armazenamento do Azure que você criou anteriormente.

O exemplo a seguir retorna um token SAS da delegação de usuário para um contêiner. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

O token SAS da delegação de usuário devolvido será semelhante a:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Crie uma delegação de usuários SAS para uma bolha

Para devolver um token SAS de delegação de usuário para uma bolha, chame o comando [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) passando no objeto de contexto de armazenamento do Azure que você criou anteriormente.

A seguinte sintaxe devolve uma delegação de usuário SAS para uma bolha. O exemplo especifica `-FullUri` o parâmetro, que retorna o URI bolha com o token SAS anexado. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

A delegação de usuário SAS URI retornada será semelhante a:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uma delegação de usuários SAS não suporta a definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma delegação de usuários SAS

Para revogar uma delegação de usuários SAS do Azure PowerShell, chame o comando **Revoke-AzStorageAccountUserDelegationKeys.** Este comando revoga todas as chaves de delegação de usuário associadas à conta de armazenamento especificada. Todas as assinaturas de acesso compartilhadas associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Tanto a chave de delegação do usuário quanto as atribuições de função RBAC são armazenadas em cache pelo Azure Storage, de modo que pode haver um atraso entre quando você inicia o processo de revogação e quando uma delegação de usuário existente SAS se torna inválida.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma delegação de usuários SAS (Rest API)](/rest/api/storageservices/create-user-delegation-sas)
- [Obter operação chave da delegação do usuário](/rest/api/storageservices/get-user-delegation-key)
