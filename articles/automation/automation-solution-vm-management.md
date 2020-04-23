---
title: Solução iniciar/parar VMs fora do horário comercial
description: Essa solução de gerenciamento de VM inicia e interrompe suas máquinas virtuais do Azure em um agendamento e monitora de forma proativa os logs de Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 10c66ba175484d8b95f26ef9330753151a92969b
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106025"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solução iniciar/parar VMs fora do horário comercial na automação do Azure

A solução **iniciar/parar VMs fora do horário comercial** inicia ou interrompe as máquinas virtuais do Azure. Ele inicia ou interrompe computadores em agendas definidas pelo usuário, fornece informações sobre logs de Azure Monitor e envia emails opcionais usando [grupos de ação](../azure-monitor/platform/action-groups.md). A solução dá suporte a VMs Azure Resource Manager e clássicas para a maioria dos cenários. 

Essa solução usa o cmdlet [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) para iniciar as VMs. Ele usa [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) para parar as VMs.

> [!NOTE]
> A solução **iniciar/parar VMs fora do horário comercial** foi atualizada para dar suporte às versões mais recentes dos módulos do Azure que estão disponíveis.

A solução fornece uma opção de automação descentralizada de baixo custo para os usuários que desejam otimizar seus custos de VM. Com essa solução, você pode:

