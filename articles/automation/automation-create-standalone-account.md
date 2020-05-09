---
title: Como criar conta autônoma de automação do Azure
description: Este artigo orienta você pelas etapas de criação, teste e uso de uma autenticação de entidade de segurança de exemplo na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 5f291bdea2df67e07b1aca2dcc6326c3c9864ad2
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995790"
---
# <a name="create-a-standalone-azure-automation-account"></a>Como criar conta autônoma de automação do Azure

Este artigo mostra como criar uma conta da Automação do Azure no portal do Azure. Você pode usar a conta de automação do portal para avaliar e saber mais sobre a automação sem usar recursos de gerenciamento adicionais ou a integração com os logs de Azure Monitor. Você pode adicionar recursos de gerenciamento ou integrar com Azure Monitor logs para monitoramento avançado de trabalhos de runbook em qualquer momento no futuro.

Com uma conta de Automação, você pode autenticar runbooks gerenciando recursos no Azure Resource Manager ou no modelo de implantação clássico. Uma Conta de Automação pode gerenciar recursos em todas as regiões e assinaturas para determinado locatário.

Quando você cria uma conta de automação no portal do Azure, a conta **Executar como** é criada automaticamente. Essa conta executa as seguintes tarefas:

* Cria uma entidade de serviço no Azure AD (Azure Active Directory).
* Cria um certificado.
* Atribui o RBAC (Controle de Acesso Baseado em Função) de Colaborador, que gerencia os recursos do Azure Resource Manager por meio de runbooks.

Com essa conta criada para você, você pode começar rapidamente a criar e implantar runbooks para dar suporte às suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta de Automação e concluir as tarefas descritas neste artigo, é necessário ter os seguintes privilégios e permissões:

* Para criar uma conta de automação, sua conta de usuário do Azure AD deve ser adicionada a uma função com permissões equivalentes à `Microsoft.Automation` função de proprietário para recursos. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
* Na portal do Azure, em **Azure Active Directory** > **gerenciar** > **configurações de usuário**, se **registros de aplicativo** estiver definido como **Sim**, os usuários não administradores em seu locatário do Azure ad poderão [registrar Active Directory aplicativos](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **Registros do aplicativo** estiver definido como **Não**, o usuário que executar esta ação precisará ser um administrador global no Microsoft Azure AD.

Se você não for um membro da instância de Active Directory da assinatura antes de ser adicionado à função Administrador Global/coadministrador da assinatura, será adicionado ao Active Directory como convidado. Nesse cenário, você verá esta mensagem no painel Adicionar conta de automação:`You do not have permissions to create.`

Se um usuário for adicionado à função de administrador global/coadministrador primeiro, você poderá remover o usuário da instância de Active Directory da assinatura. Você pode ler o usuário para a função de usuário no Active Directory.

Para verificar as funções de usuário:

1. No portal do Azure, acesse o painel Azure Active Directory.
1. Selecione **Usuários e grupos**.
1. Selecione **todos os usuários**.
1. Depois de selecionar um usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** no perfil do usuário não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de Automação no portal do Azure

Para criar uma conta da Automação do Azure no portal do Azure, execute as seguintes etapas:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores da assinatura e um coadministrador da assinatura.
1. Selecione **+ Criar um Recurso**.
1. Pesquise **Automação**. Nos resultados da pesquisa, selecione **Automação**.

   ![Pesquisar e selecionar Automação e Controle no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na próxima tela, selecione **criar novo**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se você receber a mensagem a seguir no painel Adicionar Conta de Automação, a sua conta não é membro da função Administradores da assinatura nem um coadministrator da assinatura.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-without-perms.png)

1. No painel Adicionar conta de automação, insira um nome para a nova conta de automação no campo **nome** . Você não pode alterar esse nome depois que ele é escolhido. 

    > [!NOTE]
    > Os nomes da conta de Automação são exclusivos por região e grupo de recursos. Os nomes das contas de automação excluídas podem não estar imediatamente disponíveis.

1. Se você tiver mais de uma assinatura, use o campo **assinatura** para especificar a assinatura a ser usada para a nova conta.
1. Para **Grupo de recursos**, insira ou selecione um grupo de recursos novo ou existente.
1. Para **Local**, selecione um local de datacenter do Azure.
1. Para a opção **criar conta Executar como do Azure** , verifique se **Sim** está selecionado e, em seguida, clique em **criar**.

   > [!NOTE]
   > Se você optar por não criar a conta Executar como selecionando **Não** em **Criar conta Executar como do Azure**, será exibida uma mensagem no painel Adicionar Conta de Automação. Embora a conta seja criada no portal do Azure, ela não tem uma identidade de autenticação correspondente em sua assinatura do modelo de implantação clássico ou no serviço de diretório da assinatura do Azure Resource Manager. Portanto, a conta de Automação não tem acesso aos recursos em sua assinatura. Isso impede que runbooks que referenciam essa conta possam se autenticar e executar tarefas nos recursos nesses modelos de implantação.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.
   >

1. Para acompanhar o progresso da criação da conta de automação, selecione **notificações** no menu.

### <a name="resources-included"></a>Recursos incluídos

Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. Após a criação, esses runbooks poderão ser excluídos com segurança se você não quiser mantê-los. As contas Executar como poderão ser usadas para autenticar a conta em um runbook e deverão ser mantidas, exceto se você criar outra ou não forem mais necessárias. A tabela a seguir resume os recursos para a conta Executar como.

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript |Um runbook de exemplo do PowerShell que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de exemplo do Python que demonstra como fazer a autenticação usando a conta Executar como. O runbook lista todos os grupos de recursos presentes na assinatura. |
| AzureRunAsCertificate |Um ativo de certificado criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. O certificado é autenticado no Azure, do modo que você possa gerenciar os recursos do Azure Resource Manager em runbooks. Esse certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de conexão criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. |

## <a name="create-a-classic-run-as-account"></a>Criar uma conta Executar como clássica

As contas Executar como clássicas não são mais criadas por padrão quando você cria uma conta de automação do Azure. Se você ainda precisar de uma conta Executar como clássica:

1. Na sua conta de automação, selecione **contas Executar como** em **configurações da conta**.
2. Selecione **conta Executar como clássica do Azure**.
3. Clique em **criar** para prosseguir com a criação da conta Executar como clássica.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação gráfica, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, consulte [meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar com runbooks do Python 2, consulte [meu primeiro runbook do Python2](automation-first-runbook-textual-python2.md).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
