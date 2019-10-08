---
title: Conceder permissão para aplicativos acessarem um Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Saiba como conceder permissão para vários aplicativos acessarem um Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: b472d36f17853549f2bfc773bdcb65faf0421b3f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719000"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Fornecer a autenticação do Key Vault com uma política de controle de acesso

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A maneira mais simples de autenticar um aplicativo baseado em nuvem no Key Vault é com uma identidade gerenciada; confira [Usar uma identidade gerenciada do Serviço de Aplicativo para acessar o Azure Key Vault](managed-identity.md) para obter detalhes.  Caso você esteja criando um aplicativo local, fazendo o desenvolvimento local ou, de outro modo, não possa usar uma identidade gerenciada, registre uma entidade de serviço manualmente e forneça acesso ao cofre de chaves usando uma política de controle de acesso.  

O cofre de chaves dá suporte a até 1.024 entradas de política de acesso, com cada entrada concedendo um conjunto distinto de permissões a uma "entidade de segurança":   Por exemplo, é assim que o aplicativo de console no [Início rápido da biblioteca de clientes do Azure Key Vault para .NET](quick-create-net.md) acessa o cofre de chaves.

Para obter detalhes completos sobre o controle de acesso do Key Vault, confira [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](overview-security.md#identity-and-access-management). Para obter detalhes completos sobre o controle de acesso de [Chaves, segredos e certificados](about-keys-secrets-and-certificates.md), confira: 

- [Controle de acesso de chaves](about-keys-secrets-and-certificates.md#key-access-control)
- [Controle de acesso de segredos](about-keys-secrets-and-certificates.md#secret-access-control)
- [Controle de acesso de certificados](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre de chaves. Você pode usar um cofre de chaves existente ou criar um seguindo as etapas em um destes inícios rápidos:
   - [Criar um cofre de chaves com a CLI do Azure](quick-create-cli.md)
   - [Criar um cofre de chaves com o Azure PowerShell](quick-create-powershell.md)
   - [Criar um cofre de chaves com o portal do Azure](quick-create-portal.md).
- A [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou o [Azure PowerShell](/powershell/azure/overview). Como alternativa, você pode usar o [portal do Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Cada entrada da política de acesso do cofre de chaves concede um conjunto distinto de permissões a uma entidade de segurança:

- **Um aplicativo** Se o aplicativo for baseado em nuvem, você deverá [usar uma identidade gerenciada para acessar o Azure Key Vault](managed-identity.md), se possível
- **Um grupo do Azure AD** Embora o cofre de chaves dê suporte a apenas 1.024 entradas de política de acesso, você pode adicionar vários aplicativos e usuários a um único grupo do Azure AD e, em seguida, adicionar esse grupo como uma única entrada à política de controle de acesso.
- **Um usuário** Não **incentivamos** a permissão do acesso direto aos usuários a um cofre de chaves. O ideal é que os usuários sejam adicionados a um grupo do Azure AD, que, por sua vez, recebe acesso ao cofre de chaves. Confira [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Obter a objectID

Para fornecer acesso a um aplicativo, um grupo do Azure AD ou um usuário ao cofre de chaves, primeiro será necessário obter sua objectId.

#### <a name="applications"></a>APLICATIVOS

A objectId de um aplicativo corresponde à sua entidade de serviço associada. Para obter detalhes completos sobre entidades de serviço. confira [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Há duas maneiras de obter uma objectId para um aplicativo.  A primeira é registrar o aplicativo no Azure Active Directory. Para fazer isso, siga as etapas do início rápido [Registrar um aplicativo na plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md). Quando o registro for concluído, a objectID será listada como a "ID do Aplicativo (cliente)".

A segunda é criar uma entidade de serviço em uma janela de terminal. Com a CLI do Azure, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

A objectId será listada na saída como `clientID`.

Com o Azure PowerShell, use o cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0).


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

A objectId será listada na saída como `Id` (não `ApplicationId`).

#### <a name="azure-ad-groups"></a>Grupos do Azure AD

Você pode adicionar vários aplicativos e usuários a um grupo do Azure AD e, em seguida, fornecer ao grupo o acesso ao cofre de chaves.  Para obter mais detalhes, confira a seção [Como criar e adicionar membros a um grupo do Azure AD](#creating-and-adding-members-to-an-azure-ad-group) abaixo.

Para localizar a objectId de um grupo do Azure AD com a CLI do Azure, use o comando [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list). Devido ao grande número de grupos que podem existir em sua organização, você também deverá fornecer uma cadeia de pesquisa para o parâmetro `--display-name`.

```azurecli-interactive
az ad group list --displayname <search-string>
```
A objectId será retornada no JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Para localizar a objectId de um grupo do Azure AD com o Azure PowerShell, use o cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0). Devido ao grande número de grupos que podem existir em sua organização, provavelmente, você também desejará fornecer uma cadeia de pesquisa para o parâmetro `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Na saída, a objectId é listada como `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Usuários

Você também pode adicionar um usuário individual à política de controle de acesso de um cofre de chaves. **Não recomendamos fazer isso.** Em vez disso, incentivamos você a adicionar usuários a um grupo do Azure AD e adicionar o grupo nas políticas.

Se, mesmo assim, você desejar localizar um usuário com a CLI do Azure, use o comando [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) passando o endereço de email dos usuários para o parâmetro `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

A objectId do usuário será retornada na saída:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Para localizar um usuário com o Azure PowerShell, use o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) passando o endereço de email dos usuários para o parâmetro `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

A objectId do usuário será retornada na saída como `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de segurança ao cofre de chaves

Agora que você tem uma objectID da entidade de segurança, crie uma política de acesso para o cofre de chaves que conceda a ele as permissões obter, listar, definir e excluir para chaves e segredos, além das permissões adicionais desejadas.

Com a CLI do Azure, isso é feito passando a objectId para o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Com o Azure PowerShell, isso é feito passando a objectId para o cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Como criar e adicionar membros a um grupo do Azure AD

Você pode criar um grupo do Azure AD, adicionar aplicativos e usuários ao grupo e fornecer ao grupo o acesso ao cofre de chaves.  Isso permite que você adicione vários aplicativos a um cofre de chaves como uma única entrada de política de acesso e elimina a necessidade de fornecer aos usuários acesso direto ao cofre de chaves (o que não incentivamos). Para obter mais detalhes, confira [Gerenciar o acesso de aplicativo e recursos usando grupos do Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="addition-prerequisites"></a>Pré-requisitos de adição

Além dos [pré-requisitos acima](#prerequisites), você precisará de permissões para criar/editar grupos em seu locatário do Azure Active Directory. Se não tiver permissões, você poderá precisar entrar em contato com o administrador do Azure Active Directory.

Se você pretender usar o PowerShell, também precisará do [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Criar um grupo do Azure Active Directory

Crie um grupo do Azure Active Directory usando o comando [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) da CLI do Azure ou o cmdlet [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) do Azure PowerShell.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Em ambos os casos, anote a GroupId dos grupos recém-criados, pois você precisará dela para as etapas abaixo.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Encontrar as objectIds dos aplicativos e dos usuários

Encontre as objectIds dos aplicativos usando a CLI do Azure com o comando [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list), com o parâmetro `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Encontre as objectIds dos aplicativos usando o Azure PowerShell com o cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0), passando uma cadeia de pesquisa para o parâmetro `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Para encontrar as objectIds dos usuários, siga as etapas da seção [Usuários](#users).

### <a name="add-your-applications-and-users-to-the-group"></a>Adicionar os aplicativos e os usuários ao grupo

Agora, adicione as objectIds ao grupo recém-criado do Azure AD.

Com a CLI do Azure, use [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), passando a objectId para o parâmetro `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Com o Azure PowerShell, use o cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0), passando a objectId para o parâmetro `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Fornecer ao grupo do AD o acesso ao cofre de chaves

Por fim, conceda ao grupo do AD permissões no cofre de chaves usando o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) da CLI do Azure ou o cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) do Azure PowerShell. Para obter exemplos, confira a seção [Fornecer ao aplicativo, ao grupo do Azure AD ou ao usuário o acesso ao cofre de chaves](#give-the-principal-access-to-your-key-vault) acima.


## <a name="next-steps"></a>Próximas etapas

- [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](overview-security.md#identity-and-access-management)
- [Fornecer a autenticação do Key Vault com uma identidade gerenciada do Serviço de Aplicativo](managed-identity.md)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Proteger o cofre de chaves](key-vault-secure-your-key-vault.md).
- [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](key-vault-best-practices.md)
