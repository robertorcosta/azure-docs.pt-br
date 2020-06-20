---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir da conta de Automação
description: Este artigo informa como habilitar o Gerenciamento de Atualizações a partir de uma conta de Automação.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f739134cd066f4dcc7fdf3da16c6db99a54d6265
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204932"
---
# <a name="enable-update-management-from-an-automation-account"></a>Habilitar o Gerenciamento de Atualizações de uma conta de Automação

Este artigo descreve como você pode usar sua conta de Automação para habilitar o recurso de [Gerenciamento de Atualizações](automation-update-management.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações. 

> [!NOTE]
> Quando habilitamos o Gerenciamento de Atualizações, somente determinadas regiões têm suporte para a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento com suporte, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione o workspace do Log Analytics e a conta da Automação e clique em **Habilitar** para habilitar o Gerenciamento de Atualizações. A instalação leva até 15 minutos para ser concluída.

    ![Habilitar Gerenciamento de Atualizações](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Clique em **+ Adicionar VMs do Azure** e selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação. 

3. Clique em **Habilitar** para adicionar as VMs selecionadas à pesquisa salva do grupo de computadores para o recurso.

    ![Habilitar VMs do Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Habilitar VMs que não sejam do Azure

Computadores que não estão no Azure precisam ser adicionados manualmente. 

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Clique em **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com instruções [ para instalar e configurar o agente do Log Analytics para o Windows](../azure-monitor/platform/log-analytics-agent.md). Isso serve para a máquina começar a relatar operações de Gerenciamento de Atualizações. Se você estiver habilitando uma máquina que atualmente é gerenciada pelo Operations Manager, um novo agente não é necessário, e as informações do workspace são inseridas no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Habilitar máquinas no workspace

Máquinas ou máquinas instaladas manualmente que já estão se reportando ao seu workspace devem ser adicionadas à Automação do Azure para que o Gerenciamento de Atualizações seja habilitado. 

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Gerenciar computadores**. O botão **Gerenciar computadores** poderá ficar esmaecido se você tiver escolhido anteriormente a opção **Habilitar em todos os computadores disponíveis e futuros**

    ![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Para habilitar o Gerenciamento de Atualizações para todas as máquinas disponíveis, selecione **Ativar em todas as máquinas disponíveis**, na página Gerenciar máquinas. Esta ação desabilita o controle para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes dos computadores que informam sobre o workspace na consulta de pesquisa salva do grupo de computadores. Quando selecionada, esta ação desabilita o botão **Gerenciar Máquinas**.

5. Para ativar o recurso para todos os computadores disponíveis e futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui as pesquisas salvas e as configurações de escopo do workspace e abre o recurso para todos os computadores do Azure e não Azure que se comunicam com o workspace. Quando selecionada, essa ação desabilita o botão **Gerenciar computadores** permanentemente, pois não há configuração de escopo à esquerda.

6. Se necessário, você pode adicionar de novo as configurações de escopo. Para isso, volte a adicionar as pesquisas salvas iniciais. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).

7. Para habilitar a funcionalidade em um ou mais computadores, selecione **Habilitar nos computadores selecionados** e clique em **Adicionar** próximo a cada computador em que você quer habilitar a funcionalidade. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para o recurso.

## <a name="next-steps"></a>Próximas etapas

* Para usar o Gerenciamento de Atualizações para VMs, confira [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, confira [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
