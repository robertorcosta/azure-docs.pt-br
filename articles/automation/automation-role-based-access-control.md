---
title: Controle de acesso com base em função na Automação do Azure
description: O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Esse artigo descreve como configurar o RBAC na Automação do Azure.
keywords: rbac de automação, controle de acesso baseado em função, rbac azure
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: a49f2596df91c44deafa1be83483f8972e223742
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535563"
---
# <a name="role-based-access-control-in-azure-automation"></a>Controle de acesso com base em função na Automação do Azure

O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Com o [RBAC](../role-based-access-control/overview.md), você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários, os grupos e os aplicativos precisam para realizar seus trabalhos. Você pode conceder acesso baseado em função aos usuários usando o portal do Azure, as ferramentas de linha de comando do Azure ou as APIs de gerenciamento do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="roles-in-automation-accounts"></a>Funções em Contas de Automação

Na Automação do Azure, o acesso é concedido atribuindo a função apropriada de RBAC aos usuários, grupos e aplicativos no escopo da Conta de Automação. Veja a seguir as funções internas com suporte de uma Conta de Automação:

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função Proprietário permite acesso a todos os recursos e ações em uma Conta de Automação, incluindo o fornecimento de acesso a outros usuários, grupos e aplicativos para gerenciar a conta de Automação. |
| Colaborador |A função Colaborador permite gerenciar tudo, exceto a modificação de permissões de acesso de outros usuários para uma conta de Automação. |
| Leitor |A função Leitor permite que você veja todos os recursos em uma conta de Automação, mas não permite realizar alterações. |
| Operador de automação |A função Operador de Automação permite exibir o nome e as propriedades do runbook e criar e gerenciar trabalhos para todos os runbooks em uma conta de Automação. Essa função será útil se você quiser proteger seus recursos de conta de automação, como ativos de credenciais e runbooks, de serem exibidos ou modificados, mas ainda permitir que os membros da sua organização executem esses runbooks. |
|Operador do Trabalho de Automação|A função Operador do Trabalho de Automação permite criar e gerenciar trabalhos para todos os runbooks em uma conta de Automação.|
|Operador de Runbook de Automação|A função Operador de Runbook de Automação permite visualizar o nome e as propriedades de um runbook.|
| Colaborador do Log Analytics | A função Colaborador de Log Analytics permite que você leia todos os dados de monitoramento e edite as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure.|
| Leitor do Log Analytics | A função de Leitor do Log Analytics permite exibir e pesquisar todos os dados de monitoramento, além de exibir configurações de monitoramento. Isso inclui a exibição das configurações de diagnóstico do Azure em todos os recursos do Azure. |
| Colaborador de monitoramento | A função Colaborador de Monitoramento permite que você leia todos os dados de monitoramento e atualize as configurações de monitoramento.|
| Leitor de monitoramento | A função Leitor de monitoramento permite que você leia todos os dados de monitoramento. |
| Administrador de Acesso do Usuário |A função Administrador de Acesso do Usuário permite que você gerencie o acesso dos usuários às Contas de Automação do Azure. |

## <a name="role-permissions"></a>Permissões de função

As tabelas a seguir descrevem as permissões específicas fornecidas a cada função. Isso pode incluir Ações, que concedem permissões, e Não Ações, que as restringem.

### <a name="owner"></a>Proprietário

O Proprietário pode gerenciar tudo, incluindo o acesso. A tabela a seguir mostra as permissões concedidas para a função:

|Ações|Descrição|
|---|---|
|Microsoft.Automation/automationAccounts/|Crie e gerencie recursos de todos os tipos.|

### <a name="contributor"></a>Colaborador

Um Colaborador pode gerenciar tudo, exceto o acesso. A tabela a seguir mostra as permissões concedidas e negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Criar e gerenciar recursos de todos os tipos|
|**Não Ações**||
|Microsoft.Authorization/*/Delete| Exclua funções e atribuições de função.       |
|Microsoft.Authorization/*/Write     |  Crie funções e atribuições de função.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um Administrador de Acesso do Usuário.       |

### <a name="reader"></a>Leitor

Um Leitor pode exibir todos os recursos em uma conta de Automação, mas não pode realizar nenhuma alteração.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Exiba todos os recursos em uma Conta de automação. |

### <a name="automation-operator"></a>Operador de automação

