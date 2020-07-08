---
title: Gerenciar uma conta Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 66fb5741a5ed1af9e7edf002485c959f9f2fc82f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507255"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerenciar uma conta Executar como da Automação do Azure

As contas Executar como na Automação do Azure são usadas para fornecer autenticação para o gerenciamento de recursos no Azure com os cmdlets do Azure. Quando você cria uma conta Executar como, ela cria uma nova entidade de serviço no Azure Active Directory (AD) e atribui a função Colaborador a esse usuário no nível da assinatura.

## <a name="types-of-run-as-accounts"></a>Tipos de contas Executar como

A automação do Azure usa dois tipos de contas Executar como:

* Conta Executar como do Azure
* Conta Executar como Clássica do Azure

>[!NOTE]
>As assinaturas do CSP (Provedor de Soluções de Nuvem) do Azure são compatíveis apenas com o modelo do Azure Resource Manager. Serviços que não pertencem ao Azure Resource Manager não estão disponíveis no programa. Quando você estiver usando uma assinatura do CSP, a conta Executar como Clássica do Azure não será criada, mas a conta Executar como do Azure será criada. Para saber mais sobre assinaturas de CSP, consulte [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

A entidade de serviço para uma conta Executar como não tem permissões para ler o Azure AD por padrão. Se desejar adicionar permissões para ler ou gerenciar o Azure AD, você precisará conceder as permissões na entidade de serviço em **permissões de API**. Para saber mais, consulte [Adicionar permissões para acessar APIs Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Conta Executar como

A conta Executar como gerencia recursos do [Modelo de implantação do Resource Manager](../azure-resource-manager/management/deployment-models.md). Ela segue as seguintes etapas.

* Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de Colaborador à conta na assinatura atual. Você pode alterar a configuração de certificado para Proprietário ou qualquer outra função. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
  
* Cria um ativo de certificado de Automação chamado `AzureRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.
  
* Cria um ativo de conexão de Automação chamado `AzureRunAsConnection` na conta de Automação especificada. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado.

### <a name="azure-classic-run-as-account"></a>Conta Executar como do Azure Clássico

A conta Executar como Clássica do Azure gerencia recursos do [Modelo de implantação clássico](../azure-resource-manager/management/deployment-models.md). Você deve ser um coadministrador na assinatura para criar ou renovar esse tipo de conta.

A conta Executar como Clássica do Azure executa as seguintes tarefas.

  * Configurar um certificado de gerenciamento na assinatura.

  * Cria um ativo de certificado de Automação chamado `AzureClassicRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.

  * Cria um ativo de conexão de Automação chamado `AzureClassicRunAsConnection` na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, a ID da assinatura e o nome do ativo de certificado.

>[!NOTE]
>A conta Executar como Clássica do Azure não é criada por padrão ao mesmo tempo em que você cria uma conta de automação. Essa conta é criada individualmente seguindo as etapas descritas posteriormente neste artigo.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Obter as permissões da conta Executar como

Esta seção define permissões para contas Executar como regulares e contas Executar como Clássicas.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, é necessário ter privilégios e permissões específicos. Um administrador de aplicativos no Azure Active Directory e um proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Função Desenvolvedor de Aplicativos<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Atribuir ou obter a função RBAC para a entidade especificada|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador ou Proprietário de acesso do usuário ou ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Home > assinaturas > \<subscription name\> -controle de acesso (iam)|
|Criar ou remover um certificado de Automação|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Colaborador no Grupo de Recursos         |Grupo de Recursos da Conta de Automação|
|Criar ou remover uma conexão de Automação|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Colaborador no Grupo de Recursos |Grupo de Recursos da Conta de Automação|

<sup>1</sup> Os usuários não administradores em seu locatário do Azure AD poderão [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se a opção **Os usuários podem registrar aplicativos** do locatário do Azure AD na página Configurações do usuário estiver definida como **Sim**. Se a configuração de registro do aplicativo for **Não**, o usuário que executa essa ação deverá ser conforme definido nesta tabela.

Se você não for um membro da instância do Active Directory da assinatura antes de ser adicionado à função de Administrador Global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um `You do not have permissions to create…` aviso na página **adicionar conta de automação** .

Se você for um membro da instância de Active Directory da assinatura quando a função de administrador global for atribuída, você também poderá receber um `You do not have permissions to create…` aviso na página **adicionar conta de automação** . Nesse caso, você pode solicitar a remoção da instância do Active Directory da assinatura e solicitar que ela seja adicionada novamente, para que você se torne um usuário completo no Active Directory.

Para verificar se a situação que produz a mensagem de erro foi remediada:

1. No painel do Azure Active Directory no portal do Azure, selecione **Usuários e grupos**.
2. Selecione **Todos os usuários**.
3. Escolha seu nome e, em seguida, selecione **Perfil**. 
4. Verifique se o valor do atributo **Tipo de usuário** no perfil do usuário não está definido como **Convidado**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Obter permissões para configurar contas Executar como Clássicas

Para configurar ou renovar contas Executar como Clássicas, você deve ter a função de coadministrador no nível de assinatura. Para saber mais sobre as permissões de assinatura clássicas, consulte [Administradores de assinatura do Azure Clássico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Criar uma conta Executar como no portal do Azure

Execute as seguintes etapas para atualizar sua conta de Automação do Azure no portal do Azure. Criar as contas Executar como e Executar como Clássicas individualmente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.

1. Faça logon no portal do Azure com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

2. Pesquise e escolha **Contas de Automação**.

3. Na página Contas de Automação, escolha sua conta de Automação na lista.

4. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.

5. Dependendo da conta de que você precisa, selecione **Conta Executar como do Azure** ou **Conta Executar como Clássica do Azure**. 

6. Dependendo da conta de interesse, use o painel **Adicionar Executar como do Azure** ou **Adicionar a conta Executar como Clássica do Azure**. Depois de examinar as informações de visão geral, clique em **Criar**.

7. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. O processo pode levar alguns minutos.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica

Esta seção descreve como excluir uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Após excluir uma conta, você pode recriá-la no portal do Azure.

1. No Portal do Azure, abra a Conta de automação.

2. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.

3. Na página de propriedades de Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. 

4. No painel Propriedades da conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

6. Depois que a conta for excluída, você poderá recriá-la na página de propriedades Contas Executar como selecionando a opção de criação **Executar como Conta do Azure**.

   ![Recriar a conta de Automação Executar como](media/manage-runas-account/automation-account-create-runas.png)

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

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script de[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Esse script altera a entidade de serviço da conta Executar como existente para criar e usar uma definição de função personalizada. A função tem permissões para todos os recursos, exceto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

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

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Você também pode determinar a definição de função usada pelas contas Executar como para várias assinaturas ou contas de automação. Faça isso usando o script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a um Key Vault

Você pode permitir que a Automação do Azure verifique se Key Vault e sua entidade de serviço da conta Executar como estão usando uma definição de função personalizada. Você deve:

* Conceder permissões a um Key Vault.
* Definir a política de acesso.

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para conceder permissões de conta Executar como para Key Vault. Consulte [Conceder aos aplicativos acesso a um key vault](../key-vault/general/group-permissions-for-apps.md) para obter mais detalhes sobre como definir permissões no Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração incorreta para contas Executar como

Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. As possíveis instâncias de configuração incorreta incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Para outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades Contas Executar como para a conta.

![Status incompleto de configuração de conta Executar como](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando você selecionar a conta Executar como, o painel Propriedades da conta exibirá a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximas etapas

* [Objetos de aplicativo e de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* [Visão geral sobre certificados para os Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
