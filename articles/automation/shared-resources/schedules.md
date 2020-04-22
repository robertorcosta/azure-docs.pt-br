---
title: Gerenciar horários no Azure Automation
description: As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Descreve como criar e gerenciar uma agenda para que você possa iniciar um runbook automaticamente em um determinado momento ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732789"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerenciar horários no Azure Automation

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou de forma recorrente a cada hora ou diariamente para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar horários com o PowerShell no Azure Automation. Eles são enviados como parte do [módulo do PowerShell do Azure](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Cronograma de automação get-Az](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera uma agenda. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera runbooks agendados. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Cria uma nova agenda. |
| [Registro-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa um runbook a uma agenda. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Remove uma agenda. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Define as propriedades de uma agenda existente. |
| [Não registra-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissocia um runbook de uma agenda. |

## <a name="creating-a-schedule"></a>Criando uma agenda

Você pode criar um novo cronograma para runbooks no portal Azure ou com o PowerShell.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  Para evitar o impacto em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendas vinculadas com uma conta de Automação dedicada a teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso não continuem, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar Módulos do Azure](../automation-update-azure-modules.md) em **Módulos**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Crie um novo cronograma no portal Azure

1. No portal Azure, a partir de sua conta de automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
2. Clique em **Adicionar um agendamento** na parte superior da página.
3. No painel Novo, digite um nome e, opcionalmente, uma descrição para o novo horário.
4. Selecione se o agendamento é executado uma vez ou em um agendamento recorrente selecionando **Once** ou **Recurring**. Se você selecionar **Uma**vez, especifique uma hora de início e clique em **Criar**. Se você selecionar **Recorrente,** especifique uma hora de início. Para **Recur cada**, selecione quantas vezes você quer que o livro de execução se repita - por hora, dia, semana ou mês.
    1. Se você selecionar **a semana,** os dias da semana são apresentados para você escolher. Selecione quantos dias você desejar. A primeira execução da agenda de ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher uma programação de fim de semana, selecione sábado e domingo. 
    
       ![Definindo a programação recorrente do fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se você selecionar **o mês,** você recebe diferentes opções. Para a **opção De ocorrências Mensais,** selecione os **dias de mês** ou dias da **semana**. Se você escolher **os dias do mês**, um calendário é mostrado que permite que você escolha quantos dias quiser. Se você escolher uma data como a 31ª que não ocorre no mês atual, o cronograma não será executado. Se você quiser que a programação seja executada no último dia, escolha **Sim** em **Executar no último dia do mês**. Se você escolher **dias da semana**, o **repetir cada** opção for apresentada. Escolher **primeira**, **segundo**, **terceira**, **quarto**, ou **último**. Finalmente escolha um dia para repetir.

       ![Programação mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Crie um novo cronograma com o PowerShell

Use o [cmdlet New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) para criar agendas. Especifique a hora de início para a agenda e a frequência de execução. Os exemplos a seguir mostram como criar muitos cenários de programação diferentes.

#### <a name="create-a-one-time-schedule"></a>Crie um cronograma único

Os seguintes comandos de amostra criam um cronograma de uma única hora.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Crie um cronograma recorrente

O exemplo a seguir mostra como criar um horário recorrente que funciona todos os dias às 13:00 por um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Crie um cronograma semanal recorrente

O exemplo a seguir mostra como criar um horário semanal que seja executado apenas em dias úteis.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Crie uma programação semanal recorrente para fins de semana

Os seguintes comandos de amostra mostram como criar uma programação semanal que seja executada apenas nos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Crie um cronograma recorrente para o primeiro, 15º e último dia do mês

O exemplo a seguir mostra como criar um cronograma recorrente que é executado no dia 1º, 15 e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular um cronograma a um runbook com o portal Azure

1. No portal Azure, da sua conta de automação, selecione **Runbooks** em **Automação de Processos**.
2. Clique no nome do runbook para agendar.
3. Se o runbook não estiver vinculado a um cronograma no momento, então você tem a opção de criar um novo cronograma ou vincular a um cronograma existente.
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (Default:Azure)** e o painel Parâmetros é apresentado. Você pode inserir informações de parâmetros aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Vincular um cronograma a um runbook com o PowerShell

Use o [cmdlet Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) para vincular um cronograma. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para obter mais informações sobre os valores do parâmetro, confira [Como iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook.md).
O exemplo a seguir mostra como vincular um cronograma a um runbook usando um cmdlet do Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Agendamento de runbooks para executar com mais freqüência

O intervalo mais frequente que a Automação do Azure pode ser configurada é uma hora. Se você precisar de agendas para executar com mais frequência que isso, há duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use [o Azure Logic Apps](../../logic-apps/logic-apps-overview.md) para chamar o webhook. O Azure Logic Apps fornece granularidade de grãos finos ao definir um cronograma.

* Crie quatro agendas todas iniciando dentro de 15 minutos entre si em execução uma vez a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Uma vez atingido o prazo de validade, o cronograma é desativado.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desativar um cronograma do portal Azure

1. Em sua conta de Automação, selecione **Agendas** em **Recursos Compartilhados**.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Altere **Habilitado** para **Não**.

> [!NOTE]
> Se você quiser desativar um cronograma que tenha um horário de início no passado, você deve alterar a data de início para um momento no futuro antes de salvá-lo.

### <a name="disable-a-schedule-with-powershell"></a>Desativar um cronograma com o PowerShell

Use o [cmdlet Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) para alterar as propriedades de um cronograma existente. Para desativar o cronograma, especifique Falso para o `IsEnabled` parâmetro.

O exemplo a seguir mostra como desativar um cronograma de um runbook usando um cmdlet do Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para começar com runbooks no Azure Automation, consulte [Iniciar um Runbook no Azure Automation](../automation-starting-a-runbook.md).