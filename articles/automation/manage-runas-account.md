---
title: Gerenciar uma conta Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767422"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerenciar uma conta Executar como da Automação do Azure

As contas Executar como na automação do Azure fornecem autenticação para gerenciar recursos no Azure Resource Manager ou no modelo de implantação clássico do Azure usando runbooks de automação e outros recursos de automação. Este artigo fornece orientação sobre como gerenciar uma conta Executar como ou executar como clássica.

Para saber mais sobre a autenticação da conta de automação do Azure e diretrizes relacionadas a cenários de automação de processos, consulte [visão geral da autenticação da conta de automação](automation-security-overview.md).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Permissões da conta Executar como

Esta seção define permissões para contas Executar como regulares e contas Executar como Clássicas.

Para criar ou atualizar uma conta Executar como, é necessário ter privilégios e permissões específicos. Um administrador de aplicativos no Azure Active Directory e um proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Função Desenvolvedor de Aplicativos<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Atribuir ou obter a função do Azure para a entidade de segurança especificada|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador ou Proprietário de acesso do usuário ou ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Home > assinaturas > \<subscription name\> -controle de acesso (iam)|
|Criar ou remover um certificado de Automação|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Colaborador no Grupo de Recursos         |Grupo de Recursos da Conta de Automação|
|Criar ou remover uma conexão de Automação|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Colaborador no Grupo de Recursos |Grupo de Recursos da Conta de Automação|

