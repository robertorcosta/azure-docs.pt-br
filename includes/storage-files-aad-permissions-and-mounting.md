---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208240"
---
## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para acessar os recursos do Azure Files com autenticação baseada em identidade, uma identidade (um usuário, grupo ou diretor de serviço) deve ter as permissões necessárias no nível de compartilhamento. Esse processo é semelhante à especificação de permissões de compartilhamento do Windows, onde você especifica o tipo de acesso que um usuário em particular tem a um compartilhamento de arquivos. As orientações nesta seção demonstram como atribuir ler, gravar ou excluir as permissões para um compartilhamento de arquivos para uma identidade.

Introduzimos três funções incorporadas do Azure para conceder permissões de nível de compartilhamento aos usuários:

- **O Storage File Data SMB Share Reader** permite o acesso à leitura em compartilhamentos de arquivos do Azure Storage sobre SMB.
- **O Storage File Data SMB Share Contributor** permite ler, gravar e excluir acesso em compartilhamentos de arquivos do Azure Storage sobre SMB.
- **O compartilhamento de dados de arquivo de armazenamento SMB Compartilhar O Contribuinte Elevado** permite ler, gravar, excluir e modificar permissões NTFS em compartilhamentos de arquivos do Azure Storage sobre SMB.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de tomar posse de um arquivo, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD.

Você pode usar o portal Azure, PowerShell ou Azure CLI para atribuir as funções incorporadas à identidade Azure AD de um usuário para conceder permissões de nível de compartilhamento.

> [!NOTE]
> Lembre-se de sincronizar suas credenciais de Anúncio com o Azure AD se você planeja usar seu Anúncio para autenticação. A sincronização de hash de senha do AD para o Azure AD é opcional. A permissão de nível de compartilhamento será concedida à identidade Azure AD que é sincronizada a partir de AD.

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função RBAC a uma identidade AD do Azure, usando o [portal Azure,](https://portal.azure.com)siga estas etapas:

1. No portal Azure, vá para o seu compartilhamento de arquivos ou [Crie um compartilhamento de arquivos](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecione **controle de acesso (IAM)**.
3. Selecione **Adicionar uma atribuição de função**
4. Na **lâmina de atribuição Adicionar função,** selecione a função incorporada apropriada (Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor) na lista **Role.** Deixar **atribuir acesso na** configuração padrão: **Usuário, grupo ou principal de serviço do Azure AD**. Selecione a identidade Azure AD de destino por nome ou endereço de e-mail.
5. Selecione **Salvar** para concluir a operação de atribuição de função.

#### <a name="powershell"></a>PowerShell

A amostra do PowerShell a seguir mostra como atribuir uma função RBAC a uma identidade AD do Azure, com base no nome de login. Para obter mais informações sobre como atribuir funções do RBAC ao PowerShell, consulte [Gerenciar acesso usando o RBAC e o Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Antes de executar o seguinte script de exemplo, lembre-se de substituir os valores do espaço reservado, incluindo suportes, por seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O comando CLI 2.0 a seguir mostra como atribuir uma função RBAC a uma identidade AD do Azure, com base no nome de login. Para obter mais informações sobre como atribuir funções RBAC com o Azure CLI, consulte [Gerenciar o acesso usando o RBAC e o Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Antes de executar o seguinte script de exemplo, lembre-se de substituir os valores do espaço reservado, incluindo suportes, por seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões NTFS em SMB 
Depois de atribuir permissões no nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS corretas no nível raiz, de diretório ou de arquivo. Pense em permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento. Considerando que as permissões NTFS agem em um nível mais granular para determinar quais operações o usuário pode fazer no diretório ou nível de arquivo.

Os arquivos do Azure dá suporte a todo o conjunto de permissões de NTFS básicos e avançados. Você pode visualizar e configurar permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos Do Azure, montando o compartilhamento e, em seguida, usando o Windows File Explorer ou executando o comando Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 

Para configurar o NTFS com permissões de superusuário, você deve montar o compartilhamento usando a chave da conta de armazenamento da VM com um domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos Azure a partir do prompt de comando e para configurar as permissões NTFS de acordo.

Conjuntos de permissões a seguir têm suporte para o diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com icacls
Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar icacls para definir permissões NTFS e sobre os diferentes tipos de permissões suportadas, consulte [a referência de linha de comando para icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do Azure no prompt de comando

Usar o Windows **net use** comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores do espaço reservado no exemplo a seguir por seus próprios valores. Para obter mais informações sobre a montagem de compartilhamentos de arquivos, consulte [Usar um compartilhamento de arquivos Do Zure com o Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configure permissões NTFS com o Windows File Explorer
Use o Windows File Explorer para conceder permissão total a todos os diretórios e arquivos o compartilhamento de arquivos, incluindo o diretório raiz.

1. Abra o Windows File Explorer e clique com o botão direito do mouse no arquivo/diretório e selecione **Propriedades**
2. Clique na guia **Segurança**
3. Clique em **Editar..**. botão para alterar permissões
4. Você pode alterar a permissão dos usuários existentes, ou clicar em **Adicionar...** para conceder permissões a novos usuários
5. Na janela de solicitação para adicionar novos usuários, digite o nome de usuário de destino a que deseja conceder permissão na **Digite os nomes dos objetos para selecionar caixa** e clique em **'Marcar nomes'** para encontrar o nome completo da UPN do usuário-alvo.
7.  Clique em **OK**
8.  Na guia Segurança, selecione todas as permissões que deseja conceder ao usuário recém-adicionado
9.  Clique em **Aplicar**

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar um compartilhamento de arquivos de uma VM ingressada no domínio

O processo a seguir verifica se as permissões de compartilhamento e acesso do arquivo foram configuradas corretamente e que você pode acessar um compartilhamento de arquivos Do Azure a partir de uma VM com um domínio:

Faça login na VM usando a identidade Azure AD à qual você concedeu permissões, conforme mostrado na imagem a seguir. Se você habilitou a autenticação AD para arquivos Azure, use a credencial AD. Para autenticação Azure AD DS, faça login com credencial Azure AD.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Use o seguinte comando para montar o compartilhamento de arquivos Azure. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Como você foi autenticado, você não precisa fornecer a chave da conta de armazenamento, as credenciais do AD ou as credenciais do Azure AD. A experiência de login único é suportada para autenticação com AD ou Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
