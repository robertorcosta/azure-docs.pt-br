---
title: Configurar solução Start/Stop VMs
description: Este artigo descreve como configurar as VMs Start/Stop durante a solução off-hours para suportar diferentes casos ou cenários de uso.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550377"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Como configurar VMs Start/Stop durante a solução de horas de folga

Com as VMs Start/Stop durante a solução de horas de folga, você pode:

- [Agende as VMs para iniciar e parar](#schedule).
- Agende As VMs para iniciar e parar em ordem crescente [usando tags Azure](#tags) (não suportadas para VMs clássicas).
- Parar automaticamente as VMs com base no [baixo uso da CPU](#cpuutil).

Este artigo descreve como configurar com sucesso a solução para suportar esses cenários. Você também pode aprender como executar outras configurações comuns para a solução, tais como:

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

1. Configure as variáveis **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar as VMs de destino.

2. Habilite e atualize os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Execute o runbook **ScheduledStartStop_Parent** com o parâmetro ACTION definido como **iniciar** e o parâmetro WHATIF definido como **True** para visualizar as alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Execute o runbook **ScheduledStartStop_Parent** com o parâmetro ACTION definido como **iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e defina o parâmetro WHATIF como **True**. Visualize as alterações.

2. Configure o parâmetro **External_ExcludeVMNames** com uma lista de VMs separadas por vírgula (VM1, VM2, VM3).

3. Esse cenário não segue as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames**. Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agendamento de runbooks na Automação do Azure](../automation/automation-schedules.md).

    > [!NOTE]
    > O valor de **Target ResourceGroup Names** é armazenado como o valor de **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Para obter uma maior granularidade, você pode modificar cada uma das variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, use **External_Start_ResourceGroupNames** e, para parar a ação, use **External_Stop_ResourceGroupNames**. As VMs são adicionadas automaticamente aos agendamentos de início e parada.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/parar VMs na sequência usando marcas

Em um ambiente que inclui dois ou mais componentes em várias VMs compatíveis com cargas de trabalho distribuídas, é importante dar suporte ao sequenciamento de quais componentes são iniciados/parados em ordem. Para alcançar este cenário, execute as etapas a seguir:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar a ação de início e parada para uma assinatura e um grupo de recursos

1. Adicione uma **seqüência start** e uma **tag sequencestop** com um valor inteiro positivo às VMs que são direcionadas em **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** variáveis. As ações iniciar e parar são executadas em ordem crescente. Para saber como marcar uma VM, consulte [Marcar uma Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Marcar uma Máquina Virtual do Linux no Azure](../virtual-machines/linux/tag.md).

2. Modifique os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** de acordo com a data e hora que atendem às suas exigências e habilite o agendamento.

3. Execute o runbook **SequencedStartStop_Parent** com o parâmetro ACTION definido como **iniciar** e o parâmetro WHATIF definido como **True** para visualizar as alterações.

4. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute o runbook manualmente com o parâmetro definido como **False** ou permita que os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** da Automação sejam executados automaticamente de acordo com o agendamento prescrito.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Adicione as marcas **sequencestart** e **sequencestop** com um valor inteiro positivo às VMs que você planeja adicionar ao parâmetro **VMList**.

2. Execute o runbook **SequencedStartStop_Parent** com o parâmetro ACTION definido como **iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e defina o parâmetro WHATIF como **True**. Visualize as alterações.

3. Configure o parâmetro **External_ExcludeVMNames** com uma lista de VMs separadas por vírgula (VM1, VM2, VM3).

4. Esse cenário não segue as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames**. Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agendamento de runbooks na Automação do Azure](../automation/automation-schedules.md).

5. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute o runbook monitoring-and-diagnostics/monitoring-action-groups manualmente com o parâmetro definido como **False** ou permita que a Automação agende a execução de **Sequenced-StartVM** e de **Sequenced-StopVM** automaticamente após o agendamento prescrito.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar/parar automaticamente com base na utilização da CPU

Essa solução pode ajudar a gerenciar o custo de executar o Azure Resource Manager e as máquinas virtuais Classic em sua assinatura, avaliando VMs que não são usadas durante períodos não-pico, como após o expediente, e desligando-os automaticamente se a utilização do processador for menor que uma porcentagem especificada.

Por padrão, a solução é pré-configurada para avaliar a métrica percentual da CPU para ver se a utilização média é de 5% ou menos. Este cenário é controlado pelas seguintes variáveis e pode ser modificado caso os valores padrão não atendam às suas necessidades:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

Você pode habilitar e direcionar a ação contra um grupo de assinatura e recursos ou direcionar uma lista específica de VMs.

Quando você executa o **runbook AutoStop_CreateAlert_Parent,** ele verifica se a assinatura direcionada, os grupos de recursos e as VMs existem. Se as VMs existirem, ela então chamará o **manual de AutoStop_CreateAlert_Child** para cada VM verificada pelo runbook pai. Este manual infantil executa o seguinte:

* Cria uma regra de alerta métrico para cada VM verificada.

* Aciona o manual **de AutoStop_VM_Child** para uma VM específica caso a CPU caia abaixo do limite configurado para o intervalo de tempo especificado. Este manual então tenta parar o VM.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Para direcionar a ação de parada automática contra todas as VMs em uma assinatura

1. Certifique-se de que a variável **External_Stop_ResourceGroupNames** está vazia ou definida como * (curinga).

2. [Passo opcional] Se você deseja excluir algumas VMs do desligamento automático, você pode adicionar uma lista separada de comuma de nomes VM à variável **External_ExcludeVMNames.**

3. Habilite o cronograma **Schedule_AutoStop_CreateAlert_Parent** para criar as regras de alerta métrica súmétricas da Stop VM necessárias para todas as VMs em sua assinatura. Executar isso em um cronograma permitirá que você crie novas regras de alerta métrica à medida que novas VMs forem adicionadas à assinatura.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>Para direcionar a ação de parada automática contra todas as VMs em um grupo de recursos/vários grupos de recursos

1. Adicione uma lista comum separada de nomes de grupos de recursos à variável **External_Stop_ResourceGroupNames.**

2. Opcionalmente, se você quiser excluir algumas das VMs do desligamento automático, você pode adicionar uma lista separada de comuma de nomes VM à variável **External_ExcludeVMNames.**

3. Habilite o cronograma **Schedule_AutoStop_CreateAlert_Parent** para executar para criar as regras de alerta **métrica de Stop VM** necessárias para todas as VMs em seus grupos de recursos. Executar isso em um cronograma permite criar novas regras de alerta métrica à medida que novas VMs são adicionadas aos grupos de recursos.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>Para direcionar a ação de parada automática a uma lista de VMs

1. Crie um novo [Cronograma](shared-resources/schedules.md#creating-a-schedule) e vincule-o ao manual **de execução AutoStop_CreateAlert_Parent,** adicionando uma lista separada de comuma de nomes vm ao parâmetro **VMList.**

2. Opcionalmente, se você quiser excluir algumas VMs do desligamento automático, você pode adicionar uma lista separada de comuma de nomes VM à variável **External_ExcludeVMNames.**

## <a name="configure-email-notifications"></a>Configurar notificações por email

Para alterar as notificações por email depois que a solução for implantada, modifique o grupo de ações que foi criado durante a implantação.  

> [!NOTE]
> As assinaturas na nuvem do Azure Governamental não são compatíveis com a funcionalidade de email desta solução.

1. No portal do Azure, navegue até Monitorar -> grupos de ações. Selecione o grupo de ações intitulado **StartStop_VM_Notication**.

    ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-monitor.png)

2. Na página **StartStop_VM_Notification**, clique em **Editar detalhes** em **Detalhes**. Isso abre a página **Email/SMS/Push/Voz**. Atualize o endereço de email e clique em **OK** para salvar suas alterações.

    ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/change-email.png)

    Como alternativa, você pode adicionar mais ações no grupo de ações. Para saber mais sobre grupos de ações, confira [grupos de ações](../azure-monitor/platform/action-groups.md)

