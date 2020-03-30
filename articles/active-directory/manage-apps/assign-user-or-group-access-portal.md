---
title: Atribuir um usuário ou grupo a um aplicativo corporativo no Azure AD
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409185"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory

Este artigo mostra como atribuir usuários ou grupos a aplicativos corporativos no Azure Active Directory (Azure AD), seja dentro do portal Azure ou usando o PowerShell. Quando você atribui um usuário a um aplicativo, o aplicativo aparece no painel de [acesso Meus Aplicativos](https://myapps.microsoft.com/) do usuário para fácil acesso. Se o aplicativo expor funções, você também poderá atribuir uma função específica ao usuário.

Para maior controle, certos tipos de aplicativos corporativos podem ser configurados para exigir a [atribuição do usuário](#configure-an-application-to-require-user-assignment). 

Para [atribuir um usuário ou grupo a um aplicativo corporativo,](#assign-users-or-groups-to-an-app-via-the-azure-portal)você precisará fazer login como administrador global, administrador de aplicativos, administrador de aplicativos na nuvem ou o proprietário designado do aplicativo corporativo.

> [!NOTE]
> A atribuição baseada em grupo requer a edição Premium P1 ou P2 do Azure Active Directory. A atribuição baseada em grupo é suportada apenas para grupos de segurança. Membros de grupos aninhados e grupos do Office 365 não são atualmente apoiados. Para obter mais requisitos de licenciamento para os recursos discutidos neste artigo, consulte a página de preços do Diretório Ativo do [Azure](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configure um aplicativo para exigir a atribuição do usuário

Com os seguintes tipos de aplicativos, você tem a opção de exigir que os usuários sejam atribuídos ao aplicativo antes que eles possam acessá-lo:

- Aplicativos configurados para o SSO (Single Signon, inscrição única federada) com autenticação baseada em SAML
- Aplicativos proxy de aplicativos que usam pré-autenticação do Diretório Ativo do Azure
- Aplicativos construídos na plataforma de aplicativos Azure AD que usam OAuth 2.0 / OpenID Connect Authentication após um usuário ou administrador ter consentido com esse aplicativo.

Quando a atribuição do usuário for necessária, somente os usuários que você atribuir explicitamente ao aplicativo poderão fazer login. Eles podem acessar o aplicativo em sua página Meus Aplicativos ou usando um link direto. 

Quando a atribuição não é *necessária,* seja porque você definiu essa opção como **Não** ou porque o aplicativo usa outro modo SSO, qualquer usuário poderá acessar o aplicativo se tiver um link direto para o aplicativo ou a **URL de acesso** do usuário na página **Propriedades** do aplicativo. 

Essa configuração não afeta se um aplicativo aparece ou não no painel de acesso Meus Aplicativos. Os aplicativos aparecem nos painéis de acesso meus aplicativos dos usuários depois de ter atribuído um usuário ou grupo ao aplicativo. Em busca de antecedentes, consulte [Gerenciando o acesso a aplicativos](what-is-access-management.md).


Para exigir a atribuição do usuário para um aplicativo:

1. Faça login no [portal do Azure](https://portal.azure.com) com uma conta de administrador ou como proprietário do aplicativo.

2. Selecione **O Diretório Ativo do Azure**. No menu de navegação à esquerda, selecione **aplicativos Enterprise**.

3. Selecione o aplicativo na lista. Se você não ver o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles do filtro para selecionar o tipo de aplicativo, status ou visibilidade e, em seguida, **selecione Aplicar**.

4. No menu de navegação à esquerda, selecione **Propriedades**.

5. Certifique-se de que a **atribuição de usuário necessária?** Alternar está definido como **Sim**.

   > [!NOTE]
   > Se a **atribuição de Usuário necessária?** Alternância não estiver disponível, você poderá usar o PowerShell para definir a propriedade appRoleAssignmentRequired no principal do serviço.

6. Selecione o botão **Salvar** na parte superior da tela.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Atribuir usuários ou grupos a um aplicativo através do portal Azure

1. Faça login no [portal do Azure](https://portal.azure.com) com um administrador global, administrador de aplicativos ou conta administradora de aplicativos na nuvem ou como o proprietário designado do aplicativo corporativo.
2. Selecione **O Diretório Ativo do Azure**. No menu de navegação à esquerda, selecione **aplicativos Enterprise**.
3. Selecione o aplicativo na lista. Se você não ver o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles do filtro para selecionar o tipo de aplicativo, status ou visibilidade e, em seguida, **selecione Aplicar**.
4. No menu de navegação à esquerda, selecione **Usuários e grupos**.
   > [!NOTE]
   > Se você quiser atribuir usuários a aplicativos Microsoft, como aplicativos do Office 365, alguns desses aplicativos usam o PowerShell. 
5. Selecione o botão **Adicionar usuário.**
6. No **painel Adicionar atribuição,** selecione **Usuários e grupos**.
7. Selecione o usuário ou grupo que deseja atribuir ao aplicativo ou comece a digitar o nome do usuário ou grupo na caixa de pesquisa. Você pode escolher vários usuários e grupos, e suas seleções serão exibidas em **itens selecionados**.
8. Quando terminar, clique **em Selecionar**.

   ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)

9. No painel **Usuários e grupos,** selecione um ou mais usuários ou grupos da lista e escolha o botão **Selecionar** na parte inferior do painel.
10. Se o aplicativo o suportar, você pode atribuir uma função ao usuário ou grupo. No painel **Adicionar atribuição,** escolha **Selecionar função**. Em seguida, no painel **Selecionar função,** escolha uma função para aplicar aos usuários ou grupos selecionados e selecione **OK** na parte inferior do painel. 

    > [!NOTE]
    > Se o aplicativo não suportar a seleção de função, a função de acesso padrão será atribuída. Neste caso, o aplicativo gerencia o nível de acesso que os usuários têm.

2. No painel **Adicionar atribuição,** selecione o botão **Atribuir** na parte inferior do painel.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Atribuir usuários ou grupos a um aplicativo via PowerShell

1. Abra um prompt elevado do Windows PowerShell.

   > [!NOTE]
   > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.

1. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
1. Use o script abaixo para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Para saber mais sobre como atribuir um usuário a uma função de aplicativo, consulte a documentação sobre [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Para atribuir um grupo a um aplicativo empresarial, é preciso substituir `Get-AzureADUser` por `Get-AzureADGroup`.

### <a name="example"></a>Exemplo

Este exemplo atribui a usuária Brenda Fernandes ao aplicativo [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) usando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, app_name $ e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Neste exemplo, não sabemos o nome exato da função de aplicativo que desejamos atribuir à Brenda Fernandes. Execute os comandos a seguir para obter o usuário ($user) e a entidade de serviço ($sp) usando o nome UPN do usuário e o nome de exibição da entidade de serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Execute o comando `$sp.AppRoles` para exibir as funções disponíveis para o aplicativo Workplace Analytics. Neste exemplo, queremos atribuir a Brenda Fernandes a função de Analista (acesso limitado).

   ![Mostra as funções disponíveis para um usuário usando a função de análise do local de trabalho](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Atribua o nome de função à variável `$app_role_name`.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Execute o seguinte comando para atribuir o usuário à função de aplicativo:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Artigos relacionados

- [Saiba mais sobre o acesso do usuário final aos aplicativos](end-user-experiences.md)
- [Planeje a implantação de um painel de acesso Ad do Azure](access-panel-deployment-plan.md)
- [Gerenciamento de acesso a aplicativos](what-is-access-management.md)
 
## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
- [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
