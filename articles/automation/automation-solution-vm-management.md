---
title: Iniciar/Parar VMs durante a solução de fora do horário comercial
description: Esta solução de gerenciamento de VM inicia e interrompe suas máquinas virtuais Do Azure em um cronograma e monitora proativamente os registros do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548363"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solução Iniciar/Parar VMs fora do horário comercial na Automação do Azure

As VMs Start/Stop durante o início da solução fora de hora e param suas máquinas virtuais do Azure em horários definidos pelo usuário, fornecem insights através de logs do Monitor Do Azure e enviam e-mails opcionais usando [grupos de ação](../azure-monitor/platform/action-groups.md). A solução dá suporte ao Azure Resource Manager e VMs clássicas na maioria dos cenários. 

Para usar esta solução com VMs clássicos, você precisa de uma conta Classic RunAs, que não é criada por padrão. Para obter instruções sobre como criar uma conta Clássica runAs, consulte [Contas clássicas de execução](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> As VMs Start/Stop durante a solução off-hours foram atualizadas para suportar as versões mais recentes dos módulos Azure disponíveis.

Essa solução fornece uma opção de automação de baixo custo descentralizada para usuários que desejam otimizar seus custos de VM. Com essa solução, você pode:

- [Agende as VMs para iniciar e parar](automation-solution-vm-management-config.md#schedule).
- Agende As VMs para iniciar e parar em ordem crescente [usando tags Azure](automation-solution-vm-management-config.md#tags) (não suportadas para VMs clássicas).
- Autostop VMs com base no [baixo uso da CPU](automation-solution-vm-management-config.md#cpuutil).

As seguintes limitações com a solução atual:

- Essa solução gerencia VMs em qualquer região, mas só pode ser usada na mesma assinatura que sua conta do Azure Automation.
- Essa solução está disponível no Azure e no Azure Government para qualquer região que suporte um espaço de trabalho log analytics, uma conta de Automação Azure e Alertas. Atualmente, as regiões do governo do Azure não suportam a funcionalidade de e-mail.

> [!NOTE]
> Se você estiver usando a solução para VMs clássicas, todas as VMs serão processadas sequencialmente pelo serviço de nuvem. Máquinas virtuais ainda são processadas em paralelo entre diferentes serviços de nuvem. Se você tiver mais de 20 VMs por serviço em nuvem, recomendamos criar vários horários com o **runbook** pai ScheduledStartStop_Parent e especificar 20 VMs por cronograma. Nas propriedades do cronograma, especifique como uma lista separada por comma, nomes de VM no parâmetro **VMList.** Caso contrário, se o trabalho de Automação para esta solução for executado por mais de três horas, ele será temporariamente descarregado ou parado pelo limite [de participação justa.](automation-runbook-execution.md#fair-share)
>
> As assinaturas do Azure CSP (Provedor de Soluções na Nuvem do Azure) dão suporte apenas ao modelo do Azure Resource Manager, serviços que não são do Azure Resource Manager não estão disponíveis no programa. Quando a solução Iniciar/Parar é executada, você pode receber erros, pois ele tem cmdlets para gerenciar os recursos clássicos. Para saber mais sobre CSP, confira [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se usar uma assinatura do CSP, você deverá modificar a variável [**External_EnableClassicVMs**](#variables) para **False** após a implantação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os runbooks para esta solução funcionam com uma conta do [Azure Run As](automation-create-runas-account.md). A conta Executar como é o método de autenticação preferido, pois ela usa a autenticação de certificado em vez de uma senha que poderá expirar ou ser alterada com frequência.

Recomendamos que você use uma conta de automação separada para a solução VM Start/Stop. Isso porque as versões do módulo Azure são frequentemente atualizadas, e seus parâmetros podem mudar. A solução Start/Stop VM não é atualizada na mesma cadência, portanto pode não funcionar com versões mais recentes dos cmdlets que ele usa. Também recomendamos que você teste atualizações de módulos em uma conta de automação de teste antes de importá-las em sua conta de automação de produção.

### <a name="permissions"></a>Permissões

Existem certas permissões que um usuário deve ter para implantar as VMs Start/Stop durante a solução de horas de folga. Essas permissões são diferentes se usar um espaço de trabalho de conta de automação e log analytics pré-criado ou criar novas durante a implantação. Se você é um Contribuinte na assinatura e um Administrador Global no seu inquilino do Azure Active Directory, você não precisará configurar as seguintes permissões. Se você não tiver esses direitos ou precisar configurar uma função personalizada, consulte as permissões exigidas abaixo.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Espaço de trabalho de conta de automação e log analytics pré-existente

Para implantar as VMs Start/Stop durante a solução de horas de folga em um espaço de trabalho existente de Automação Account e Log Analytics, o usuário que implanta a solução requer as seguintes permissões no **Grupo de Recursos**. Para saber mais sobre funções, consulte [Funções personalizadas para os recursos do Azure](../role-based-access-control/custom-roles.md).

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
| Microsoft.automation/automaçãoContas/empregosAgendados/gravação | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/read | Grupo de recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de recursos |
| Microsoft.Insights/diagnosticSettings/write | Grupo de recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupo de recursos |
| Microsoft.Resources/deployments/* | Grupo de recursos |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nova conta de automação e um novo espaço de trabalho do Log Analytics

Para implantar as VMs Start/Stop durante a solução de horas de folga para um novo espaço de trabalho de Conta de Automação e Log Analytics, o usuário que implanta a solução precisa das permissões definidas na seção anterior, bem como das seguintes permissões:

- Co-administrador na assinatura - Só é necessário criar o Classic Run As Account se você vai gerenciar VMs clássicos. [As contas clássicas do RunAs](automation-create-standalone-account.md#classic-run-as-accounts) não são mais criadas por padrão.
- Um membro da função [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **Application Developer.** Para obter mais informações sobre a configuração de Contas de execução como contas, consulte [Permissões para configurar contas execute como .](manage-runas-account.md#permissions)
- Contribuinte na assinatura ou nas seguintes permissões.

| Permissão |Escopo|
| --- | --- |
| Microsoft.Autorização/Operações/leitura | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/certificates/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de recursos |

## <a name="solution-components"></a>Componentes da solução

Essa solução inclui runbooks pré-configurados, cronogramas e integração com logs do Monitor Azure para que você possa adaptar a inicialização e o desligamento de suas máquinas virtuais para atender às necessidades de seus negócios.

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks implantados em sua conta da Automação por esta solução. Não faça alterações no código do runbook. Em vez disso, escreva seu próprio runbook para obter novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum runbook com *criança* anexada ao seu nome.

Todos os runbooks pai incluem o parâmetro _WhatIf_. Quando definido como **True**, o _WhatIf_ é compatível com o detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro _WhatIf_ e valida as VMs corretas que estão sendo direcionadas. Um runbook só executa suas ações definidas quando o parâmetro _WhatIf_ é definido como **False**.

|Runbook | parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook pai. Este runbook cria alertas por recurso para o cenário AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Verdadeiro ou Falso  | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> VMList: lista de VMs separadas por vírgula. Por exemplo, _vm1, vm2, vm3_.<br> *WhatIf* valida a lógica de runbook sem execução.|
|AutoStop_Disable | none | Desabilita os alertas de AutoStop e o agendamento padrão.|
|AutoStop_VM_Child | WebHookData | Chamada a partir do runbook pai. As regras de alerta chamam este manual para parar o VM clássico.|
|AutoStop_VM_Child_ARM | WebHookData |Chamada a partir do runbook pai. As regras de alerta chamam esse runbook para parar a VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Este runbook usado para executar o início ou parado de ação no grupo clássico de VM pela Cloud Services.<br> VMList: lista de VMs separadas por vírgula. Por exemplo, _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar para a parada agendada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de início ou parada para a parada programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> WhatIf: Verdadeiro ou Falso | Esta configuração afeta todas as VMs na assinatura. Edite o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** para executar apenas nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a variável **External_ExcludeVMNames**.<br> VMList: lista de VMs separadas por vírgula. Por exemplo, _vm1, vm2, vm3_.<br> _WhatIf_ valida a lógica de runbook sem execução.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso<br>VMList| Crie tags chamada **sequencestart** e **sequencestop** em cada VM para a qual você deseja sequenciar a atividade de início/parada. Os nomes das tags diferenciam maiúsculas de minúsculas. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem em que você deseja iniciar ou parar. <br> VMList: lista de VMs separadas por vírgula. Por exemplo, _vm1, vm2, vm3_. <br> _WhatIf_ valida a lógica de runbook sem execução. <br> **Observação**: as VMs devem fazer parte dos grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis da Automação do Azure. Elas devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>variáveis

A tabela a seguir lista as variáveis criadas na sua conta da Automação. Modifique apenas as variáveis com prefixo **External**. Modificar variáveis prefixadas com **Internal** causará efeitos indesejáveis.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional exigido para configurar a condição antes de disparar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan** e **LessThanOrEqual**.|
|External_AutoStop_Description | O alerta para parar a VM se o percentual da CPU exceder o limite.|
|External_AutoStop_Frequency | A freqüência de avaliação para a regra. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 min a 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deverá ser configurada.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limite para a regra de alerta do Azure especificada na variável _External_AutoStop_MetricName_. Os valores percentuais podem variar de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo que é aplicado ao tamanho de janela selecionado para avaliar a condição. Os valores aceitáveis são **Médio**, **Mínimo**, **Máximo**, **Total** e **Último**.|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure analisa a métrica selecionada para disparar um alerta. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas.|
|External_EnableClassicVMs| Especifica se a solução destina-se a VMs clássicas. O valor padrão é True. Isso deve ser definido como False para assinaturas CSP. VMs clássicos requerem uma [conta clássica run-as](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Insira os nomes das VMs a serem excluídas, separando-os por vírgula, sem espaços. Isso é limitado a 140 VMs. Se você adicionar mais de 140 VMs a esta lista separada por comma, as VMs que serão excluídas poderão ser inadvertidamente iniciadas ou paradas.|
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações de iniciar.|
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações de parar.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam executadas nas VMs para o runbook seqüenciado de início/parada.<br> O valor padrão é de 2100 segundos e suporta a configuração para um valor máximo de 10800 ou três horas.|
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|
|Internal_AutoSnooze_ARM_WebhookURI | Especifica o Webhook URI chamado para o cenário AutoStop para VMs clássicos.|
|Internal_AutoSnooze_WebhookUri | Especifica o URI do Webhook chamado para o cenário AutoStop.|
|Internal_AzureSubscriptionId | Especifica a ID da Assinatura do Azure.|
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta da Automação.|

>[!NOTE]
>Para a variável **External_WaitTimeForVMRetryInSeconds,** o valor padrão foi atualizado de 600 para 2100. Essa variável permite que o manual de **cenário sinuoso de início/parada** aguarde as operações do filho por um número especificado de segundos antes de prosseguir com a próxima ação.
>

Em todos os cenários, as variáveis **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** são necessárias para direcionar VMs, com exceção da disponibilização de uma lista de VMs separadas por vírgula para os runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** e **ScheduledStartStop_Parent**. Em outras palavras, suas VMs devem residir em grupos de recursos de destino para que as ações iniciar e parar ocorram. A lógica funciona semelhante à política do Azure, isto é, você pode direcionar a assinatura ou o grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar e parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.É possível modificá-las ou criar suas próprias agendas personalizadas.Por padrão, todas as agendas são desabilitadas, exceto **Scheduled_StartVM** e **Scheduled_StopVM**.

Você não deve habilitar todas os agendamentos, porque isso poderá criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja executar e modificar de acordo. Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, que, por sua vez, interrompe os valores baseados em VM em External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis da Automação do Azure. Como alternativa, você pode especificar uma lista de VMs separadas por vírgula utilizando o parâmetro VMList.|
|Scheduled_StopVM | Definida pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _Parar_ todos os dias no horário especificado.Interrompe automaticamente todas as VMs que atendem às regras definidas por variáveis de ativo.Habilite o agendamento relacionado, **Scheduled-StartVM**.|
|Scheduled_StartVM | Definida pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _Iniciar_ todos os dias no horário especificado. Inicia automaticamente todas as VMs que atendem às regras definidas pelas variáveis adequadas.Habilite o agendamento relacionado, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o runbook Scheduled_Parent com um parâmetro de _Parar_ toda sexta-feira no horário especificado.Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida pelas variáveis adequadas. Para ver mais informações sobre os valores de tag e variáveis de ativos, confira a seção de Runbooks.Habilite o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o runbook Scheduled_Parent com um parâmetro de _Parar_ toda segunda-feira no horário determinado. Inicia sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida pelas variáveis adequadas. Para ver mais informações sobre os valores de tag e variáveis de ativos, confira a seção de Runbooks. Habilite o agendamento relacionado, **Sequenced-StopVM**.|

## <a name="enable-the-solution"></a>Habilitar a solução

Para começar a usar a solução, execute as etapas na [solução Enable Start/Stop VMs](automation-solution-vm-management-enable.md).

## <a name="viewing-the-solution"></a>Exibindo a solução

Você pode acessar a solução depois de habilitá-la de uma das seguintes maneiras:

* A partir de sua conta de automação, selecione **Start/Stop VM** em **Recursos Relacionados**. Na página **Start/Stop VM,** **selecione Gerenciar a solução** do lado direito da página, sob a seção **Gerenciar soluções VM Start/Stop**.

* Navegue até o espaço de trabalho do Log Analytics vinculado à sua conta de Automação e, após selecionar o espaço de trabalho, selecione **Soluções** no painel esquerdo. Na página **Soluções,** selecione a solução **Start-Stop-VM[workspace]** da lista.  

A seleção da solução exibe a página de soluções **Start-Stop-VM [workspace]**. Aqui você pode analisar detalhes importantes, como o bloco **StartStopVM**. Como no seu espaço de trabalho do Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook da solução que foi iniciada e encerrada com êxito.

![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Daqui, você pode executar outras análises dos registros de trabalho clicando no bloco de rosca. O painel da solução mostra o histórico de trabalhos e as consultas de pesquisa de logs pré-definidas. Mude para o portal avançado de análise de log para pesquisar com base em suas consultas de pesquisa.

## <a name="update-the-solution"></a>Atualizar a solução

Se você tiver implantado uma versão anterior dessa solução, será preciso primeiro excluí-la da sua conta antes de implantar uma versão atualizada. Siga as etapas para [remover a solução](#remove-the-solution) e siga as etapas para implantar a [solução.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>Remover a solução

Se decidir que não precisa mais usar a solução, você poderá excluí-la da conta de Automação. A exclusão da solução remove apenas os runbooks. Ela não exclui os agendamentos ou as variáveis que foram criadas quando a solução foi adicionada. Esses ativos deverão ser excluídos manualmente se não estiverem sendo usados com outros runbooks.

Para excluir a solução, execute as etapas a seguir:

1. A partir de sua conta de Automação, em **Recursos Relacionados,** selecione **espaço de trabalho vinculado.**

2. Selecione **Ir para o espaço de trabalho**.

3. Em **Geral,** selecione **Soluções**. 

4. Na página **Soluções**, selecione a solução **Start-Stop-VM[Workspace]**. Na página **VMManagementSolution[Workspace]**, no menu, selecione **Excluir**.<br><br> ![Excluir a Solução de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. Na janela **Excluir Solução**, confirme que deseja excluir a solução.

6. Enquanto as informações são verificadas e a solução é excluída, você pode acompanhar seu progresso no menu **Notificações**. Você é levado de volta à página **Soluções** após o início do processo de remoção da solução.

A conta de Automação e o espaço de trabalho do Log Analytics não serão excluídos como parte desse processo. Se você não deseja manter o espaço de trabalho do Log Analytics, é necessário excluí-lo manualmente. Isso pode ser feito no portal do Azure:

1. No portal Azure, procure e selecione **espaços de trabalho do Log Analytics**.

2. Na página **'Log Analytics's workspaces',** selecione o espaço de trabalho.

3. Selecione **Excluir** no menu da página de configurações do workspace.

Se você não deseja manter os componentes de conta de automação do Azure, você poderá excluir cada um manualmente. Para obter a lista de runbooks, variáveis e agendamentos criados pela solução, consulte a [componentes da solução](#solution-components).

## <a name="next-steps"></a>Próximas etapas

[Habilite](automation-solution-vm-management-enable.md) a solução Start/Stop durante a resolução off-hours de suas VMs Azure.
