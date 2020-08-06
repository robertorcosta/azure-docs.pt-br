---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure AD
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04b0b0120a514591fb4fa23656906203acf443b4
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799495"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory

Neste artigo, você verá como atribuir usuários ou grupos a aplicativos empresariais no Azure AD (Azure Active Directory) no portal do Azure ou no PowerShell. Quando você atribui um usuário a um aplicativo, esse aplicativo aparece no [painel de acesso Meus Aplicativos](https://myapps.microsoft.com/) do usuário para facilitar o acesso. Se o aplicativo expõe funções, você também pode atribuir uma função específica ao usuário.

Para maior controle, determinados tipos de aplicativos empresariais podem ser configurados para [exigir a atribuição de usuário](#configure-an-application-to-require-user-assignment). 

Para [atribuir um usuário ou grupo a um aplicativo empresarial](#assign-users-or-groups-to-an-app-via-the-azure-portal), você precisará entrar como administrador global, administrador de aplicativos, administrador de aplicativos de nuvem ou proprietário atribuído do aplicativo empresarial.

> [!IMPORTANT]
> Quando você atribui um grupo a um aplicativo, somente os usuários no grupo terão acesso. A atribuição não faz a cascata para grupos aninhados.

> [!NOTE]
> A atribuição baseada em grupo exige o Azure Active Directory Premium edição P1 ou P2. A atribuição baseada em grupo é compatível somente com os grupos de segurança. Atualmente, não há compatibilidade com associações de grupo aninhado e grupos do Office 365. Para ver mais requisitos de licenciamento dos recursos discutidos neste artigo, confira a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configurar um aplicativo para exigir a atribuição de usuários

Com os seguintes tipos de aplicativos, você tem a opção de exigir que os usuários sejam atribuídos ao aplicativo antes que eles possam acessá-lo:

- Aplicativos configurados para SSO (logon único) federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam a pré-autenticação do Azure Active Directory
- Aplicativos criados na plataforma de aplicativos do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador autoriza o aplicativo.

Quando a atribuição de usuário é necessária, somente os usuários que você atribui explicitamente ao aplicativo (por meio da atribuição de usuário direta ou com base na associação de grupo) poderão entrar. Eles podem acessar o aplicativo na página Meus Aplicativos ou usando um link direto. 

Quando a atribuição *não é necessária*, seja porque você definiu essa opção como **Não** ou porque o aplicativo usa outro modo SSO, qualquer usuário poderá acessar o aplicativo se tiver um link direto para ele ou a **URL de acesso do usuário** na página **Propriedades** do aplicativo. 

Essa configuração não afeta se um aplicativo é exibido ou não no painel de acesso Meus Aplicativos. Os aplicativos aparecem nos painéis de acesso Meus Aplicativos dos usuários depois que você atribuiu um usuário ou grupo ao aplicativo. Para saber mais, confira [Gerenciar o acesso a aplicativos](what-is-access-management.md).


Para exigir a atribuição de usuários a um aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador ou proprietário do aplicativo.

2. Selecione **Azure Active Directory**. No menu de navegação esquerdo, selecione **Aplicativos empresariais**.

3. Selecione o aplicativo na lista. Se você não encontrar o aplicativo, comece a digitar o nome dele na caixa de pesquisa. Outra opção é usar os controles de filtro para selecionar o tipo, o status ou a visibilidade do aplicativo e, em seguida, selecionar **Aplicar**.

4. No menu de navegação esquerdo, selecione **Propriedades**.

5. Defina o botão de alternância **Atribuição de usuário necessária?** para **Sim**.

   > [!NOTE]
   > Se o botão de alternância **Atribuição de usuário necessária?** não estiver disponível, use o PowerShell para definir a propriedade appRoleAssignmentRequired na entidade de serviço.

6. Clique no botão **Salvar** na parte superior da tela.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Atribuir usuários ou grupos a um aplicativo no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador global, administrador de aplicativo, administrador de aplicativos de nuvem ou proprietário atribuído do aplicativo empresarial.
2. Selecione **Azure Active Directory**. No menu de navegação esquerdo, selecione **Aplicativos empresariais**.
3. Selecione o aplicativo na lista. Se você não encontrar o aplicativo, comece a digitar o nome dele na caixa de pesquisa. Outra opção é usar os controles de filtro para selecionar o tipo, o status ou a visibilidade do aplicativo e, em seguida, selecionar **Aplicar**.
4. No menu de navegação esquerdo, selecione **Usuários e grupos**.
   > [!NOTE]
   > Se você quiser atribuir usuários aos Aplicativos da Microsoft, como os do Office 365, alguns deles usam o PowerShell. 
5. Selecione o botão **Adicionar usuário**.
6. No painel **Adicionar Atribuição**, selecione **Usuários e grupos**.
7. Selecione o usuário ou grupo que você deseja atribuir ao aplicativo ou comece a digitar o nome do usuário ou grupo na caixa de pesquisa. É possível escolher vários usuários e grupos. As seleções aparecerão em **Itens selecionados**.
8. Ao terminar, clique em **Selecionar**.

   ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)

9. No painel **Usuários e grupos**, selecione um ou mais usuários ou grupos na lista. Em seguida, pressione o botão **Selecionar** na parte inferior do painel.
10. Se o aplicativo for compatível, você poderá atribuir uma função ao usuário ou grupo. Na painel **Adicionar Atribuição**, escolha **Selecionar Função**. Em seguida, no painel **Selecionar Função**, escolha uma função para aplicar aos usuários ou grupos selecionados e clique no botão **OK** na parte inferior do painel. 

    > [!NOTE]
    > Se o aplicativo não for compatível com a seleção de função, a função de acesso padrão será atribuída. Nesse caso, o aplicativo gerencia o nível de acesso dos usuários.

2. No painel **Adicionar Atribuição**, clique no botão **Atribuir** na parte inferior do painel.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Atribuir usuários ou grupos a um aplicativo no PowerShell

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

Para saber mais sobre como atribuir um usuário a uma função de aplicativo, confira a documentação sobre [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Para atribuir um grupo a um aplicativo empresarial, é preciso substituir `Get-AzureADUser` por `Get-AzureADGroup` e `New-AzureADUserAppRoleAssignment` por `New-AzureADGroupAppRoleAssignment`.

Para saber mais sobre como atribuir um grupo a uma função de aplicativo, confira a documentação sobre [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

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

   ![Mostra as funções disponíveis para um usuário que usa a função de análise](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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
- [Gerenciamento do acesso aos aplicativos](what-is-access-management.md)
 
## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
- [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
