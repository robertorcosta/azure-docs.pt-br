---
title: Habilitar o Controle de Alterações da Automação do Azure e Inventário da conta de Automação
description: Este artigo conta como habilitar o Controle de Alterações e Inventário de uma conta da Automação.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 13f17ed9d165e368d2e9d9cde694408b600006cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84171116"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Habilitar Controle de Alterações e Inventário de uma conta de Automação

Este artigo descreve como você pode usar uma conta de Automação para habilitar o recurso de [Controle de Alterações e Inventário](change-tracking.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, habilite uma VM existente usando os recursos Controle de Alterações e Inventário. 

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. Navegue até sua conta de Automação e selecione **Inventário** ou **Controle de alterações** em **Gerenciamento de configuração**.

2. Escolha o workspace do Log Analytics e a conta da Automação e clique em **Habilitar** para habilitar o Controle de Alterações e Inventário. A instalação leva até 15 minutos para ser concluída.

    ![Habilitar Controle de Alterações e Inventário](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Clique em **+ Adicionar VMs do Azure** e selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação. 

3. Clique em **Habilitar** para adicionar as VMs selecionadas à pesquisa salva do grupo de computadores para o recurso. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](automation-scope-configurations-change-tracking.md).

    ![Habilitar VMs do Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Habilitar VMs que não sejam do Azure

Computadores que não estão no Azure precisam ser adicionados manualmente. 

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Clique em **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente do Log Analytics para o Windows](../azure-monitor/platform/log-analytics-agent.md). Isso serve para o computador começar a relatar operações de Controle de Alterações e Inventário. Se você estiver habilitando um computador que seja gerenciado atualmente pelo Operations Manager, não será necessário um novo agente e as informações do workspace serão inseridas no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Habilitar computadores no workspace

Computadores instalados manualmente ou que já relatam para seu workspace precisam ser adicionados à Automação do Azure para que o Controle de Alterações e Inventário seja habilitado. 

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Selecione **Gerenciar computadores**. O botão **Gerenciar computadores** poderá ficar esmaecido se você tiver escolhido anteriormente a opção **Habilitar em todos os computadores disponíveis e futuros**

    ![Pesquisas salvas](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Para habilitar o Controle de Alterações e Inventário para todos os computadores disponíveis, selecione **Habilitar em todos os computadores disponíveis** na página Gerenciar computadores. Esta ação desabilita o controle para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes dos computadores que informam sobre o workspace na consulta de pesquisa salva do grupo de computadores. Quando selecionada, esta ação desabilita o botão **Gerenciar Máquinas**.

4. Para ativar o recurso para todos os computadores disponíveis e futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui as pesquisas salvas e as configurações de escopo do workspace e abre o recurso para todos os computadores do Azure e não Azure que se comunicam com o workspace. Quando selecionada, essa ação desabilita o botão **Gerenciar computadores** permanentemente, pois não há configuração de escopo à esquerda.

5. Se necessário, você pode adicionar de novo as configurações de escopo. Para isso, volte a adicionar as pesquisas salvas iniciais. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](automation-scope-configurations-change-tracking.md).

6. Para habilitar a funcionalidade em um ou mais computadores, selecione **Habilitar nos computadores selecionados** e clique em **Adicionar** próximo a cada computador em que você quer habilitar a funcionalidade. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para o recurso.

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o recurso, consulte [gerenciar controle de alterações e inventário](change-tracking-file-contents.md).
* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](troubleshoot/change-tracking.md).