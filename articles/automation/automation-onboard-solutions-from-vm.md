---
title: Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma VM do Azure
description: Saiba como integrar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537025"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma máquina virtual do Azure

A Automação do Azure fornece soluções para ajudá-lo a gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de integrar computadores. É possível integrar a solução de uma máquina virtual, [a partir de sua conta de Automação](automation-onboard-solutions-from-automation-account.md), [procurando em várias máquinas ](automation-onboard-solutions-from-browse.md), ou usando um [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções a partir de uma máquina virtual do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Habilitar as soluções

Primeiro, habilite uma ou todas as três soluções em sua VM:

1. No [portal Azure,](https://portal.azure.com)selecione **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** na página inicial.
2. Selecione a VM para a qual deseja habilitar uma solução.
3. Na página VM, em **Operações,** selecione **Atualizar o gerenciamento,** **inventário**ou rastreamento **de alterações.** A máquina virtual pode existir em qualquer região, não importa a localização de sua conta de Automação. Ao embarcar em uma solução de uma VM, você precisa ter a `Microsoft.OperationalInsights/workspaces/read` permissão para determinar se a VM está a bordo de um espaço de trabalho. Para saber sobre as permissões adicionais que são necessárias, consulte [as permissões necessárias para as máquinas a bordo](automation-role-based-access-control.md#onboarding-permissions). Para saber como integrar várias máquinas de uma só vez, consulte [Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário](automation-onboard-solutions-from-automation-account.md).

4. Selecione a conta de espaço de trabalho e automação do Azure Log Analytics e clique **em Ativar** para ativar a solução. A solução demora até 15 minutos para habilitar.

![Integrar solução de Gerenciamento de Atualizações](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Acesse outras soluções e selecione **Habilitar**. As listas de seleção de conta de espaço de trabalho e automação do Log Analytics estão desativadas porque essas soluções usam a mesma conta de espaço de trabalho e automação que a solução anteriormente habilitada.

> [!NOTE]
> O Controle de Alterações e Inventário usam a mesma solução. Quando uma dessas soluções estiver habilitada, a outra também estará habilitada.

## <a name="scope-configuration"></a>Configuração de escopo

Cada solução usa uma configuração de escopo no workspace para direcionar os computadores que obtêm a solução. A configuração do escopo é um grupo de uma ou mais pesquisas salvas que são usadas para limitar o escopo da solução a computadores específicos. Para acessar as configurações do escopo:

1. Em sua conta de Automação, em **Recursos Relacionados,** selecione **Workspace**. 
2. No espaço de trabalho, em **fontes de dados do Workspace,** selecione **Configurações de escopo**.
3. Se o espaço de trabalho selecionado ainda não tiver a solução 'Gerenciamento de atualizações' ou rastreamento de alterações, as seguintes configurações de escopo serão criadas:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Se o workspace selecionado já tiver a solução, a solução não será implantada novamente e a configuração do escopo não será adicionada.

4. Selecione as elipses (**...**) em qualquer uma das configurações e clique em **Editar**. 
5. No painel **Editar configuração de escopo**, selecione **Selecionar Grupos de Computadores**. O painel **Grupos de Computadores** mostra as pesquisas salvas que são usadas para criar a configuração de escopo.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado à solução Update Management, Change Tracking ou Inventory, o computador é adicionado a uma das duas pesquisas salvas em seu espaço de trabalho. As pesquisas salvas são consultas que contêm os computadores que são direcionados para essas soluções.

Vá até seu workspace. Em **Geral**, selecione **Pesquisas salvas**. As duas pesquisas salvas usadas por essas soluções são mostradas na tabela a seguir:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione uma das pesquisas salvas para exibir a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Desvincular o workspace

As seguintes soluções são dependentes de um espaço de trabalho do Log Analytics:

* [Gerenciamento de atualizações](automation-update-management.md)
* [Controle de Alterações](automation-change-tracking.md)
* [Iniciar/parar VMs durante as horas de folga](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de Automação com um espaço de trabalho do Log Analytics, você pode desvincular sua conta diretamente do portal Azure.  Antes de prosseguir, você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de continuar. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal Azure, abra sua conta de Automação e selecione **o espaço de trabalho Vinculado** na seção Recursos **Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**.

   ![Página Desvincular workspace](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação perguntando se deseja prosseguir.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Programação de atualização - Cada um terá nomes que correspondem às implantações de atualização que você criou.

* Grupos de trabalhadores híbridos criados para a solução - Cada um será nomeado de forma semelhante à máquina1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* Variáveis

Alternativamente, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho log analytics. Em seu espaço de trabalho, selecione **Conta de Automação** em Recursos **Relacionados**. Na página Da conta de automação, selecione **Desvincular conta**.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a Configuração de Escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no workspace.
* Remova o [agente Log Analytics para Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o agente Log [Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Continue com os tutoriais das soluções para aprender como utilizá-las:

* [Tutorial - Gerenciar atualizações para sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
