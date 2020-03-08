---
title: Como criar conta autônoma de automação do Azure
description: Este artigo orienta você pelas etapas de criação, teste e uso de uma autenticação de entidade de segurança de exemplo na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 22efb5e94049b975780c6f6ea69aa94a71cc9992
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373161"
---
# <a name="create-a-standalone-azure-automation-account"></a>Como criar conta autônoma de automação do Azure

Este artigo mostra como criar uma conta da Automação do Azure no portal do Azure. Você pode usar a conta de automação do portal para avaliar e saber mais sobre a automação sem usar soluções de gerenciamento adicionais ou integração com logs de Azure Monitor. Você pode adicionar essas soluções de gerenciamento ou integrá-las a logs de Azure Monitor para monitoramento avançado de trabalhos de runbook em qualquer ponto no futuro.

Com uma conta de Automação, você pode autenticar runbooks gerenciando recursos no Azure Resource Manager ou no modelo de implantação clássico. Uma Conta de Automação pode gerenciar recursos em todas as regiões e assinaturas para determinado locatário.

Ao criar uma conta de Automação no portal do Azure, essas contas são criadas automaticamente:

* **Conta Executar como**. Essa conta executa as seguintes tarefas:
  * Cria uma entidade de serviço no Azure AD (Azure Active Directory).
  * Cria um certificado.
  * Atribui o RBAC (Controle de Acesso Baseado em Função) de Colaborador, que gerencia os recursos do Azure Resource Manager por meio de runbooks.

Com essas contas criadas para você, você pode começar rapidamente a criar e implantar runbooks em apoio às suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta de Automação e concluir as tarefas descritas neste artigo, é necessário ter os seguintes privilégios e permissões:

* Para criar uma conta de automação, sua conta de usuário do Azure AD deve ser adicionada a uma função com permissões equivalentes à função de proprietário da **Microsoft.** Recursos de automação. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
* Na portal do Azure, em **Azure Active Directory** > **gerenciar** configurações de **usuário** > , se **registros de aplicativo** estiver definido como **Sim**, os usuários não administradores em seu locatário do Azure ad poderão [registrar Active Directory aplicativos](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **Registros do aplicativo** estiver definido como **Não**, o usuário que executar esta ação precisará ser um administrador global no Microsoft Azure AD.

Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionado à função de administrador global/coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Neste cenário, você recebe esta mensagem na página **Adicionar Conta de Automação**: “Você não tem permissões para criar”.

Se um usuário for adicionado à função de administrador global/coadministrator pela primeira vez, você poderá removê-lo da instância do Active Directory da assinatura e, em seguida, adicioná-lo novamente à função de Usuário completa no Active Directory.

Para verificar as funções de usuário:

1. No portal do Azure, acesse o painel **Azure Active Directory**.
1. Selecione **Usuários e grupos**.
1. Selecione **Todos os usuários**.
1. Depois de selecionar um usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** no perfil do usuário não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de Automação no portal do Azure

Para criar uma conta da Automação do Azure no portal do Azure, execute as seguintes etapas:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores da assinatura e um coadministrador da assinatura.
1. Selecione **+ Criar um Recurso**.
1. Pesquise **Automação**. Nos resultados da pesquisa, selecione **Automação**.

   ![Pesquisar e selecionar Automação e Controle no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na próxima tela, selecione **Criar**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se você receber a mensagem a seguir no painel **Adicionar Conta de Automação**, a sua conta não é membro da função Administradores da assinatura nem um coadministrator da assinatura.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-without-perms.png)

1. No painel **Adicionar Conta de Automação**, na caixa **Nome**, insira um nome para a nova conta de Automação. Esse nome não pode ser alterado depois de ser escolhido. *Os nomes de conta de automação são exclusivos por região e grupo de recursos. Os nomes das contas de automação que foram excluídas podem não estar imediatamente disponíveis.*
1. Se você tem mais de uma assinatura, na caixa **Assinatura**, especifique a assinatura que deseja usar para a nova conta.
1. Para **Grupo de recursos**, insira ou selecione um grupo de recursos novo ou existente.
1. Para **Local**, selecione um local de datacenter do Azure.
1. Para a opção **Criar conta Executar como do Azure**, verifique se a opção **Sim** está marcada e, em seguida, selecione **Criar**.

   > [!NOTE]
   > Se você optar por não criar a conta Executar como selecionando **Não** em **Criar conta Executar como do Azure**, será exibida uma mensagem no painel **Adicionar Conta de Automação**. Embora a conta seja criada no portal do Azure, ela não tem uma identidade de autenticação correspondente em sua assinatura do modelo de implantação clássico ou no serviço de diretório da assinatura do Azure Resource Manager. Portanto, a conta de Automação não tem acesso aos recursos em sua assinatura. Isso impede que runbooks que referenciam essa conta possam se autenticar e executar tarefas nos recursos nesses modelos de implantação.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.
   >

1. Para acompanhar o progresso da criação da conta de Automação, no menu, selecione **Notificações**.

### <a name="resources-included"></a>Recursos incluídos

Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. Após a criação, esses runbooks poderão ser excluídos com segurança se você não quiser mantê-los. As contas Executar como poderão ser usadas para autenticar a conta em um runbook e deverão ser mantidas, exceto se você criar outra ou não forem mais necessárias. A tabela a seguir resume os recursos para a conta Executar como.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript |Um runbook de exemplo do PowerShell que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de exemplo do Python que demonstra como fazer a autenticação usando a conta Executar como. O runbook lista todos os grupos de recursos presentes na assinatura. |
| AzureRunAsCertificate |Um ativo de certificado criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. O certificado é autenticado no Azure, do modo que você possa gerenciar os recursos do Azure Resource Manager em runbooks. Esse certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de conexão criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. |

## <a name="classic-run-as-accounts"></a>Contas Executar como clássicas

As contas Executar como clássicas não são mais criadas, por padrão, quando você cria uma conta de automação do Azure. Se você ainda precisar de uma conta Executar como clássica, execute as etapas a seguir.

1. Na página da sua **conta de automação** , selecione **contas Executar como** em **configurações da conta**.
2. Selecione **conta Executar como clássica do Azure**.
3. Clique em **criar** para prosseguir com a criação da conta Executar como clássica.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação gráfica, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar a usar runbooks Python2, veja [Meu primeiro runbook Python2](automation-first-runbook-textual-python2.md).

