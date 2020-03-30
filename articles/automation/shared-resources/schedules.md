---
title: Agendas na Automação do Azure
description: As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Descreve como criar e gerenciar uma agenda para que você possa iniciar um runbook automaticamente em um determinado momento ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252656"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendando um runbook na Automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou de forma recorrente a cada hora ou diariamente para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar horários com o PowerShell no Azure Automation. Eles são enviados como parte do [módulo do PowerShell do Azure](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera uma agenda. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de uma agenda existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Recupera runbooks agendados. |
| [Registro-AzureRMAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook a uma agenda. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocia um runbook de uma agenda. |

## <a name="creating-a-schedule"></a>Criando uma agenda

Você pode criar um novo cronograma para runbooks no portal Azure ou com o PowerShell.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  Para evitar o impacto em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendas vinculadas com uma conta de Automação dedicada a teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso não continuem, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar Módulos do Azure](../automation-update-azure-modules.md) em **Módulos**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure

1. No portal Azure, a partir de sua conta de automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
2. Clique em **Adicionar um agendamento** na parte superior da página.
3. No painel **Novo agendamento**, digite um **Nome** e, opcionalmente, uma **Descrição** para a nova agenda.
4. Selecione se o agendamento é executado uma vez ou em um agendamento recorrente selecionando **Once** ou **Recurring**. Se você selecionar **Uma vez**, especifique uma **Hora de início** e clique em **Criar**. Se você selecionar **Recorrente**, especifique um **Horário de início** e para **Recorrente a cada**, selecione a frequência com que o runbook deve repetir - por **horas**, **dia**, **semana** ou pelo **mês**.
    1. Se você selecionar **a semana,** você tem uma lista dos dias da semana para escolher. Selecione quantos dias você desejar. A primeira execução da agenda de ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher uma programação de fim de semana, escolha **sábado** e **domingo.**

       ![Definindo a programação recorrente do fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se você selecionar **o mês,** você recebe diferentes opções. Para a **opção De ocorrências Mensais,** selecione os **dias de mês** ou dias da **semana**. Se você escolher **os dias do mês**, um calendário é mostrado que permite que você escolha quantos dias quiser. Se você escolher uma data como a 31ª que não ocorre no mês atual, o cronograma não será executado. Se você quiser que a programação seja executada no último dia, escolha **Sim** em **Executar no último dia do mês**. Se você escolher **dias da semana**, o **repetir cada** opção for apresentada. Escolher **primeira**, **segundo**, **terceira**, **quarto**, ou **último**. Finalmente escolha um dia para repetir.

       ![Programação mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **criar**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Para criar um novo cronograma com o PowerShell

Você usa o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) criar agendas. Especifique a hora de início para a agenda e a frequência de execução. Os exemplos a seguir mostram como criar muitos cenários de programação diferentes.

#### <a name="create-a-one-time-schedule"></a>Crie um cronograma de uma única hora

Os seguintes comandos de amostra criam um cronograma de uma única hora.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Crie um cronograma recorrente

Os seguintes comandos de amostra mostram como criar um cronograma recorrente que é executado todos os dias às 13:00 pm por um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Crie um cronograma semanal recorrente

Os seguintes comandos de amostra mostram como criar um cronograma semanal que é executado apenas em dias úteis.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Crie uma programação semanal recorrente para fins de semana

Os seguintes comandos de amostra mostram como criar uma programação semanal que seja executada apenas nos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Crie um cronograma recorrente para o primeiro, 15º e último dia do mês

Os seguintes comandos de amostra mostram como criar um cronograma recorrente que é executado no dia 1º, 15 e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular uma agenda a um runbook com o portal do Azure

1. No portal do Azure, em sua conta da Automação, selecione **Runbooks** na seção **Automação de Processos** à esquerda.
2. Clique no nome do runbook para agendar.
3. Se o runbook não estiver vinculado a um cronograma no momento, então você tem a opção de criar um novo cronograma ou vincular a um cronograma existente.
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (Default:Azure)** e o painel **Parâmetros** é apresentado onde você pode inserir as informações.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Para vincular um cronograma a um runbook com o PowerShell

Você pode usar o cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) para vincular um agendamento. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para obter mais informações sobre os valores do parâmetro, confira [Como iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook.md).
Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook usando um cmdlet do Gerenciador de Recursos do Azure com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Agendamento de runbooks com mais frequência

O intervalo mais frequente que a Automação do Azure pode ser configurada é uma hora. Se você precisar de agendas para executar com mais frequência que isso, há duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use [o Azure Logic Apps](../../logic-apps/logic-apps-overview.md) para chamar o webhook. O Azure Logic Apps fornece granularidade de grãos finos ao definir um cronograma.

* Crie quatro agendas todas iniciando dentro de 15 minutos entre si em execução uma vez a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Uma vez atingido o prazo de validade, o cronograma é desativado.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desabilitar uma agenda no portal do Azure

1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Altere **Habilitado** para **Não**.

> [!NOTE]
> Se você quiser desativar um cronograma que tenha um horário de início no passado, você deve alterar a data de início para um momento no futuro antes de salvá-lo.

### <a name="to-disable-a-schedule-with-powershell"></a>Para desativar um cronograma com o PowerShell

Você pode usar o cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para alterar as propriedades de uma agenda existente. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled**.

Os comandos de exemplo a seguir mostram como desabilitar uma agenda para um runbook usando um cmdlet do Gerenciador de Recursos do Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook.md)

