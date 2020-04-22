---
title: Iniciar/interromper VMs durante a solução fora do horário de expediente
description: Esta solução de gerenciamento de VM inicia e interrompe suas máquinas virtuais Do Azure em um cronograma e monitora proativamente os registros do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 369e3bcf4e5913f4a3ff82206d1e24a206db3f34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681303"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Iniciar/interromper VMs durante a solução off-hours no Azure Automation

As **VMs Start/stop durante a solução off-hours** iniciam ou param suas máquinas virtuais Do Zure. Ele inicia ou interrompe máquinas em horários definidos pelo usuário, fornece insights através de logs do Monitor Do Azure e envia e-mails opcionais usando [grupos de ação](../azure-monitor/platform/action-groups.md). A solução suporta tanto o Azure Resource Manager quanto as VMs clássicas para a maioria dos cenários. 

Essa solução fornece uma opção de automação de baixo custo descentralizada para usuários que desejam otimizar seus custos de VM. Com essa solução, você pode:

- [Agende as VMs para iniciar e parar](automation-solution-vm-management-config.md#schedule).
- Agende As VMs para iniciar e parar em ordem crescente [usando tags Azure](automation-solution-vm-management-config.md#tags) (não suportadas para VMs clássicas).
- Autostop VMs com base no [baixo uso da CPU](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> As **VMs Start/stop durante** a solução off-hours foram atualizadas para suportar as versões mais recentes dos módulos Azure disponíveis.

As seguintes limitações com a solução atual:

- Ele gerencia VMs em qualquer região, mas só pode ser usado na mesma assinatura que sua conta do Azure Automation.
- Ele está disponível no Azure e no Azure Government para qualquer região que suporte um espaço de trabalho log analytics, uma conta de Automação Azure e alertas. Atualmente, as regiões do governo do Azure não suportam a funcionalidade de e-mail.

## <a name="solution-prerequisites"></a>Pré-requisitos de solução

Os runbooks para esta solução funcionam com uma conta do [Azure Run As](automation-create-runas-account.md). A conta Run As é o método de autenticação preferido porque usa autenticação de certificado em vez de uma senha que pode expirar ou alterar com freqüência.

Recomendamos que você use uma conta de automação separada para as **VMs Start/stop durante a solução off-hours.** As versões do módulo Azure são frequentemente atualizadas, e seus parâmetros podem mudar. A solução não é atualizada na mesma cadência e pode não funcionar com versões mais recentes dos cmdlets que ele usa. Recomenda-se testar atualizações de módulos em uma conta de automação de teste antes de importá-las em sua conta de automação de produção.

## <a name="solution-permissions"></a>Permissões de solução

Você deve ter certas permissões para implantar as **VMs Start/stop durante a** solução fora do horário de expediente. As permissões são diferentes se a solução usar uma conta de automação pré-criada e espaço de trabalho do Log Analytics a partir das permissões necessárias se a solução criar uma nova conta e espaço de trabalho durante a implantação. 

Você não precisa configurar permissões se você é um Contribuinte na assinatura e um administrador global no seu inquilino do Azure Active Directory. Se você não tiver esses direitos ou precisar configurar uma função personalizada, certifique-se de que você tenha as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para o espaço de trabalho de conta de automação e log analytics pré-existentes

Para implantar as **VMs Start/stop durante** a solução off-hours em uma conta de automação existente e espaço de trabalho do Log Analytics, o usuário que implanta a solução requer as seguintes permissões no escopo do Grupo de Recursos. Para saber mais sobre funções, consulte [Funções personalizadas para os recursos do Azure](../role-based-access-control/custom-roles.md).

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

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Permissões para nova conta de automação e novo espaço de trabalho do Log Analytics

Você pode implantar as **VMs Start/stop durante** a solução off-hours para uma nova conta de automação e espaço de trabalho do Log Analytics. Neste caso, o usuário que implanta a solução precisa das permissões definidas na seção anterior, bem como das permissões definidas nesta seção. 

O usuário que implantar a solução precisa das seguintes funções:

- Co-administrador na assinatura. Essa função é necessária para criar a conta Classic Run As se você quiser gerenciar VMs clássicas. [As contas clássicas do Run As](automation-create-standalone-account.md#create-a-classic-run-as-account) não são mais criadas por padrão.
- Adesão à função de desenvolvedor de aplicativos do diretório ativo do [Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Para obter mais informações sobre a configuração de Contas de execução como contas, consulte [Permissões para configurar contas execute como .](manage-runas-account.md#permissions)
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

As **VMs Start/stop durante a solução off-hours** incluem runbooks pré-configurados, horários e integração com registros do Monitor Do Azure. Você pode usar esses elementos para adaptar a inicialização e o desligamento de suas VMs para atender às suas necessidades de negócios.

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks que a solução implanta na sua conta de Automação. NÃO faça alterações no código do livro de execução. Em vez disso, escreva seu próprio runbook para obter novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum manual com **criança** anexada ao seu nome.

Todos os resumos `WhatIf` dos pais incluem o parâmetro. Quando definido como True, o parâmetro suporta detalhar o comportamento exato que o runbook leva quando executado sem o parâmetro e valida que as VMs corretas são direcionadas. Um runbook só executa `WhatIf` suas ações definidas quando o parâmetro é definido como Falso.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook pai. Este runbook cria alertas por recurso para o cenário de Auto-Stop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Verdadeiro ou Falso  | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> `VMList`é uma lista separada por comma de VMs. Por exemplo, `vm1, vm2, vm3`.<br> `WhatIf`permite a validação da lógica do runbook sem executar.|
|AutoStop_Disable | Nenhum | Desativa alertas de Auto-Stop e cronograma padrão.|
|AutoStop_VM_Child | WebHookData | Chamada a partir do runbook pai. As regras de alerta chamam este manual para parar um VM clássico.|
|AutoStop_VM_Child_ARM | WebHookData |Chamada a partir do runbook pai. As regras de alerta chamam este manual para parar uma VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Realiza ação start ou stop no grupo clássico de VM pela Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar para a parada agendada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de início ou parada para a parada programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> WhatIf: Verdadeiro ou Falso | Inicia ou interrompe todas as VMs na assinatura. Edite as `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variáveis e execute apenas nesses grupos de recursos direcionados. Você também pode excluir VMs `External_ExcludeVMNames` específicos atualizando a variável.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso<br>VMList| Cria tags named **sequencestart** e **sequencestop** em cada VM para a qual você deseja sequenciar a atividade de início/parada. Os nomes das tags diferenciam maiúsculas de minúsculas. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem em que você deseja iniciar ou parar. <br>**Nota**: As VMs devem `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`estar `External_ExcludeVMNames` dentro de grupos de recursos definidos em , e variáveis. Elas devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>Variáveis

A tabela a seguir lista as variáveis criadas na sua conta da Automação. Apenas modifique variáveis `External`prefixadas com . Modificar variáveis prefixadas `Internal` com causa efeitos indesejáveis.

> [!NOTE]
> As limitações no nome vm e no grupo de recursos são em grande parte resultado do tamanho variável. Ver [ativos variáveis no Azure Automation](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional exigido para configurar a condição antes de disparar um alerta. Valores aceitáveis `GreaterThanOrEqual` `LessThan`são, `LessThanOrEqual` `GreaterThan`e .|
|External_AutoStop_Description | O alerta para parar a VM se o percentual da CPU exceder o limite.|
|External_AutoStop_Frequency | A freqüência de avaliação para a regra. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deverá ser configurada.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limite para a regra Alerta Azure especificado na variável `External_AutoStop_MetricName`. Os valores percentuais variam de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo se inscreveu no tamanho da janela selecionada para avaliar a condição. Valores aceitáveis `Minimum` `Maximum`são, `Total` `Average` `Last`e .|
|External_AutoStop_TimeWindow | O tamanho da janela durante a qual o Azure analisa métricas selecionadas para disparar um alerta. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas.|
|External_EnableClassicVMs| Valor especificando se as VMs clássicas são alvo da solução. O valor padrão é True. Defina essa variável como falsa para assinaturas CSP (Azure Cloud Solution Provider). VMs clássicos requerem uma [conta Classic Run As](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Lista separada por vírgula de nomes VM para excluir, limitada a 140 VMs. Se você adicionar mais de 140 VMs à lista, as VMs que estão definidas para serem excluídas podem ser inadvertidamente iniciadas ou interrompidas.|
|External_Start_ResourceGroupNames | Lista separada por comma de um ou mais grupos de recursos que são direcionados para iniciar ações.|
|External_Stop_ResourceGroupNames | Lista separada por comma de um ou mais grupos de recursos que são direcionados para ações de stop.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam executadas nas VMs para o **runbook SequencedStartStop_Parent.** Essa variável permite que o manual de execução aguarde as operações do filho por um número especificado de segundos antes de prosseguir com a próxima ação. O tempo máximo de espera é de 10800, ou três horas. O valor padrão é de 2100 segundos.|
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|
|Internal_AutoSnooze_ARM_WebhookURI | O webhook URI pediu o cenário AutoStop para VMs.|
|Internal_AutoSnooze_WebhookUri | O webhook URI pediu o cenário AutoStop para VMs clássicos.|
|Internal_AzureSubscriptionId | A ID de assinatura do Azure.|
|Internal_ResourceGroupName | O nome do grupo de recursos da conta de Automação.|

>[!NOTE]
>Para a `External_WaitTimeForVMRetryInSeconds`variável, o valor padrão foi atualizado de 600 para 2100. 

Em todos os cenários, `External_Start_ResourceGroupNames`as variáveis , `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` e são necessárias para direcionar VMs, exceto para as listas de VM separadas por comma para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** runbooks. Ou seja, suas VMs devem pertencer a grupos de recursos-alvo para que as ações de início e parada ocorram. A lógica funciona semelhante à política do Azure, isto é, você pode direcionar a assinatura ou o grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar e parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.É possível modificá-las ou criar suas próprias agendas personalizadas.Por padrão, todos os horários estão desativados, exceto os **horários Scheduled_StartVM** e **Scheduled_StopVM.**

Não habilite todos os horários, porque isso pode criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja fazer e modificá-las de acordo. Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o **manual de AutoStop_CreateAlert_Parent** a cada 8 horas, `External_Start_ResourceGroupNames`o `External_Stop_ResourceGroupNames`que, por sua vez, interrompe os valores baseados em VM em , e `External_ExcludeVMNames` variáveis. Alternativamente, você pode especificar uma lista separada por comma de VMs usando o `VMList` parâmetro.|
|Scheduled_StopVM | Definido pelo usuário, diariamente | Executa o **manual de** ScheduledStopStart_Parent `Stop` com um parâmetro de cada dia na hora especificada.Interrompe automaticamente todas as VMs que atendam às regras definidas por ativos variáveis.Habilitar o cronograma relacionado **Programado-StartVM**.|
|Scheduled_StartVM | Definido pelo usuário, diariamente | Executa o **manual de ScheduledStopStart_Parent** `Start` com um valor de parâmetro de cada dia na hora especificada. Inicia automaticamente todas as VMs que atendam às regras definidas por ativos variáveis.Habilitar o cronograma relacionado **Scheduled-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o manual **de execução Sequenced_StopStop_Parent** com um valor de parâmetro de `Stop` todas as sextas-feiras no horário especificado.Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida pelas variáveis adequadas. Para obter mais informações sobre valores de tag e variáveis de ativos, consulte [Runbooks](#runbooks).Habilite o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o **manual de SequencedStopStart_Parent** `Start` com um valor de parâmetro de cada segunda-feira no horário especificado. Inicia sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida pelas variáveis adequadas. Para obter mais informações sobre valores de tag e ativos variáveis, consulte [Runbooks](#runbooks). Habilite o agendamento relacionado, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Uso da solução com VMs clássicos

Se você estiver usando as **VMs Start/stop durante** a solução off-hours para VMs clássicas, então a Automação processa todas as suas VMs sequencialmente por serviço de nuvem. As VMs ainda são processadas em paralelo em diferentes serviços de nuvem. 

Para usar a solução com VMs clássicos, você precisa de uma conta Classic Run As, que não é criada por padrão. Para obter instruções sobre como criar uma conta Classic Run As, consulte [Criar uma conta clássica de execução como .](automation-create-standalone-account.md#create-a-classic-run-as-account)

Se você tem mais de 20 VMs por serviço em nuvem, aqui estão algumas recomendações:

* Crie vários horários com o runbook pai **ScheduledStartStop_Parent** e especificando 20 VMs por cronograma. 
* Nas propriedades do cronograma, use o `VMList` parâmetro para especificar nomes de VM como uma lista separada por comma. 

Caso contrário, se o trabalho de Automação para esta solução for executado por mais de três horas, ele será temporariamente descarregado ou parado pelo limite [de participação justa.](automation-runbook-execution.md#fair-share)

As assinaturas do Azure CSP suportam apenas o modelo Azure Resource Manager. Os serviços do Non-Azure Resource Manager não estão disponíveis no programa. Quando as **VMs Start/stop durante a solução off-hours** forem executadas, você poderá receber erros, uma vez que ele tem cmdlets para gerenciar recursos clássicos. Para saber mais sobre CSP, confira [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se você usar uma assinatura CSP, você deve definir a variável [External_EnableClassicVMs](#variables) como Falsa após a implantação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Habilitar a solução

Para começar a usar a solução, siga as etapas da [solução Enable Start/stop VMs](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Exibir a solução

Use um dos seguintes mecanismos para acessar a solução depois de habilitá-la:

* A partir de sua conta de automação, selecione **Start/Stop VM** em **Recursos Relacionados**. Na página Start/Stop VM, **selecione Gerenciar a solução** do lado direito da página, em **Manage Start/Stop VM Solutions**.

* Navegue até o espaço de trabalho do Log Analytics vinculado à sua conta de Automação. Depois de selecionar o espaço de trabalho, escolha **Soluções** no painel esquerdo. Na página Soluções, selecione a solução **Start-Stop-VM[workspace]** da lista.  

A seleção da solução exibe a página de soluções **Start-Stop-VM [workspace]**. Aqui você pode rever detalhes importantes, como as informações no azulejo **StartStopVM.** Como no seu espaço de trabalho do Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook da solução que foi iniciada e encerrada com êxito.

![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Você pode realizar uma análise mais aprofundada dos registros de trabalho clicando na telha do donut. O painel de soluções mostra o histórico do trabalho e as consultas de pesquisa de log predefinidas. Mude para o portal avançado de análise de log para pesquisar com base em suas consultas de pesquisa.

## <a name="update-the-solution"></a>Atualizar a solução

Se você implantou uma versão anterior desta solução, exclua-a de sua conta antes de implantar uma versão atualizada. Siga as etapas para [remover a solução](#remove-the-solution) e siga as etapas para implantar a [solução.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>Remover a solução

Se você não precisar mais usar a solução, você pode excluí-la da conta Automação. A exclusão da solução remove apenas os runbooks. Ela não exclui os agendamentos ou as variáveis que foram criadas quando a solução foi adicionada. Remova esses ativos manualmente se você não estiver usando-os com outros runbooks.

Para excluir a solução:

1. A partir de sua conta de Automação, selecione **o espaço de trabalho Vinculado** em recursos **relacionados**.

2. Selecione **Ir para o espaço de trabalho**.

3. Clique em **Soluções** em **Geral**. 

4. Na página Soluções, selecione a solução **Start-Stop-VM[Workspace]**. 

5. Na página **VMManagementSolution[Workspace],** **selecione Excluir** no menu.<br><br> ![Excluir a Solução de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela **Excluir solução,** confirme se deseja excluir a solução.

7. Enquanto as informações são verificadas e a solução é excluída, você pode acompanhar o progresso em **Notificações**, escolhidas no menu. Você é devolvido à página Soluções após o processo para remover o início da solução.

A conta de Automação e o espaço de trabalho do Log Analytics não serão excluídos como parte desse processo. Se você não quiser manter o espaço de trabalho do Log Analytics, você deve excluí-lo manualmente do portal Azure:

1. Procure e selecione **espaços de trabalho do Log Analytics**.

2. Na página do espaço de trabalho do Log Analytics, selecione o espaço de trabalho.

3. Selecione **Excluir** no menu da página de configurações do workspace.

4. Se você não quiser manter os [componentes](#solution-components)da solução da conta Azure Automation, você pode excluir manualmente cada um deles.

## <a name="next-steps"></a>Próximas etapas

[Habilite](automation-solution-vm-management-enable.md) as **VMs Start/stop durante** a solução off-hours de suas VMs Azure.