Um Operador de Automação é capaz de criar e gerenciar trabalhos e ler nomes de runbook e propriedades para todos os runbooks em uma conta de Automação.  Observação: se você quiser controlar o acesso do operador a runbooks individuais, não defina essa função e, em vez disso, use as funções "Operador do Trabalho de Automação" e "Operador de Runbook de Automação" combinadas. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorização de leitura.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Leia os recursos do Hybrid Runbook Worker.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os Fluxos e Saída de Trabalho.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Obter a Saída de um trabalho.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pause um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie trabalhos.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Obter uma agenda de trabalho da Automação do Azure.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Criar uma agenda de trabalho da Automação do Azure.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Obtenha o espaço de trabalho vinculado à conta de automação.|
|Microsoft.Automation/automationAccounts/read|Obter uma conta da Automação do Azure.|
|Microsoft.Automation/automationAccounts/runbooks/read|Obter um runbook da Automação do Azure.|
|Microsoft.Automation/automationAccounts/schedules/read|Obter um ativo do agendamento da Automação do Azure.|
|Microsoft.Automation/automationAccounts/schedules/write|Criar ou atualizar um ativo de agendamento da Automação do Azure.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Crie e gerencie tíquetes de suporte.|

### <a name="automation-job-operator"></a>Operador do Trabalho de Automação

Uma função Operador do Trabalho de Automação é concedida no escopo da conta de Automação.Isso permite que as permissões do operador criem e gerenciem trabalhos para todos os runbooks na conta. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorização de leitura.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os Fluxos e Saída de Trabalho.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pause um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie trabalhos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Leia funções e atribuições de função.       |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Crie e gerencie tíquetes de suporte.|

### <a name="automation-runbook-operator"></a>Operador de Runbook de Automação

