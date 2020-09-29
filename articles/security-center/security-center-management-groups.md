---
title: Obtenha visibilidade de todo o locatário para o Centro de Segurança do Azure | Microsoft Docs
description: Este artigo explica como gerenciar sua postura de segurança em escala aplicando políticas a todas as assinaturas vinculadas ao seu locatário de Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 6bbc38d79f51ba4ffcc3795718d276a7e9c0bf03
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440775"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Obtenha visibilidade de todo o locatário para o Centro de Segurança do Azure
Este artigo explica como gerenciar a postura de segurança de sua organização em escala aplicando políticas de segurança a todas as assinaturas do Azure vinculadas ao seu locatário de Azure Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupos de gerenciamento
Os grupos de gerenciamento do Azure fornecem a capacidade de gerenciar com eficiência o acesso, as políticas e os relatórios sobre grupos de assinaturas, bem como gerenciar com eficácia toda a propriedade do Azure executando ações no grupo de gerenciamento raiz. Cada locatário Azure AD recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento “raiz”. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo permite que políticas globais e atribuições de função do Azure sejam aplicadas no nível do diretório. 

O grupo de gerenciamento raiz é criado automaticamente quando você faz qualquer uma das seguintes ações: 
1. Opta por usar os grupos de gerenciamento do Azure navegando para **Grupos de Gerenciamento** no [Portal do Azure](https://portal.azure.com).
2. Cria um grupo de gerenciamento via uma chamada API.
3. Cria um grupo de gerenciamento com PowerShell.

Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md).

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um Grupo de gerenciamento no Portal do Azure
Você organiza assinaturas em grupos de gerenciamento e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. Embora os grupos de gerenciamento não precisem integrar a Central de Segurança, é altamente recomendável criar pelo menos um grupo de gerenciamento para que o grupo de gerenciamento raiz seja criado. Depois que o grupo é criado, todas as assinaturas sob o seu locatário Azure AD serão conectadas a ele. Para obter instruções sobre o PowerShell e mais informações, consulte [Criar grupos de gerenciamento para gerenciamento de recursos e organização](../azure-resource-manager/management-groups-create.md).

 
1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Grupos de gerenciamento**.
3. Na página principal, selecione **Novo grupo de gerenciamento.** 

    ![Grupo Principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo de ID do grupo de gerenciamento. 
    - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. 
    - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **Salvar**

### <a name="view-management-groups-in-the-azure-portal"></a>Exibir grupos de gerenciamento no Portal do Azure
1. Entre no [portal do Azure](https://portal.azure.com).
2. Para visualizar grupos de gerenciamento, selecione **Todos serviços** sob o menu principal do Azure.
3. Sob **Geral**, selecione **Grupos de Gerenciamento**.

    ![Crie um grupo de gerenciamento](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Conceda visibilidade em nível de locatário e a capacidade de atribuir políticas

Para obter visibilidade da postura de segurança de todas as assinaturas registradas no locatário do Azure AD, é necessário atribuir uma função do Azure com permissões de leitura suficientes no grupo de gerenciamento raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso de um Administrador global no Azure Active Directory
Um administrador de locatário do Azure Active Directory não tem acesso direto a assinaturas do Azure. No entanto, como administrador de diretório, eles têm o direito de se elevar a uma função que tenha acesso. Um administrador de locatário do Azure AD precisa se elevar ao administrador de acesso do usuário no nível do grupo de gerenciamento raiz para que ele possa atribuir funções do Azure. Para obter instruções e informações adicionais do PowerShell, consulte [Elevar o acesso de um administrador global no Active Directory do Azure](../role-based-access-control/elevate-access-global-admin.md). 


1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

   ![Azure Active Directory – captura de tela](./media/security-center-management-groups/aad-properties.png)

3. Em **Gerenciamento de acesso para recursos do Azure**, defina a opção para **Sim**.

   ![Gerenciamento de acesso para recursos do Azure - captura de tela](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando você define a opção para Sim, recebe a função de Administrador do Acesso do Usuário no RBAC do Azure no escopo raiz (/). Isso concede a você permissão para atribuir funções a todas as assinaturas e grupos de gerenciamento do Azure associados a esse diretório do AD do Azure. Essa opção está disponível apenas para usuários com a função de administrador global no Azure AD.

   - Quando você define a opção para Não, a função Administrador de Acesso do Usuário no RBAC do Azure é removida da sua conta de usuário. Você não pode mais atribuir funções a todas as assinaturas e grupos de gerenciamento do Azure associados a esse diretório do AD do Azure. Você pode exibir e gerenciar somente as assinaturas do Azure e os grupos de gerenciamento aos quais você recebeu acesso.

4. Clique em **Salvar**, para salvar suas configurações.

    - Essa configuração não é uma propriedade global, aplicando-se somente ao usuário conectado no momento.

5. Execute as tarefas que você precisa fazer com o acesso elevado. Ao terminar, retorne a opção para **Não**.


### <a name="assign-azure-roles-to-users"></a>Atribuir funções do Azure aos usuários
Para obter visibilidade de todas as assinaturas, os administradores de locatários precisam atribuir a função apropriada do Azure a todos os usuários que desejarem conceder visibilidade em todo o locatário, incluindo-se, no nível do grupo de gerenciamento raiz. As funções recomendadas para atribuir são **Admin de Segurança** ou **Leitor de Segurança**. Geralmente, a função Admin de Segurança é necessária para aplicar políticas no nível raiz, enquanto Leitor de Segurança será suficiente para fornecer visibilidade no nível dos locatários. Para obter mais informações sobre as permissões concedidas por essas funções, consulte a [descrição da função interna Admin de Segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a [descrição da função interna Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Atribua funções do Azure aos usuários por meio do portal do Azure: 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Para exibir grupos de gerenciamento, selecione **Todos os serviços** no menu principal do Azure e, em seguida, selecione **Grupos de Gerenciamento**.
1.  Selecione um grupo de gerenciamento e clique em **detalhes**.

    ![Captura de tela dos detalhes dos Grupos de Gerenciamento](./media/security-center-management-groups/management-group-details.PNG)
 
1. Clique **Controle de acesso (IAM)**, em seguida, **Atribuição de função**.

1. Clique em **Adicionar atribuição de função**.

1. Selecione a função a ser atribuída e o usuário e, em seguida, clique em **Salvar**.  
   
   ![Captura de tela da função Adicionar Leitor de Segurança](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Atribuir funções do Azure a usuários com o PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os comandos a seguir: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando solicitado, entre com suas credenciais. 

    ![Logue em screenshot promt](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de função de leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, use o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Abra ou atualize o Centro de Segurança
Após elevar o acesso, abra ou atualize a Central de Segurança do Azure para verificar se você tem visibilidade de todas as assinaturas do locatário do Azure AD. 

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Certifique-se de selecionar todas as assinaturas no seletor de assinatura que você gostaria de exibir na Central de Segurança.

    ![Captura de tela do seletor de assinatura](./media/security-center-management-groups/subscription-selector.png)

1. Selecione **Todos serviços** sob o menu principal do Azure e então selecione **Centro de Segurança**.
2. Na **Visão geral**, há um gráfico de cobertura de assinatura.

    ![Screenshot tabela de cobertura de assinatura](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Clique em **Cobertura** para ver a lista de assinaturas cobertas. 

    ![Screenshot lista de cobertura de assinatura](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Remover acesso elevado 
Depois que as funções do Azure tiverem sido atribuídas aos usuários, o administrador de locatários deverá se remover da função de administrador de acesso do usuário.

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

3. Em **Gerenciamento de acesso para recursos do Azure**, defina a opção como **não**.

4. Clique em **Salvar**, para salvar suas configurações.



## <a name="adding-subscriptions-to-a-management-group"></a>Adicionando assinaturas a um grupo de gerenciamento
Você consegue adicionar assinaturas a um grupo de gerenciamento que você criou. Estes passos não são obrigatórios para ganhar visibilidade a nível de locatário e política global e gerenciamento de acesso.

1. Em **Grupos de Gerenciamento**, selecione um grupo de gerenciamento para adicionar sua assinatura.

    ![Selecione um grupo de gerenciamento e adicione uma assinatura](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecione **Adicionar existente**.

    ![Adicione existente](./media/security-center-management-groups/add-existing.png)

3. Digite a assinatura em **Adicionar recurso existente** e clique em **Salvar**.

4. Repita etapas 1 a 3 até você ter adicionado todas as assinaturas no escopo.

   > [!NOTE]
   > Grupos de gerenciamento podem conter ambos assinaturas e grupos de gerenciamento criança. Quando você atribui um usuário a uma função do Azure ao grupo de gerenciamento pai, o acesso é herdado pelas assinaturas do grupo de gerenciamento filho. Conjunto de políticas no grupo de gerenciamento pai são também herdadas pela criança. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como ganhar visibilidade a nível locatário para Centro de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira estes artigos:

> [!div class="nextstepaction"]
> [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
