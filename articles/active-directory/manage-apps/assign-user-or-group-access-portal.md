---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409185"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory

Este artigo mostra como atribuir usuários ou grupos a aplicativos empresariais no Azure Active Directory (Azure AD), seja de dentro do portal do Azure ou usando o PowerShell. Quando você atribui um usuário a um aplicativo, o aplicativo é exibido no painel de [acesso meus aplicativos](https://myapps.microsoft.com/) do usuário para facilitar o acesso. Se o aplicativo expõe funções, você também pode atribuir uma função específica ao usuário.

Para maior controle, determinados tipos de aplicativos empresariais podem ser configurados para [exigir atribuição de usuário](#configure-an-application-to-require-user-assignment). 

Para [atribuir um usuário ou grupo a um aplicativo empresarial](#assign-users-or-groups-to-an-app-via-the-azure-portal), você precisará entrar como um administrador global, administrador de aplicativos, administrador de aplicativos de nuvem ou o proprietário atribuído do aplicativo empresarial.

> [!NOTE]
> A atribuição baseada em grupo requer a edição Azure Active Directory Premium P1 ou P2. A atribuição baseada em grupo tem suporte apenas para grupos de segurança. Atualmente, não há suporte para associações de grupo aninhado e grupos do Office 365. Para obter mais requisitos de licenciamento para os recursos discutidos neste artigo, consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configurar um aplicativo para exigir atribuição de usuário

Com os seguintes tipos de aplicativos, você tem a opção de exigir que os usuários sejam atribuídos ao aplicativo antes que possam acessá-lo:

- Aplicativos configurados para SSO (logon único) federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam Azure Active Directory pré-autenticação
- Aplicativos criados na plataforma de aplicativo do Azure AD que usam autenticação OAuth 2,0/OpenID Connect depois que um usuário ou administrador consentiu nesse aplicativo.

Quando a atribuição de usuário é necessária, somente os usuários que você atribui explicitamente ao aplicativo poderão entrar. Eles podem acessar o aplicativo na página meus aplicativos ou usando um link direto. 

Quando a atribuição *não é necessária*, porque você definiu essa opção como **não** ou porque o aplicativo usa outro modo de SSO, qualquer usuário poderá acessar o aplicativo se ele tiver um link direto para o aplicativo ou a URL de **acesso do usuário** na página de **Propriedades** do aplicativo. 

Essa configuração não afeta se um aplicativo aparece ou não no painel de acesso meus aplicativos. Os aplicativos aparecem nos painéis de acesso dos meus aplicativos dos usuários depois que você atribuiu um usuário ou grupo ao aplicativo. Para obter informações, consulte [Managing Access to apps](what-is-access-management.md).


Para exigir a atribuição de usuário para um aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador ou como um proprietário do aplicativo.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicativos empresariais**.

3. Selecione o aplicativo na lista. Se você não vir o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles de filtro para selecionar o tipo de aplicativo, o status ou a visibilidade e, em seguida, selecione **aplicar**.

4. No menu de navegação à esquerda, selecione **Propriedades**.

5. Verifique se a **atribuição de usuário é necessária?** a alternância está definida como **Sim**.

   > [!NOTE]
   > Se a **atribuição de usuário for necessária?** alternar não está disponível, você pode usar o PowerShell para definir a propriedade appRoleAssignmentRequired na entidade de serviço.

6. Selecione o botão **salvar** na parte superior da tela.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Atribuir usuários ou grupos a um aplicativo por meio do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador global, administrador de aplicativos ou administrador de aplicativos de nuvem ou como o proprietário atribuído do aplicativo empresarial.
2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicativos empresariais**.
3. Selecione o aplicativo na lista. Se você não vir o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles de filtro para selecionar o tipo de aplicativo, o status ou a visibilidade e, em seguida, selecione **aplicar**.
4. No menu de navegação à esquerda, selecione **usuários e grupos**.
   > [!NOTE]
   > Se você quiser atribuir usuários a aplicativos da Microsoft, como aplicativos do Office 365, alguns desses aplicativos usarão o PowerShell. 
5. Selecione o botão **Adicionar usuário** .
6. No painel **Adicionar atribuição** , selecione **usuários e grupos**.
7. Selecione o usuário ou grupo que você deseja atribuir ao aplicativo ou comece a digitar o nome do usuário ou grupo na caixa de pesquisa. Você pode escolher vários usuários e grupos e suas seleções aparecerão em **itens selecionados**.
8. Quando terminar, clique em **selecionar**.

   ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)

9. No painel **usuários e grupos** , selecione um ou mais usuários ou grupos na lista e, em seguida, escolha o botão **selecionar** na parte inferior do painel.
10. Se o aplicativo der suporte a ele, você poderá atribuir uma função ao usuário ou ao grupo. No painel **Adicionar atribuição** , escolha **selecionar função**. Em seguida, no painel **selecionar função** , escolha uma função a ser aplicada aos usuários ou grupos selecionados e, em seguida, selecione **OK** na parte inferior do painel. 

    > [!NOTE]
    > Se o aplicativo não oferecer suporte à seleção de função, a função de acesso padrão será atribuída. Nesse caso, o aplicativo gerencia o nível de acesso que os usuários têm.

2. No painel **Adicionar atribuição** , selecione o botão **atribuir** na parte inferior do painel.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Atribuir usuários ou grupos a um aplicativo por meio do PowerShell

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

   ![Mostra as funções disponíveis para um usuário usando a função de análise de local de trabalho](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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

- [Saiba mais sobre o acesso do usuário final a aplicativos](end-user-experiences.md)
- [Planejar uma implantação do painel de acesso do Azure AD](access-panel-deployment-plan.md)
- [Gerenciando o acesso a aplicativos](what-is-access-management.md)
 
## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
- [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
