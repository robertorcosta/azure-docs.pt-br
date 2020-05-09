---
title: Gerenciar agendas na automação do Azure
description: Saiba como criar e gerenciar uma agenda na automação do Azure para que você possa iniciar automaticamente um runbook em um horário específico ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4cd6d4236b95a17f404df13e8b50daf989cf6072
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652103"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerenciar agendas na automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou em um agendamento recorrente por hora ou diário para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte a configurações de DSC de Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlets do PowerShell usados para acessar agendas

Os cmdlets na tabela a seguir criam e gerenciam agendas de automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](modules.md#az-modules). 

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera uma agenda. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera runbooks agendados. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Cria uma nova agenda. |
| [Registrar-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa um runbook a uma agenda. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Remove uma agenda. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Define as propriedades de uma agenda existente. |
| [Cancelar registro-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissocia um runbook de uma agenda. |

## <a name="create-a-schedule"></a>Criar uma agenda

Você pode criar um novo agendamento para seus runbooks no portal do Azure ou com o PowerShell. Para evitar afetar seus runbooks e os processos que eles automatizam, primeiro você deve testar todos os runbooks que têm agendas vinculadas com uma conta de automação dedicada para teste. Um teste valida que seus runbooks agendados continuam funcionando corretamente. Se você vir um problema, poderá solucionar e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.

> [!NOTE]
> Sua conta de automação não obtém automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar módulos do Azure](../automation-update-azure-modules.md) nos **módulos**. A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Criar uma nova agenda no portal do Azure

1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
1. Selecione **Adicionar um agendamento** na parte superior da página.
1. No painel **novo agendamento** , insira um nome e, opcionalmente, insira uma descrição para o novo agendamento.
1. Selecione se a agenda é executada uma vez ou em um agendamento recorrente selecionando **uma vez** ou **repetindo**. Se você selecionar **uma vez**, especifique uma hora de início e, em seguida, selecione **criar**. Se você selecionar **recorrente**, especifique uma hora de início. Para **repetir a cada**, selecione com que frequência você deseja que o runbook se repita. Selecione por hora, dia, semana ou mês.
    1. Se você selecionar **semana**, os dias da semana serão apresentados para sua escolha. Selecione quantos dias você desejar. A primeira execução da agenda de ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher uma agenda de fim de semana, selecione sábado e domingo.
    
       ![Definindo agendamento recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se você selecionar **mês**, terá opções diferentes. Para a opção **ocorrências mensais** , selecione **dias do mês** ou **dias da semana**. Se você selecionar **dias do mês**, um calendário será exibido para que você possa escolher quantos dias desejar. Se você escolher uma data como o dia 31 que não ocorre no mês atual, a agenda não será executada. Se você quiser que a agenda seja executada no último dia, selecione **Sim** em **executar no último dia do mês**. Se você selecionar **dias da semana**, a opção **repetir a cada** será exibida. Escolher **primeira**, **segundo**, **terceira**, **quarto**, ou **último**. Por fim, escolha um dia para repetir.

       ![Agendamento mensal no primeiro, décimo-quinto e no último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

1. Quando tiver terminado, selecione **criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Criar uma nova agenda com o PowerShell

Use o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) para criar agendas. Especifique a hora de início para a agenda e a frequência de execução. Os exemplos a seguir mostram como criar vários cenários de agendamento diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar uma agenda única

O exemplo a seguir cria uma agenda única.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um agendamento recorrente

O exemplo a seguir mostra como criar um agendamento recorrente que é executado todos os dias às 1:00 para um ano.

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

O exemplo a seguir mostra como criar um agendamento semanal que é executado somente em fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Criar um agendamento recorrente para os primeiros, décimo-quinto e últimos dias do mês

O exemplo a seguir mostra como criar uma agenda recorrente que é executada no primeiro, décimo-quinto e no último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Vincular um agendamento a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você poderá fornecer valores para eles. Você deve fornecer valores para parâmetros obrigatórios e também pode fornecer valores para quaisquer parâmetros opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular um agendamento a um runbook com o portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **Runbooks** em **automação de processo**.
1. Selecione o nome do runbook a ser agendado.
1. Se o runbook não estiver atualmente vinculado a um agendamento, você terá a opção de criar uma nova agenda ou vincular a uma agenda existente.
1. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (padrão: Azure)** e o painel **parâmetros** será exibido. Você pode inserir informações de parâmetro aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Vincular um agendamento a um runbook com o PowerShell

Use o cmdlet [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) para vincular uma agenda. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para obter mais informações sobre como especificar valores de parâmetro, consulte [iniciando um runbook na automação do Azure](../automation-starting-a-runbook.md).
O exemplo a seguir mostra como vincular uma agenda a um runbook usando um cmdlet Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Agendar runbooks para serem executados com mais frequência

O intervalo mais frequente para o qual uma agenda na automação do Azure pode ser configurada é de uma hora. Se você precisar que os agendamentos sejam executados com mais frequência do que isso, há duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use os [aplicativos lógicos do Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. O aplicativo lógico do Azure fornece granularidade mais refinada para definir uma agenda.

* Crie quatro agendas que iniciam dentro de 15 minutos umas das outras e executadas uma vez a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disable-a-schedule"></a>Desabilitar uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desabilitar uma agenda do portal do Azure

1. Em sua conta de automação, selecione **agendas** em **recursos compartilhados**.
1. Selecione o nome de um agendamento para abrir o painel de detalhes.
1. Altere **Habilitado** para **Não**.

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

## <a name="remove-a-schedule"></a>Remover uma agenda

Quando estiver pronto para remover suas agendas, você poderá usar o portal do Azure ou o PowerShell. Lembre-se de que você só pode remover uma agenda que foi desabilitada conforme descrito na seção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remover uma agenda usando o portal do Azure

1. Em sua conta de automação, selecione **agendas** em **recursos compartilhados**.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Clique em **Excluir**.

### <a name="remove-a-schedule-with-powershell"></a>Remover uma agenda com o PowerShell

Você pode usar o `Remove-AzAutomationSchedule` cmdlet, conforme mostrado abaixo, para excluir uma agenda existente. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar agendas, consulte [gerenciar módulos na automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [execução de runbook na automação do Azure](../automation-runbook-execution.md).