A seguir está um email de exemplo que é enviado quando a solução desliga as máquinas virtuais.

![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Adicionar/excluir VMs

A solução fornece a capacidade de adicionar VMs para o destino da solução ou excluir máquinas específicas da solução.

### <a name="add-a-vm"></a>Adicionar uma VM

Existem duas opções que você pode usar para garantir que uma VM esteja incluída na solução Start/Stop quando for executada.

* Cada um dos [runbooks](automation-solution-vm-management.md#runbooks) pai da solução tem um parâmetro **VMList.** Você pode passar uma lista separada por comuma de nomes de VM para este parâmetro ao agendar o manual de pais apropriado para sua situação e essas VMs serão incluídas quando a solução for executada.

* Para selecionar várias VMs, defina **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** com os nomes de grupo de recursos que contêm as VMs que você deseja iniciar ou parar. Você também pode definir esse valor como `*` para fazer a solução ser executada em relação a todos os grupos de recursos na assinatura.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir uma VM da solução, você pode adicioná-la à variável **External_ExcludeVMNames**. Esta variável é uma lista separada por comma de VMs específicas para excluir da solução Start/Stop. Essa lista é limitada a 140 VMs. Se você adicionar mais de 140 VMs a esta lista separada por comma, as VMs que serão excluídas poderão ser inadvertidamente iniciadas ou paradas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar as agendas de inicialização e desligamento

O gerenciamento das agendas de inicialização e desligamento nesta solução segue as mesmas etapas descritas em [Agendando um runbook na Automação do Azure](automation-schedules.md). É necessário haver um agendamento separado para iniciar e parar VMs.

Há suporte para configurar a solução para simplesmente parar as VMs em um determinado momento. Neste cenário, você acabou de criar um agendamento **Parar** e nenhum agendamento **Iniciar** correspondente. Para fazer isso, você precisa:

1. Não deixe de adicionar os grupos de recursos das VMs que serão desligadas na variável **External_Stop_ResourceGroupNames**.

2. Crie sua própria agenda para a hora em que você deseja desligar as máquinas virtuais.

3. Navegue até o runbook **ScheduledStartStop_Parent** e clique em **Agenda**. Isso permite que você selecione a agenda que criou na etapa anterior.

4. Selecione **Parâmetros e execute as configurações** e defina o parâmetro **ACTION** para **Stop**.

5. Selecione **OK** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

* Para saber como solucionar problemas Nas VMs start/stop durante as horas de folga, consulte [VMs start/stop de solução](troubleshoot/start-stop-vm.md)de problemas .

* [Revise](automation-solution-vm-management-logs.md) os registros de automação gravados no Azure Monitor Logs e as consultas de pesquisa de log de exemplo para analisar o status dos trabalhos de runbook de automação das VMs Start/Stop.
