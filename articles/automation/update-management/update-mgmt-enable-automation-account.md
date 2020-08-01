---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir da conta de Automação
description: Este artigo informa como habilitar o Gerenciamento de Atualizações a partir de uma conta de Automação.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449885"
---
# <a name="enable-update-management-from-an-automation-account"></a>Habilitar o Gerenciamento de Atualizações de uma conta de Automação

Este artigo descreve como você pode usar sua conta de Automação para habilitar o recurso de [Gerenciamento de Atualizações](update-mgmt-overview.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações.

> [!NOTE]
> Quando habilitamos o Gerenciamento de Atualizações, somente determinadas regiões têm suporte para a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento com suporte, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../index.yml) para gerenciar máquinas.
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Escolha o espaço de trabalho Log Analytics e a conta de automação e selecione **habilitar** para habilitar gerenciamento de atualizações. A instalação leva até 15 minutos para ser concluída.

    ![Habilitar Gerenciamento de Atualizações](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **+ Adicionar VMs do Azure** e selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação.

3. Selecione **habilitar** para adicionar as VMs selecionadas ao grupo de computadores salvo na pesquisa do recurso.

    ![Habilitar VMs do Azure](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Habilitar VMs que não sejam do Azure

Computadores que não estão no Azure precisam ser adicionados manualmente.

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente do log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md) para que o computador possa iniciar a emissão de relatórios para gerenciamento de atualizações. Se você estiver habilitando um computador atualmente gerenciado pelo Operations Manager, um novo agente não será necessário. As informações do espaço de trabalho são adicionadas à configuração dos agentes.

## <a name="enable-machines-in-the-workspace"></a>Habilitar máquinas no workspace

Máquinas ou máquinas instaladas manualmente que já estão se reportando ao seu workspace devem ser adicionadas à Automação do Azure para que o Gerenciamento de Atualizações seja habilitado.

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Gerenciar computadores**. O botão **Gerenciar computadores** poderá ficar esmaecido se você tiver escolhido anteriormente a opção **Habilitar em todos os computadores disponíveis e futuros**

    ![Pesquisas salvas](media/update-mgmt-enable-automation-account/managemachines.png)

3. Para habilitar o Gerenciamento de Atualizações para todas as máquinas disponíveis, selecione **Ativar em todas as máquinas disponíveis**, na página Gerenciar máquinas. Esta ação desabilita o controle para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes dos computadores que informam sobre o workspace na consulta de pesquisa salva do grupo de computadores. Quando selecionada, esta ação desabilita o botão **Gerenciar Máquinas**.

4. Para ativar o recurso para todos os computadores disponíveis e futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui as pesquisas salvas e as configurações de escopo do workspace e abre o recurso para todos os computadores do Azure e não Azure que se comunicam com o workspace. Quando selecionada, essa ação desabilita o botão **Gerenciar computadores** permanentemente, pois não há configuração de escopo à esquerda.

5. Se necessário, você pode adicionar de novo as configurações de escopo. Para isso, volte a adicionar as pesquisas salvas iniciais. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](update-mgmt-scope-configuration.md).

6. Para habilitar o recurso para um ou mais computadores, selecione **habilitar em computadores selecionados** e selecione **Adicionar** ao lado de cada computador. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para o recurso.

## <a name="next-steps"></a>Próximas etapas

* Para usar Gerenciamento de Atualizações para VMs, consulte [gerenciar atualizações e patches para suas VMs](update-mgmt-manage-updates-for-vm.md).

* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](../troubleshoot/update-management.md).