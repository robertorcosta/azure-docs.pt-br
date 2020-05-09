---
title: Solução de configuração de Iniciar/Parar VMs fora do horário comercial da automação do Azure
description: Este artigo descreve como configurar a solução de Iniciar/Parar VMs fora do horário comercial para dar suporte a cenários ou casos de uso diferentes.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4cceb0d5ada82de73bc74c0ed408f8eb988ea8ec
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864259"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Como configurar a solução de Iniciar/Parar VMs fora do horário comercial

Com a solução **iniciar/parar VMs fora do horário comercial** , você pode:

- [Agende as VMs para iniciar e parar](#schedule).
- Agende as VMs para iniciar e parar em ordem crescente [usando marcas do Azure](#tags) (sem suporte para VMs clássicas).
- Interrompa as VMs automaticamente com base no [baixo uso da CPU](#cpuutil).

Este artigo descreve como configurar com êxito a solução para dar suporte a esses cenários. Você também pode aprender a executar outras definições de configuração comuns para a solução, como:

* [Configurar notificações por email](#configure-email-notifications)

* [Adicionar uma VM](#add-a-vm)

* [Excluir uma VM](#exclude-a-vm)

* [Modificar as agendas de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Cenário 1: Iniciar/parar VMs em um agendamento

Este cenário é a configuração padrão ao implantar a solução pela primeira vez. Por exemplo, você pode configurar a solução para parar todas as VMs em uma assinatura ao sair do trabalho à noite e iniciá-las pela manhã ao retornar ao escritório. Quando configurados durante a implantação, os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM** iniciam e param VMs de destino. A configuração desta solução para simplesmente parar VMs tem suporte, consulte [Modificar agendamentos de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules) para aprender a configurar um agendamento personalizado.

> [!NOTE]
> O fuso horário é o seu fuso horário atual de quando o parâmetro de hora do agendamento foi configurado. No entanto, o parâmetro é armazenado no formato UTC na Automação do Azure. Não é necessário executar nenhuma conversão de fuso horário, pois isso acontece durante a implantação.

Para controlar quais VMs estão no escopo, configure as seguintes variáveis: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames**.

Você pode habilitar o direcionamento da ação a uma assinatura e um grupo de recursos ou direcionar a uma lista específica de VMs, mas não ambos.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar a ação de início e parada para uma assinatura e um grupo de recursos

1. Configure as `External_Stop_ResourceGroupNames` variáveis `External_ExcludeVMNames` e para especificar as VMs de destino.

2. Habilite e atualize os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Execute o runbook **ScheduledStartStop_Parent** com o campo parâmetro de **ação** definido como **Iniciar** e o campo de parâmetro **WHATIF** definido como true para visualizar as alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Execute o runbook **ScheduledStartStop_Parent** com a **ação** definida para **Iniciar**, adicione uma lista separada por vírgulas de VMs no campo parâmetro **VMList** e, em seguida, defina o campo de parâmetro **WHATIF** como true. Visualize as alterações.

2. Configure a `External_ExcludeVMNames` variável com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).

3. Esse cenário não respeita as `External_Start_ResourceGroupNames` variáveis e `External_Stop_ResourceGroupnames` . Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agendamento de runbooks na Automação do Azure](../automation/automation-schedules.md).

    > [!NOTE]
    > O valor dos **nomes de resourcegroup de destino** é armazenado como o valor para `External_Start_ResourceGroupNames` e. `External_Stop_ResourceGroupNames` Para obter uma maior granularidade, você pode modificar cada uma das variáveis para direcionar diferentes grupos de recursos. Para iniciar ação, use `External_Start_ResourceGroupNames`e use `External_Stop_ResourceGroupNames` para ação de parada. As VMs são adicionadas automaticamente aos agendamentos de início e parada.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/parar VMs na sequência usando marcas

Em um ambiente que inclui dois ou mais componentes em várias VMs compatíveis com cargas de trabalho distribuídas, é importante dar suporte ao sequenciamento de quais componentes são iniciados/parados em ordem. Para alcançar este cenário, execute as etapas a seguir:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar a ação de início e parada para uma assinatura e um grupo de recursos

1. Adicione um `sequencestart` e uma `sequencestop` marca com um valor inteiro positivo às VMs que são direcionadas `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` variáveis. As ações iniciar e parar são executadas em ordem crescente. Para saber como marcar uma VM, consulte [Marcar uma Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Marcar uma Máquina Virtual do Linux no Azure](../virtual-machines/linux/tag.md).

2. Modifique os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** de acordo com a data e hora que atendem às suas exigências e habilite o agendamento.

3. Execute o **SequencedStartStop_Parent** runbook com a **ação** definida como **Iniciar** e **WHATIF** definido como true para visualizar as alterações.

4. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute manualmente o runbook com o parâmetro definido como **false**ou deixe a agenda `Sequenced-StartVM` de automação `Sequenced-StopVM` e execute-a automaticamente seguindo o agendamento prescrito.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Adicione um `sequencestart` e uma `sequencestop` marca com um valor inteiro positivo às VMs que você planeja adicionar ao `VMList` parâmetro.

2. Execute o runbook **SequencedStartStop_Parent** com a **ação** definida como **Iniciar**, adicione uma lista separada por vírgulas de VMs no campo parâmetro **VMList** e, em seguida, defina **WHATIF** como verdadeiro. Visualize as alterações.

3. Configure a `External_ExcludeVMNames` variável com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).

4. Esse cenário não respeita as `External_Start_ResourceGroupNames` variáveis e `External_Stop_ResourceGroupnames` . Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agendamento de runbooks na Automação do Azure](../automation/automation-schedules.md).

5. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute manualmente o **Monitoring-and-Diagnostics/Monitoring-Action-groupsrunbook** com o parâmetro definido como **false**. Como alternativa, permita que a automação `Sequenced-StartVM` seja `Sequenced-StopVM` agendada e executada automaticamente seguindo o agendamento prescrito.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar/parar automaticamente com base na utilização da CPU

Essa solução pode ajudar a gerenciar o custo de execução de máquinas virtuais Azure Resource Manager e clássicas em sua assinatura, avaliando as VMs que não são usadas fora de períodos de pico, como após as horas, e desligando-as automaticamente se a utilização do processador for menor do que uma porcentagem especificada.

Por padrão, a solução é pré-configurada para avaliar a métrica percentual da CPU para ver se a utilização média é de 5% ou menos. Esse cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores padrão não atenderem às suas necessidades:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Você pode habilitar e direcionar a ação em uma assinatura e grupo de recursos ou direcionar uma lista específica de VMs.

Quando você executa o runbook **AutoStop_CreateAlert_Parent** , ele verifica se a assinatura de destino, os grupos de recursos e as VMs existem. Se as VMs existirem, o runbook chamará o runbook de **AutoStop_CreateAlert_Child** para cada VM verificada pelo runbook pai. Esse runbook filho executa o seguinte:

* Cria uma regra de alerta de métrica para cada VM verificada.

* Dispara o **AutoStop_VM_Child** runbook para uma VM específica se a CPU cair abaixo do limite configurado para o intervalo de tempo especificado. Em seguida, esse runbook tenta parar a VM.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Para direcionar a ação de parada automática em todas as VMs em uma assinatura

1. Verifique se a `External_Stop_ResourceGroupNames` variável está vazia ou definida como * (curinga).

2. [Etapa opcional] Se você quiser excluir algumas VMs do desligamento automático, poderá adicionar uma lista separada por vírgulas de nomes de VM à `External_ExcludeVMNames` variável.

3. Habilite `Schedule_AutoStop_CreateAlert_Parent` o agendamento a ser executado para criar as regras de alerta de interrupção de métrica de VM necessárias para todas as VMs em sua assinatura. A execução desse tipo de agenda permite criar novas regras de alerta de métrica à medida que novas VMs são adicionadas à assinatura.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Para direcionar a ação de parada automática em todas as VMs em um grupo de recursos ou em vários grupos de recursos

1. Adicione uma lista separada por vírgulas de nomes de grupos de `External_Stop_ResourceGroupNames` recursos à variável.

2. Se você quiser excluir algumas das VMs do desligamento automático, poderá adicionar uma lista separada por vírgulas de nomes de VM à `External_ExcludeVMNames` variável.

3. Habilite o agendamento de **Schedule_AutoStop_CreateAlert_Parent** para executar para criar as regras de alerta de interrupção de métrica de VM necessárias para todas as VMs em seus grupos de recursos. A execução dessa operação em uma agenda permite que você crie novas regras de alerta de métrica à medida que novas VMs são adicionadas aos grupos de recursos.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Para direcionar a ação de autostop para uma lista de VMs

1. Crie um novo [agendamento](shared-resources/schedules.md#create-a-schedule) e vincule-o ao runbook de **AutoStop_CreateAlert_Parent** , adicionando uma lista separada por vírgulas de nomes de `VMList` VM ao parâmetro.

2. Opcionalmente, se você quiser excluir algumas VMs do desligamento automático, poderá adicionar uma lista separada por vírgulas de nomes de VM à `External_ExcludeVMNames` variável.

## <a name="configure-email-notifications"></a>Configurar notificações por email

Para alterar as notificações por email depois que a solução for implantada, modifique o grupo de ações criado durante a implantação.  

> [!NOTE]
> As assinaturas na nuvem do Azure governamental não dão suporte à funcionalidade de email dessa solução.

1. No portal do Azure, navegue até **Monitor**e, em seguida, **grupos de ações**. Selecione o grupo de ação chamado **StartStop_VM_Notication**.

    ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-monitor.png)

2. Na página **StartStop_VM_Notification**, clique em **Editar detalhes** em **Detalhes**. Isso abre a página **Email/SMS/Push/Voz**. Atualize o endereço de email e clique em **OK** para salvar suas alterações.

    ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/change-email.png)

    Como alternativa, você pode adicionar mais ações no grupo de ações. Para saber mais sobre grupos de ações, confira [grupos de ações](../azure-monitor/platform/action-groups.md)

A seguir está um email de exemplo que é enviado quando a solução desliga as máquinas virtuais.

![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Adicionar/excluir VMs

A solução fornece a capacidade de adicionar VMs para o destino da solução ou excluir máquinas específicas da solução.

### <a name="add-a-vm"></a>Adicionar uma VM

Há duas opções que você pode usar para garantir que uma VM seja incluída na solução iniciar/parar quando for executada.

* Cada um dos [runbooks](automation-solution-vm-management.md#runbooks) pai da solução tem um `VMList` parâmetro. Você pode passar uma lista separada por vírgulas de nomes de VM para esse parâmetro ao agendar o runbook pai apropriado para sua situação e essas VMs serão incluídas quando a solução for executada.

* Para selecionar várias VMs, defina `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` com os nomes do grupo de recursos que contêm as VMs que você deseja iniciar ou parar. Você também pode definir as variáveis para um valor de `*` para que a solução seja executada em todos os grupos de recursos na assinatura.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir uma VM da solução, você pode adicioná-la à `External_ExcludeVMNames` variável. Essa variável é uma lista separada por vírgulas de VMs específicas a serem excluídas da solução iniciar/parar. Essa lista é limitada a 140 VMs. Se você adicionar mais de 140 VMs a essa lista separada por vírgulas, as VMs definidas como excluídas poderão ser iniciadas ou interrompidas inadvertidamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar as agendas de inicialização e desligamento

O gerenciamento das agendas de inicialização e desligamento nesta solução segue as mesmas etapas descritas em [Agendando um runbook na Automação do Azure](automation-schedules.md). É necessário haver um agendamento separado para iniciar e parar VMs.

Há suporte para configurar a solução para simplesmente parar as VMs em um determinado momento. Nesse cenário, você apenas cria uma agenda de **interrupção** e nenhuma agenda de **início** correspondente. Para fazer isso, você precisa:

1. Verifique se você adicionou os grupos de recursos para que as VMs sejam desligadas na `External_Stop_ResourceGroupNames` variável.

2. Crie sua própria agenda para a hora em que você deseja desligar as máquinas virtuais.

3. Navegue até o runbook **ScheduledStartStop_Parent** e clique em **Agenda**. Isso permite que você selecione a agenda que criou na etapa anterior.

4. Selecione **parâmetros e configurações de execução** e defina o campo de **ação** a ser **interrompido**.

5. Selecione **OK** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

* Para saber como solucionar problemas Iniciar/Parar VMs fora do horário comercial, consulte [solução de problemas de iniciar/parar VMs](troubleshoot/start-stop-vm.md).

* [Examine](automation-solution-vm-management-logs.md) os registros de automação gravados nos Logs de Azure monitor e as consultas de pesquisa de log de exemplo para analisar o status dos trabalhos de runbook de automação de iniciar/parar VMS.