<sup>1</sup> Os usuários não administradores em seu locatário do Azure AD poderão [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se a opção **Os usuários podem registrar aplicativos** do locatário do Azure AD na página Configurações do usuário estiver definida como **Sim**. Se a configuração de registro do aplicativo for **Não**, o usuário que executa essa ação deverá ser conforme definido nesta tabela.

Se você não for um membro da instância do Active Directory da assinatura antes de ser adicionado à função de Administrador Global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um `You do not have permissions to create…` aviso na página **adicionar conta de automação** .

Se você for um membro da instância de Active Directory da assinatura em que a função de administrador global for atribuída, você também poderá receber um `You do not have permissions to create…` aviso na página **adicionar conta de automação** . Nesse caso, você pode solicitar a remoção da instância do Active Directory da assinatura e solicitar que ela seja adicionada novamente, para que você se torne um usuário completo no Active Directory.

Para verificar se a situação que produz a mensagem de erro foi remediada:

1. No painel do Azure Active Directory no portal do Azure, selecione **Usuários e grupos**.
2. Selecione **Todos os usuários**.
3. Escolha seu nome e, em seguida, selecione **Perfil**.
4. Verifique se o valor do atributo **Tipo de usuário** no perfil do usuário não está definido como **Convidado**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Permissões necessárias para criar ou gerenciar contas Executar como clássicas

Para configurar ou renovar contas Executar como Clássicas, você deve ter a função de coadministrador no nível de assinatura. Para saber mais sobre as permissões de assinatura clássicas, consulte [Administradores de assinatura do Azure Clássico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Criar uma conta Executar como no portal do Azure

Execute as seguintes etapas para atualizar sua conta de Automação do Azure no portal do Azure. Criar as contas Executar como e Executar como Clássicas individualmente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.

1. Faça logon no portal do Azure com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

2. Pesquise e escolha **Contas de Automação**.

3. Na página Contas de Automação, escolha sua conta de Automação na lista.

4. No painel esquerdo, selecione **contas Executar como** na seção **configurações de conta** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Selecione a opção conta Executar como.":::

5. Dependendo da conta que você precisa, use a **conta Executar como do Azure** ou **+ painel da conta Executar como clássica do Azure** . Depois de examinar as informações de visão geral, clique em **Criar**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Selecione a opção conta Executar como.":::

6. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. O processo pode levar alguns minutos.

## <a name="create-a-run-as-account-using-powershell"></a>Criar conta Executar como usando PowerShell

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell usando um script fornecido. Esses requisitos se aplicam a ambos os tipos de contas Executar como.

* Windows 10 ou Windows Server 2016 com os módulos 3.4.1 e posteriores do Azure Resource Manager. O script do PowerShell não é compatível com versões anteriores do Windows.
* Azure PowerShell PowerShell 6.2.4 ou posterior. Para obter informações, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).
* Uma Conta de automação, que é referenciada como o valor para os parâmetros `AutomationAccountName` e `ApplicationDisplayName`.
* Permissões equivalentes às listadas em [Permissões necessárias para configurar contas Executar como](#permissions).

Para obter os valores para `AutomationAccountName` , `SubscriptionId` e `ResourceGroupName` , que são parâmetros necessários para o script do PowerShell, conclua as etapas a seguir.

1. No portal do Azure, selecione **Contas de Automação**.

1. Na página de Contas de Automação, selecione a sua Conta de automação.

1. Na seção de configurações da conta, selecione **Propriedades**.

1. Observe os valores de **nome**, **ID da assinatura**e **grupo de recursos** na página **Propriedades** .

   ![Painel de propriedades da conta de automação](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Criar o script do PowerShell da conta Executar como

O script do PowerShell inclui suporte para várias configurações.

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.
* Criar uma conta Executar como e uma conta Clássica Executar como usando um certificado emitido por sua autoridade de certificação corporativa (CA).
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental.

1. Baixe e salve o script em uma pasta local usando o comando a seguir.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Inicie o PowerShell com direitos de usuário elevados e navegue até a pasta que contém o script.

3. Execute um dos comandos a seguir para criar uma conta Executar como e/ou executar como clássica com base em seus requisitos.

    * Crie uma conta Executar como usando um certificado autoassinado.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Se tiver criado uma conta Executar como Clássica com um certificado público corporativo (arquivo .cer), use este certificado. O script cria e salva-o na pasta arquivos temporários no computador, no perfil do usuário `%USERPROFILE%\AppData\Local\Temp` usado para executar a sessão do PowerShell. Consulte [Carregar um certificado de gerenciamento de API](../cloud-services/cloud-services-configure-ssl-certificate-portal.md) no portal do Azure.

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Depois que o script for executado, você deverá se autenticar no Azure. Entre com uma conta que seja membro da função Administradores de assinatura. Se você estiver criando uma conta Executar como clássica, sua conta deverá ser um coadministrador da assinatura.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica

Esta seção descreve como excluir uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Depois de excluir a conta Executar como, você poderá recriá-la no portal do Azure ou com o script do PowerShell fornecido.

1. No Portal do Azure, abra a Conta de automação.

2. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.

3. Na página de propriedades de Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir.

4. No painel Propriedades da conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovar um certificado autoassinado

O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Antes da conta Executar como expirar, você deverá renovar o certificado. Você pode renová-lo a qualquer momento antes que ele expire.

Ao renovar o certificado autoassinado, o certificado válido atual é retido para garantir que todos os runbooks colocados na fila ou em execução ativa e autenticados com a conta Executar como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

>[!NOTE]
>Se achar que a conta Executar como foi comprometida, você poderá excluir e recriar o certificado autoassinado.

>[!NOTE]
>Se você tiver configurado a conta Executar como para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção para renovar uma opção de certificado autoassinado, o certificado da empresa será substituído por um certificado autoassinado.

Use as etapas a seguir para renovar o certificado autoassinado.

1. No Portal do Azure, abra a Conta de automação.

1. Selecione **Contas Executar como** na seção configurações de conta.

    ![Painel de propriedades da conta de Automação](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página de propriedades Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica para a qual você deseja renovar o certificado.

1. No painel Propriedades da conta selecionada, clique em **Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="limit-run-as-account-permissions"></a>Limitar as permissões da conta Executar como

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script de[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Esse script altera a entidade de serviço da conta Executar como existente para criar e usar uma definição de função personalizada. A função tem permissões para todos os recursos, exceto [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1**, os runbooks que acessam Key Vault por meio do uso de contas Executar como não funcionam mais. Antes de executar o script, você deve examinar os runbooks em sua conta para chamadas para Azure Key Vault. Para habilitar o acesso a Key Vault de runbooks de automação do Azure, você deve [adicionar a conta Executar como a permissões de Key Vault](#add-permissions-to-key-vault).

Se você precisar restringir, além do que a entidade de serviço executar como pode fazer, poderá adicionar outros tipos de recursos ao elemento `NotActions` da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute/*`. Se você adicionar esse tipo de recurso a `NotActions` para a definição de função, a função não poderá acessar nenhum recurso de computação. Para obter mais informações sobre definições de função, confira [Noções básicas sobre definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Você pode determinar se a entidade de serviço usada pela sua conta Executar como está na definição da função colaborador ou em uma personalizada.

1. Acesse sua conta de Automação e selecione **Contas Executar como** na seção de configurações de conta.
2. Selecione **Conta Executar como do Azure**.
3. Selecione **Função** para localizar a definição de função que está sendo usada.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Selecione a opção conta Executar como." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Você também pode determinar a definição de função usada pelas contas Executar como para várias assinaturas ou contas de automação. Faça isso usando o script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a um Key Vault

Você pode permitir que a Automação do Azure verifique se Key Vault e sua entidade de serviço da conta Executar como estão usando uma definição de função personalizada. Você deve:

* Conceder permissões a um Key Vault.
* Definir a política de acesso.

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para conceder permissões de conta Executar como para Key Vault. Consulte [atribuir uma política de acesso Key Vault](/azure/key-vault/general/assign-access-policy-powershell) para obter mais detalhes sobre como definir permissões em Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração incorreta para contas Executar como

Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. As possíveis instâncias de configuração incorreta incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Para outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades Contas Executar como para a conta.

![Status incompleto de configuração de conta Executar como](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Quando você selecionar a conta Executar como, o painel Propriedades da conta exibirá a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta Executar como.

## <a name="next-steps"></a>Próximas etapas

* [Objetos de aplicativo e de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* [Visão geral sobre certificados para os Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