Uma função Operador de Runbook de Automação é concedida no escopo do Runbook. Um Operador de Runbook de Automação pode exibir o nome e as propriedades do runbook.Essa função combinada com a função 'Operador de Trabalho de Automação' permite que o operador também crie e gerencie trabalhos para o runbook. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Liste os runbooks.        |
|Microsoft.Authorization/*/read      | Autorização de leitura.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      | Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/*      | Crie e gerencie tíquetes de suporte.        |

### <a name="log-analytics-contributor"></a>Colaborador do Log Analytics

Uma função Colaborador de Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automationAccounts/*|Gerenciar contas de automação.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crie e gerencie extensões de escala da máquina virtual.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Liste chaves de conta de armazenamento clássica.|
|Microsoft.Compute/virtualMachines/extensions/*|Crie e gerencie extensões de escala da máquina virtual clássicas.|
|Microsoft.Insights/alertRules/*|Leitura/gravação/exclusão de regras de alerta.|
|Microsoft.Insights/diagnosticSettings/*|Leitura/gravação/exclusão de configurações de diagnóstico.|
|Microsoft.OperationalInsights/*|Gerenciar logs de Azure Monitor.|
|Microsoft.OperationsManagement/*|Gerencie soluções em workspaces.|
|Microsoft.Resources/deployments/*|Crie e gerencie implantações do grupo de recursos.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crie e gerencie implantações do grupo de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Liste chaves da conta de armazenamento.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas ou negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gerenciar consultas em logs de Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar Azure Monitor dados de log.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|
|**Não Ações**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não é possível ler as chaves de acesso compartilhadas.|

### <a name="monitoring-contributor"></a>Colaborador de monitoramento

Uma função Colaborador de Monitoramento pode ler todos os dados de monitoramento e atualizar as configurações de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsManagement/alerts/*|Gerencie alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Gerencie o Painel de alertas.|
|Microsoft.Insights/AlertRules/*|Gerencie as regras de alerta.|
|Microsoft.Insights/components/*|Gerencie os componentes do Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gerencie as configurações de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades.|
|Microsoft.Insights/LogDefinitions/*|Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades.|
|Microsoft.Insights/MetricDefinitions/*|Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Ler as métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registre o provedor do Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gerencie os testes da Web do Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerenciar Azure Monitor os pacotes de solução de logs.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gerenciar Azure Monitor logs de pesquisas salvas.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquise workspaces do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listar chaves para um espaço de trabalho do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerenciar Azure Monitor registra as configurações de insights de armazenamento.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|
|Microsoft.WorkloadMonitor/workloads/*|Gerencie cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de monitoramento

Um Leitor de Monitoramento pode ler todos os dados de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquise workspaces do Log Analytics.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

### <a name="user-access-administrator"></a>Administrador de Acesso do Usuário

Um Administrador de Acesso do Usuário pode gerenciar o acesso do usuário aos recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler todos os recursos|
|Microsoft.Authorization/*|Gerenciar autorização|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

## <a name="onboarding-permissions"></a>Permissões de integração

As seções a seguir descrevem as permissões mínimas necessárias para a integração de máquinas virtuais para o controle de alterações ou soluções de gerenciamento de atualizações.

### <a name="permissions-for-onboarding-from-a-vm"></a>Permissões para integração de uma VM

|**Ação**  |**Permissão**  |**Escopo mínimo**  |
|---------|---------|---------|
|Gravar nova implantação      | Microsoft.Resources/deployments/*          |Assinatura          |
|Gravar novo grupo de recursos      | Microsoft.Resources/subscriptions/resourceGroups/write        | Assinatura          |
|Criar novo workspace padrão      | Microsoft.OperationalInsights/workspaces/write         | Resource group         |
|Criar nova conta      |  Microsoft.Automation/automationAccounts/write        |Resource group         |
|Vincular workspace e conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Workspace</br>Conta de automação
|Criar extensão MMA      | Microsoft.Compute/virtualMachines/write         | Máquina Virtual         |
|Criar pesquisa salva      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Criar configuração de escopo      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Verificação do estado da integração – Ler workspace      | Microsoft.OperationalInsights/workspaces/read         | Workspace         |
|Verificação do estado da integração – Ler propriedade de conta do workspace vinculado     | Microsoft.Automation/automationAccounts/read      | Conta de automação        |
|Verificação do estado da integração – Ler solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação do estado da integração – Ler VM      | Microsoft.Compute/virtualMachines/read         | Máquina Virtual         |
|Verificação do estado da integração – Ler conta      | Microsoft.Automation/automationAccounts/read  |  Conta de automação   |
| Verificação de espaço de trabalho de integração para a VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Assinatura         |
| Registrar o provedor de Log Analytics |Microsoft. insights/registro/ação | Assinatura|

<sup>1</sup> essa permissão é necessária para integração por meio da experiência do portal de VM.

### <a name="permissions-for-onboarding-from-automation-account"></a>Permissões para integração da conta de automação

|**Ação**  |**Permissão** |**Escopo mínimo**  |
|---------|---------|---------|
|Criar nova implantação     | Microsoft.Resources/deployments/*        | Assinatura         |
|Criar novo grupo de recursos     | Microsoft.Resources/subscriptions/resourceGroups/write         | Assinatura        |
|Folha AutomationOnboarding – Criar novo workspace     |Microsoft.OperationalInsights/workspaces/write           | Resource group        |
|Folha AutomationOnboarding – Ler workspace vinculado     | Microsoft.Automation/automationAccounts/read        | Conta de automação       |
|Folha AutomationOnboarding – Ler solução     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|Folha AutomationOnboarding – Ler workspace     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Workspace        |
|Criar link para o workspace e conta     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Gravar conta para caixa de sapatos      | Microsoft.Automation/automationAccounts/write        | Conta        |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Criar/editar configuração de escopo     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
| Registrar o provedor de Log Analytics |Microsoft. insights/registro/ação | Assinatura|
|**Etapa 2 – Integrar várias VMs**     |         |         |
|Folha de VMOnboarding – Criar extensão MMA     | Microsoft.Compute/virtualMachines/write           | Máquina Virtual        |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write           | Workspace        |
|Criar/editar configuração de escopo  | Microsoft.OperationalInsights/workspaces/write   | Workspace|

## <a name="update-management-permissions"></a>Permissões de gerenciamento de atualizações

Gerenciamento de atualizações atinge vários serviços para fornecer seu serviço. A tabela a seguir mostra as permissões necessárias para gerenciar implantações de atualização de gerenciamento:

|**Recurso**  |**Função**  |**Escopo**  |
|---------|---------|---------|
|Conta de automação     | Colaborador do Log Analytics       | Conta de automação        |
|Conta de automação    | Colaborador de Máquina Virtual        | Grupo de recursos para a conta        |
|Espaço de trabalho do Log Analytics     | Colaborador do Log Analytics| Espaço de trabalho do Log Analytics        |
|Espaço de trabalho do Log Analytics |Leitor do Log Analytics| Assinatura|
|Solução     |Colaborador do Log Analytics         | Solução|
|Máquina Virtual     | Colaborador de Máquina Virtual        | Máquina Virtual        |

## <a name="configure-rbac-for-your-automation-account"></a>Configurar o RBAC para sua conta de automação

A seção a seguir mostra como configurar o RBAC em sua conta de automação por meio do [portal do Azure](#configure-rbac-using-the-azure-portal) e do [PowerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Configurar o RBAC usando o portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com/) e abra sua conta de Automação na página Contas de Automação.
2. Clique em **controle de acesso (iam)** para abrir a página controle de acesso (iam). Você pode usar essa página para adicionar novos usuários, grupos e aplicativos para gerenciar sua conta de automação e exibir as funções existentes que são configuráveis para a conta de automação.
3. Clique na guia **Atribuições de função**.

   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo usuário e atribuir uma função

1. Na página controle de acesso (IAM), clique em **+ Adicionar atribuição de função**. Essa ação abre a página Adicionar atribuição de função, na qual é possível adicionar um usuário, grupo ou aplicativo e atribuir uma função correspondente.

2. Selecione uma função na lista de funções disponíveis. É possível escolher qualquer uma das funções internas disponíveis compatíveis com uma Conta de Automação ou qualquer função personalizada que você tenha definido.

3. Digite o nome do usuário ao qual você deseja conceder permissões no campo **selecionar** . Escolha o usuário na lista e clique em **salvar**.

   ![Adicionar usuários](media/automation-role-based-access-control/automation-04-add-users.png)

   Agora você deve ver o usuário adicionado à página usuários, com a função selecionada atribuída.

   ![Listar usuários](media/automation-role-based-access-control/automation-05-list-users.png)

   Você também pode atribuir uma função para o usuário na página Funções .
4. Clique em **Funções** na página Controle de acesso (IAM) para abrir a página Funções. Você pode exibir o nome da função e o número de usuários e grupos atribuídos a essa função.

    ![Atribuir função na página de usuários](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Você só pode definir o controle de acesso baseado em função no escopo da conta de automação e não em nenhum recurso abaixo da conta de automação.

#### <a name="remove-a-user"></a>Remover um usuário

Você pode remover a permissão de acesso de um usuário que não está gerenciando a Conta de Automação ou que não trabalha mais para a organização. Veja abaixo as etapas para remover um usuário:

1. Na página controle de acesso (IAM), selecione o usuário a ser removido e clique em **remover**.
2. Clique no botão **Remover** na página de detalhes da atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover usuários](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configurar o RBAC usando o PowerShell

Você também pode configurar o acesso baseado em função a uma conta de automação usando os seguintes [cmdlets Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleDefinition?view=azps-3.7.0) lista todas as funções RBAC que estão disponíveis no Azure Active Directory. Você pode usar esse cmdlet com o `Name` parâmetro para listar todas as ações que uma função específica pode executar.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

A seguir está a saída de exemplo:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0) lista as atribuições de função do RBAC do Azure AD no escopo especificado. Sem nenhum parâmetro, esse cmdlet retorna todas as atribuições de função feitas na assinatura. Use o `ExpandPrincipalGroups` parâmetro para listar as atribuições de acesso para o usuário especificado, bem como os grupos aos quais o usuário pertence.

**Exemplo:** Use o cmdlet a seguir para listar todos os usuários e suas funções em uma conta de automação.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A seguir está a saída de exemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Use [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzRoleAssignment?view=azps-3.7.0) para atribuir acesso a usuários, grupos e aplicativos a um escopo específico.
    
**Exemplo:** use o comando a seguir para atribuir a função "Operador de Automação" para um usuário no escopo da Conta de Automação.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A seguir está a saída de exemplo:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Use [Remove-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzRoleAssignment?view=azps-3.7.0) para remover o acesso de um usuário, grupo ou aplicativo especificado de um escopo específico.

**Exemplo:** Use o comando a seguir para remover o usuário da função de operador de automação no escopo da conta de automação.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

No exemplo anterior, `sign-in ID of a user you wish to remove`substitua, `SubscriptionID` `Resource Group Name`, e `Automation account name` pelos detalhes da sua conta. Escolha **Sim** quando for solicitado a confirmar antes de continuar a remover as atribuições de função de usuário.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Experiência do usuário para função do operador de automação – conta de automação

Quando um usuário atribuído à função de operador de automação no escopo da conta de automação exibir a conta de automação à qual ela está atribuída, o usuário poderá exibir apenas a lista de runbooks, trabalhos de runbook e agendamentos criados na conta de automação. Este usuário não pode exibir as definições desses itens. O usuário pode iniciar, parar, suspender, retomar ou agendar o trabalho de runbook. No entanto, o usuário não tem acesso a outros recursos de automação, como configurações, grupos de trabalho híbrido ou nós DSC.

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configurar o RBAC para runbooks

A automação do Azure permite que você atribua o RBAC a runbooks específicos. Para fazer isso, execute o script a seguir para adicionar um usuário a um runbook específico. Um administrador de conta de automação ou um Administrador de Locatários pode executar esse script.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Depois que o script tiver sido executado, peça ao usuário que faça logon no portal do Azure e selecione **todos os recursos**. Na lista, o usuário pode ver o runbook para o qual ele foi adicionado como um operador de runbook de automação.

![Runbook RBAC no portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Experiência do usuário para a função de operador de Automação – runbook

Quando um usuário atribuído à função de operador de automação no escopo do runbook exibe um runbook atribuído, o usuário só pode iniciar o runbook e exibir os trabalhos de runbook.

![Só tem acesso ao iniciar](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre como configurar o RBAC para a automação do Azure, consulte [gerenciar RBAC com Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Para obter detalhes de maneiras de iniciar um runbook, consulte [iniciando um runbook](automation-starting-a-runbook.md).
* Para obter informações sobre tipos de runbook, consulte [tipos de runbook de automação do Azure](automation-runbook-types.md).