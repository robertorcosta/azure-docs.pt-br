---
title: Gerenciando permissões de usuário e administrador-Azure Active Directory | Microsoft Docs
description: Saiba como examinar e gerenciar permissões para o aplicativo no Azure AD. Por exemplo, se você quiser revogar todas as permissões concedidas a um aplicativo.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277616"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Agir em um aplicativo com privilégios totais ou suspeito no Azure Active Directory

Saiba como examinar e gerenciar permissões de aplicativo. Com base no cenário, este artigo fornecerá ações diferentes que você pode executar para proteger seu aplicativo. Isso se aplica a todos os aplicativos que foram adicionados ao seu locatário do Azure Active Directory (Azure AD) por meio do consentimento de usuário ou administrador.

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

A capacidade de executar as ações abaixo exige que você entre como administrador global, administrador de aplicativos ou administrador de aplicativos de nuvem.

Para restringir o acesso aos aplicativos, você precisará exigir a atribuição do usuário e, em seguida, atribuir usuários ou grupos ao aplicativo.  Para obter mais informações, confira [Métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

Você pode acessar o portal do AD do Azure para obter scripts do PowerShell contextuais para executar as ações.
 
1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo para o qual você deseja restringir o acesso.
4. Selecione **Permissões**. Na barra de comandos, selecione **revisar permissões**.

![Examinar permissões](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Quero controlar o acesso a um aplicativo

Recomendamos que você restrinja o acesso a esse aplicativo ativando a configuração de atribuição de usuário.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo para o qual você deseja restringir o acesso.
4. Selecione **Propriedades** e defina a configuração requisito de usuário necessário como Sim.
5. Selecione **usuário e grupos** e remova usuários indesejados atribuídos ao aplicativo.
6. Atribua usuário (s) ou grupo (s) ao aplicativo.

Opcional, você pode remover todos os usuários atribuídos ao aplicativo usando o PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Desejo revogar todas as permissões de um aplicativo

O uso do PowerShell revoga todas as permissões concedidas a este aplicativo.

> [!NOTE]
> A revogação da permissão concedida atual não interromperá os usuários de reconseing para os aplicativos. Se você quiser impedir que os usuários consentim no aplicativo, leia [configurar como os usuários finais](configure-user-consent.md)conferem aos aplicativos.

Opcional, você pode desabilitar o aplicativo para impedir que os usuários acessem o aplicativo e que o aplicativo acesse seus dados.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo para o qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, defina habilitado para que os usuários entrem? para não.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>O aplicativo é suspeito e eu quero investigar

Recomendamos que você restrinja o acesso a esse aplicativo ao ativar a configuração de atribuição de usuário e examinar as permissões que o usuário e os administradores concederam ao aplicativo.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
3. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
5. Selecione o aplicativo para o qual você deseja restringir o acesso.
6. Selecione **Propriedades** e defina a configuração requisito de usuário necessário como Sim.
7. Selecione **permissões** e examine as permissões de administrador e de usuário consentidas.

Opcional, você pode:

- Usando o PowerShell, remova todos os usuários atribuídos para impedir que eles façam logon no aplicativo.
- Usando o PowerShell, invalidar tokens de atualização para usuários que têm acesso ao aplicativo.
- Usando o PowerShell, revogar todas as permissões para este aplicativo
- Desabilite o aplicativo para bloquear o acesso dos usuários e interromper o acesso aos seus dados aos aplicativos.


## <a name="application-is-malicious-and-im-compromised"></a>O aplicativo é mal-intencionado e estou comprometido

Recomendamos que você desabilite o aplicativo para impedir que os usuários acessem o aplicativo e o aplicativo acessem seus dados. Se você excluir o aplicativo em vez disso, os usuários finais poderão se reconcordar com o aplicativo e conceder acesso aos seus dados.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo para o qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie a ID do objeto.

### <a name="powershell-commands"></a>Comandos do PowerShell


Recuperar ID de objeto da entidade de serviço

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo para o qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie a ID do objeto.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Remova todos os usuários atribuídos ao aplicativo.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Revogar permissões concedidas ao aplicativo

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Invalidar tokens de atualização
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Próximas etapas
- [Gerenciar o consentimento para aplicativos e avaliar a solicitação de consentimento](manage-consent-requests.md)
- [Configurar consentimento do usuário](configure-user-consent.md)
- [Configurar fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