- [Agende as VMs para iniciar e parar](automation-solution-vm-management-config.md#schedule).
- Agende as VMs para iniciar e parar em ordem crescente [usando marcas do Azure](automation-solution-vm-management-config.md#tags) (sem suporte para VMs clássicas).
- Autostop VMs com base no [baixo uso da CPU](automation-solution-vm-management-config.md#cpuutil).

A seguir estão as limitações da solução atual:

- Ele gerencia as VMs em qualquer região, mas só pode ser usado na mesma assinatura que a sua conta de automação do Azure.
- Ele está disponível no Azure e no Azure governamental para qualquer região que dê suporte a um espaço de trabalho Log Analytics, uma conta de automação do Azure e alertas. Atualmente, as regiões do Azure governamental não dão suporte à funcionalidade de email.

## <a name="solution-prerequisites"></a>Pré-requisitos da solução

Os runbooks para esta solução funcionam com uma conta do [Azure Run As](automation-create-runas-account.md). A conta Executar como é o método de autenticação preferencial porque ela usa a autenticação de certificado em vez de uma senha que pode expirar ou ser alterada com frequência.

Recomendamos que você use uma conta de automação separada para a solução **iniciar/parar VMs fora do horário comercial** . As versões de módulo do Azure são atualizadas com frequência e seus parâmetros podem ser alterados. A solução não é atualizada na mesma cadência e pode não funcionar com versões mais recentes dos cmdlets que ele usa. Você é recomendado para testar as atualizações de módulo em uma conta de automação de teste antes de importá-las em suas contas de automação de produção.

## <a name="solution-permissions"></a>Permissões de solução

Você deve ter determinadas permissões para implantar a solução **iniciar/parar VMs fora do horário comercial** . As permissões serão diferentes se a solução usar uma conta de automação criada previamente e Log Analytics espaço de trabalho das permissões necessárias se a solução criar uma nova conta e um espaço de trabalho durante a implantação. 

Você não precisa configurar permissões se for um colaborador na assinatura e um administrador global em seu locatário Azure Active Directory. Se você não tiver esses direitos ou precisar configurar uma função personalizada, certifique-se de ter as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para a conta de automação pré-existente e Log Analytics espaço de trabalho

Para implantar a solução **iniciar/parar VMs fora do horário comercial** em uma conta de automação existente e log Analytics espaço de trabalho, o usuário que está implantando a solução requer as seguintes permissões no escopo do grupo de recursos. Para saber mais sobre as funções, confira [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md).

| Permissão | Escopo|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/variables/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/schedules/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/runbooks/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/connections/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/certificates/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/modules/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/modules/read | Grupo de recursos |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/read | Grupo de recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de recursos |
| Microsoft. OperationalInsights/Workspaces/* | Grupo de recursos |
| Microsoft. insights/diagnosticSettings/Write | Grupo de recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de recursos |
| Microsoft. insights/ActionGroups/Read | Grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupo de recursos |
| Microsoft.Resources/deployments/* | Grupo de recursos |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Permissões para nova conta de automação e novo Log Analytics espaço de trabalho

Você pode implantar a solução **iniciar/parar VMs fora do horário comercial** em uma nova conta de automação e log Analytics espaço de trabalho. Nesse caso, o usuário que está implantando a solução precisa das permissões definidas na seção anterior, bem como as permissões definidas nesta seção. 

O usuário que está implantando a solução precisa das seguintes funções:

- Coadministrador na assinatura. Essa função é necessária para criar a conta Executar como clássica se você pretende gerenciar VMs clássicas. [As contas Executar como clássicas](automation-create-standalone-account.md#create-a-classic-run-as-account) não são mais criadas por padrão.
- Associação na função de desenvolvedor de aplicativo [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Para obter mais informações sobre como configurar contas Executar como, consulte [permissões para configurar contas Executar como](manage-runas-account.md#permissions).
- Colaborador na assinatura ou nas permissões a seguir.

| Permissão |Escopo|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/certificates/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de recursos |

## <a name="solution-components"></a>Componentes da solução

A **solução iniciar/parar VMs fora do horário comercial** inclui runbooks pré-configurados, agendas e integração com logs de Azure monitor. Você pode usar esses elementos para personalizar a inicialização e o desligamento de suas VMs de acordo com suas necessidades de negócios.

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks que a solução implanta em sua conta de automação. Não faça alterações no código do runbook. Em vez disso, escreva seu próprio runbook para obter novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum runbook com **filho** anexado ao seu nome.

Todos os runbooks pai incluem `WhatIf` o parâmetro. Quando definido como true, o parâmetro dá suporte à detalhamento do comportamento exato que o runbook usa quando executado sem o parâmetro e valida que as VMs corretas são direcionadas. Um runbook executa apenas suas ações definidas quando o `WhatIf` parâmetro é definido como false.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook pai. Esse runbook cria alertas por recurso para o cenário de parada automática.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Verdadeiro ou Falso  | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> `VMList`é uma lista separada por vírgulas de VMs. Por exemplo, `vm1, vm2, vm3`.<br> `WhatIf`habilita a validação da lógica do runbook sem execução.|
|AutoStop_Disable | Nenhum | Desabilita os alertas de parada automática e o agendamento padrão.|
|AutoStop_VM_Child | WebHookData | Chamada a partir do runbook pai. As regras de alerta chamam esse runbook para interromper uma VM clássica.|
|AutoStop_VM_Child_ARM | WebHookData |Chamada a partir do runbook pai. As regras de alerta chamam esse runbook para interromper uma VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Executa a ação iniciar ou parar no grupo de VMs clássica por serviços de nuvem. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar para a parada agendada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar para a interrupção agendada para VMs clássicas. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> WhatIf: Verdadeiro ou Falso | Inicia ou para todas as VMs na assinatura. Edite as `External_Start_ResourceGroupNames` variáveis `External_Stop_ResourceGroupNames` e execute somente nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a `External_ExcludeVMNames` variável.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso<br>VMList| Cria marcas chamadas **sequencestart** e **SEQUENCESTOP** em cada VM para a qual você deseja sequenciar a atividade iniciar/parar. Os nomes das tags diferenciam maiúsculas de minúsculas. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem em que você deseja iniciar ou parar. <br>**Observação**: as VMs devem estar dentro dos grupos de `External_Start_ResourceGroupNames`recursos `External_Stop_ResourceGroupNames`definidos nas `External_ExcludeVMNames` variáveis, e. Elas devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>Variáveis

A tabela a seguir lista as variáveis criadas na sua conta da Automação. Modifique apenas variáveis prefixadas `External`com. Modificar variáveis prefixadas `Internal` com causa efeitos indesejáveis.

> [!NOTE]
> As limitações no nome da VM e no grupo de recursos são basicamente um resultado do tamanho da variável. Consulte [ativos de variáveis na automação do Azure](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional exigido para configurar a condição antes de disparar um alerta. Os valores aceitáveis `GreaterThanOrEqual`são `LessThan` `GreaterThan`,, `LessThanOrEqual`e.|
|External_AutoStop_Description | O alerta para parar a VM se o percentual da CPU exceder o limite.|
|External_AutoStop_Frequency | A frequência de avaliação para a regra. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deverá ser configurada.|
|External_AutoStop_Severity | Severidade do alerta de métrica, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limite para a regra de alerta do Azure especificada na `External_AutoStop_MetricName`variável. Os valores percentuais variam de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo aplicado ao tamanho de janela selecionado para avaliar a condição. Os `Average`valores aceitáveis `Minimum`são `Maximum`, `Total`,, `Last`e.|
|External_AutoStop_TimeWindow | O tamanho da janela durante a qual o Azure analisa as métricas selecionadas para disparar um alerta. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas.|
|External_EnableClassicVMs| Valor que especifica se as VMs clássicas são direcionadas pela solução. O valor padrão é True. Defina essa variável como false para assinaturas do CSP (provedor de soluções na nuvem) do Azure. As VMs clássicas exigem uma [conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Lista separada por vírgulas de nomes de VM a serem excluídos, limitada a 140 VMs. Se você adicionar mais de 140 VMs à lista, as VMs definidas como excluídas poderão ser iniciadas ou interrompidas inadvertidamente.|
|External_Start_ResourceGroupNames | Lista separada por vírgulas de um ou mais grupos de recursos que são direcionados para ações de início.|
|External_Stop_ResourceGroupNames | Lista separada por vírgulas de um ou mais grupos de recursos que são direcionados para ações de parada.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam executadas nas VMs para o **SequencedStartStop_Parent** runbook. Essa variável permite que o runbook aguarde as operações filhas por um número especificado de segundos antes de prosseguir com a próxima ação. O tempo de espera máximo é de 10800 ou três horas. O valor padrão é 2100 segundos.|
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|
|Internal_AutoSnooze_ARM_WebhookURI | O URI do webhook chamado para o cenário de autostop para VMs.|
|Internal_AutoSnooze_WebhookUri | O URI do webhook chamado para o cenário de autostop para VMs clássicas.|
|Internal_AzureSubscriptionId | A ID de assinatura do Azure.|
|Internal_ResourceGroupName | O nome do grupo de recursos da conta de automação.|

>[!NOTE]
>Para a variável `External_WaitTimeForVMRetryInSeconds`, o valor padrão foi atualizado de 600 para 2100. 

Em todos os cenários, as `External_Start_ResourceGroupNames`variáveis `External_Stop_ResourceGroupNames`, e `External_ExcludeVMNames` são necessárias para direcionar as VMs, exceto para as listas de VMs separadas por vírgulas para os runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** . Ou seja, suas VMs devem pertencer aos grupos de recursos de destino para que as ações iniciar e parar ocorram. A lógica funciona semelhante à política do Azure, isto é, você pode direcionar a assinatura ou o grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar e parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.É possível modificá-las ou criar suas próprias agendas personalizadas.Por padrão, todas as agendas são desabilitadas, exceto as agendas de **Scheduled_StartVM** e **Scheduled_StopVM** .

Não habilite todos os agendamentos, pois isso pode criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja fazer e modificá-las de acordo. Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o **runbook AutoStop_CreateAlert_Parent** a cada 8 horas, o que, por sua vez, interrompe os `External_Start_ResourceGroupNames`valores `External_Stop_ResourceGroupNames`baseados em `External_ExcludeVMNames` VM nas variáveis, e. Como alternativa, você pode especificar uma lista separada por vírgulas de VMs usando o `VMList` parâmetro.|
|Scheduled_StopVM | Definido pelo usuário, diariamente | Executa o runbook **ScheduledStopStart_Parent** com um parâmetro de `Stop` todos os dias no horário especificado.Interrompe automaticamente todas as VMs que atendem às regras definidas por ativos de variáveis.Habilite o agendamento relacionado Scheduled **-StartVM**.|
|Scheduled_StartVM | Definido pelo usuário, diariamente | Executa o **ScheduledStopStart_Parent** runbook com um valor de parâmetro `Start` de todos os dias no horário especificado. Inicia automaticamente todas as VMs que atendem às regras definidas por ativos variáveis.Habilite o agendamento relacionado Scheduled **-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o **Sequenced_StopStop_Parent** runbook com um valor de parâmetro `Stop` de cada sexta-feira na hora especificada.Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida pelas variáveis adequadas. Para obter mais informações sobre valores de marca e variáveis de ativo, consulte [Runbooks](#runbooks).Habilite o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o **SequencedStopStart_Parent** runbook com um valor de parâmetro `Start` de cada segunda-feira no tempo especificado. Inicia sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida pelas variáveis adequadas. Para obter mais informações sobre valores de marca e ativos de variáveis, consulte [Runbooks](#runbooks). Habilite o agendamento relacionado, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Uso da solução com VMs clássicas

Se você estiver usando a solução **iniciar/parar VMs fora do horário comercial** para VMs clássicas, a automação processará todas as VMs sequencialmente por serviço de nuvem. As VMs ainda são processadas em paralelo em diferentes serviços de nuvem. 

Para usar a solução com VMs clássicas, você precisa de uma conta Executar como clássica, que não é criada por padrão. Para obter instruções sobre como criar uma conta Executar como clássica, consulte [criar uma conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se você tiver mais de 20 VMs por serviço de nuvem, estas são algumas recomendações:

* Crie vários agendamentos com o runbook pai **ScheduledStartStop_Parent** e especifique 20 VMs por agendamento. 
* Nas propriedades da agenda, use o `VMList` parâmetro para especificar nomes de VM como uma lista separada por vírgulas. 

Caso contrário, se o trabalho de automação para essa solução for executado mais de três horas, ele será temporariamente descarregado ou interrompido de acordo com o limite de [compartilhamento justo](automation-runbook-execution.md#fair-share) .

As assinaturas do Azure CSP dão suporte apenas ao modelo de Azure Resource Manager. Os serviços não Azure Resource Manager não estão disponíveis no programa. Quando a **solução iniciar/parar VMs fora do horário comercial** for executada, você poderá receber erros, pois tem cmdlets para gerenciar recursos clássicos. Para saber mais sobre CSP, confira [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se você usar uma assinatura do CSP, defina a variável [External_EnableClassicVMs](#variables) como false após a implantação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Habilitar a solução

Para começar a usar a solução, siga as etapas em [habilitar a solução iniciar/parar VMs](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Exibir a solução

Use um dos mecanismos a seguir para acessar a solução depois de habilitá-la:

* Na sua conta de automação, selecione **iniciar/parar VM** em **recursos relacionados**. Na página iniciar/parar VM, selecione **gerenciar a solução** no lado direito da página, em **gerenciar iniciar/parar soluções de VM**.

* Navegue até o espaço de trabalho Log Analytics vinculado à sua conta de automação. Depois de selecionar o espaço de trabalho, escolha **soluções** no painel esquerdo. Na página soluções, selecione a solução **Start-Stop-VM [Workspace]** na lista.  

A seleção da solução exibe a página de soluções **Start-Stop-VM [workspace]**. Aqui você pode examinar detalhes importantes, como as informações no bloco **StartStopVM** . Como no seu espaço de trabalho do Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook da solução que foi iniciada e encerrada com êxito.

![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Você pode executar uma análise adicional dos registros de trabalho clicando no bloco de rosca. O painel da solução mostra o histórico de trabalhos e as consultas de pesquisa de log predefinidas. Alterne para o portal avançado do log Analytics para pesquisar com base em suas consultas de pesquisa.

## <a name="update-the-solution"></a>Atualizar a solução

Se você implantou uma versão anterior dessa solução, exclua-a da sua conta antes de implantar uma versão atualizada. Siga as etapas para [remover a solução](#remove-the-solution) e, em seguida, siga as etapas para [implantar a solução](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Remover a solução

Se você não precisar mais usar a solução, poderá excluí-la da conta de automação. A exclusão da solução remove apenas os runbooks. Ela não exclui os agendamentos ou as variáveis que foram criadas quando a solução foi adicionada. Remova esses ativos manualmente se você não os estiver usando com outros runbooks.

Para excluir a solução:

1. Na sua conta de automação, selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Selecione **ir para o espaço de trabalho**.

3. Clique em **soluções** em **geral**. 

4. Na página Soluções, selecione a solução **Start-Stop-VM[Workspace]**. 

5. Na página **VMManagementSolution [espaço de trabalho]** , selecione **excluir** no menu.<br><br> ![Excluir a Solução de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela **excluir solução** , confirme que você deseja excluir a solução.

7. Embora as informações sejam verificadas e a solução seja excluída, você pode acompanhar o progresso em **notificações**, escolhidas no menu. Você será levado para a página soluções depois que o processo para remover a solução for iniciado.

A conta de Automação e o espaço de trabalho do Log Analytics não serão excluídos como parte desse processo. Se não quiser manter o espaço de trabalho Log Analytics, você deverá excluí-lo manualmente da portal do Azure:

1. Pesquise e selecione **log Analytics espaços de trabalho**.

2. Na página Log Analytics espaço de trabalho, selecione o espaço de trabalho.

3. Selecione **Excluir** no menu da página de configurações do workspace.

4. Se você não quiser manter os [componentes da solução](#solution-components)de conta de automação do Azure, poderá excluir cada um manualmente.

## <a name="next-steps"></a>Próximas etapas

[Habilite](automation-solution-vm-management-enable.md) a solução **iniciar/parar VMs fora do horário comercial** para suas VMs do Azure.
