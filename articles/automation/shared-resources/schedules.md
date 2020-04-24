---
title: Gerenciar agendas na automação do Azure
description: As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Descreve como criar e gerenciar uma agenda para que você possa iniciar um runbook automaticamente em um determinado momento ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97dc7cb90f5ffc577a007c23074a0e8a75788fab
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114013"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerenciar agendas na automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou de forma recorrente a cada hora ou diariamente para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar agendas com o PowerShell na automação do Azure. Eles são enviados como parte do [módulo do PowerShell do Azure](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera uma agenda. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera runbooks agendados. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Cria uma nova agenda. |
| [Registrar-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa um runbook a uma agenda. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Remove uma agenda. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Define as propriedades de uma agenda existente. |
| [Cancelar registro-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissocia um runbook de uma agenda. |

## <a name="creating-a-schedule"></a>Criando uma agenda

Você pode criar um novo agendamento para runbooks no portal do Azure ou com o PowerShell.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  Para evitar o impacto em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendas vinculadas com uma conta de Automação dedicada a teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso não continuem, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar Módulos do Azure](../automation-update-azure-modules.md) em **Módulos**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Criar uma nova agenda no portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **agendas** na seção **recursos compartilhados** à esquerda.
2. Clique em **Adicionar um agendamento** na parte superior da página.
3. No painel novo agendamento, digite um nome e, opcionalmente, uma descrição para o novo agendamento.
4. Selecione se o agendamento é executado uma vez ou em um agendamento recorrente selecionando **Once** ou **Recurring**. Se você selecionar **uma vez**, especifique uma hora de início e, em seguida, clique em **criar**. Se você selecionar **recorrente**, especifique uma hora de início. Para **repetir a cada**, selecione com que frequência você deseja que o runbook repita-por hora, dia, semana ou mês.
    1. Se você selecionar **semana**, os dias da semana serão apresentados para sua escolha. Selecione quantos dias você desejar. A primeira execução da agenda de ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher uma agenda de fim de semana, selecione sábado e domingo. 
    
       ![Definindo agendamento recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se você selecionar **mês**, terá opções diferentes. Para a opção **ocorrências mensais** , selecione **dias do mês** ou **dias da semana**. Se você escolher **dias do mês**, será mostrado um calendário que permite que você escolha quantos dias desejar. Se você escolher uma data como o dia 31 que não ocorre no mês atual, a agenda não será executada. Se você quiser que a programação seja executada no último dia, escolha **Sim** em **Executar no último dia do mês**. Se você escolher **dias da semana**, o **repetir cada** opção for apresentada. Escolher **primeira**, **segundo**, **terceira**, **quarto**, ou **último**. Finalmente escolha um dia para repetir.

       ![Agendamento mensal no primeiro, décimo-quinto e no último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Criar uma nova agenda com o PowerShell

Use o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) para criar agendas. Especifique a hora de início para a agenda e a frequência de execução. Os exemplos a seguir mostram como criar vários cenários de agendamento diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar uma agenda única

Os comandos de exemplo a seguir criam um agendamento de uma vez.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um agendamento recorrente

O exemplo a seguir mostra como criar um agendamento recorrente que é executado todos os dias em 1:13h por um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar uma agenda recorrente semanal

O exemplo a seguir mostra como criar um agendamento semanal que é executado somente em dias da semana.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um agendamento recorrente semanal para fins de semana

Os comandos de exemplo a seguir mostram como criar um agendamento semanal que é executado somente em fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Criar um agendamento recorrente para o primeiro, o 15º e o último dia do mês

O exemplo a seguir mostra como criar uma agenda recorrente que é executada no 1º, no 15º e no último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular um agendamento a um runbook com o portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **Runbooks** em **automação de processo**.
2. Clique no nome do runbook para agendar.
3. Se o runbook não estiver atualmente vinculado a um agendamento, você terá a opção de criar uma nova agenda ou vincular a uma agenda existente.
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (padrão: Azure)** e o painel de parâmetros será apresentado. Você pode inserir informações de parâmetro aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Vincular um agendamento a um runbook com o PowerShell

Use o cmdlet [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) para vincular uma agenda. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para obter mais informações sobre os valores do parâmetro, confira [Como iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook.md).
O exemplo a seguir mostra como vincular um agendamento a um runbook usando um cmdlet Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Agendando runbooks para serem executados com mais frequência

O intervalo mais frequente que a Automação do Azure pode ser configurada é uma hora. Se você precisar de agendas para executar com mais frequência que isso, há duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use o [aplicativo lógico do Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. O aplicativo lógico do Azure fornece granularidade mais refinada ao definir uma agenda.

* Crie quatro agendas todas iniciando dentro de 15 minutos entre si em execução uma vez a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Depois que o tempo de expiração for atingido, o agendamento será desabilitado.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desabilitar uma agenda do portal do Azure

1. Em sua conta de automação, selecione **agendas** em **recursos compartilhados**.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Altere **Habilitado** para **Não**.

> [!NOTE]
> Se desejar desabilitar uma agenda que tenha uma hora de início no passado, você deverá alterar a data de início para uma hora no futuro antes de salvá-la.

### <a name="disable-a-schedule-with-powershell"></a>Desabilitar uma agenda com o PowerShell

Use o cmdlet [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) para alterar as propriedades de uma agenda existente. Para desabilitar a agenda, especifique false para o `IsEnabled` parâmetro.

O exemplo a seguir mostra como desabilitar uma agenda para um runbook usando um cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Removendo uma agenda

Quando estiver pronto para remover suas agendas, você poderá usar o portal do Azure ou o `Remove-AzureRmAutomationSchedule` cmdlet. Lembre-se de que você só pode remover uma agenda que foi desabilitada conforme descrito na seção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remover uma agenda usando o portal do Azure

1. Em sua conta de automação, selecione **agendas** em **recursos compartilhados**.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Clique em **Excluir**.

### <a name="remove-a-schedule-with-powershell"></a>Remover uma agenda com o PowerShell

Você pode usar o cmdlet [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) para excluir uma agenda existente. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzureRmAutomationSchedule -AutomationAccountName $automationAccountName ` -Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks na automação do Azure, consulte [Iniciar um runbook na automação do Azure](../automation-starting-a-runbook.md).