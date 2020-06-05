---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir do portal do Azure
description: Este artigo informa como habilitar o Gerenciamento de Atualizações do portal do Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: cb3bbf1a7c2e55d152d26c475369f9ccb6fb7d1e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743979"
---
# <a name="enable-update-management-from-azure-portal"></a>Habilitar o Gerenciamento de Atualizações a partir do portal do Azure

Este artigo descreve como você pode habilitar o recurso [Gerenciamento de Atualizações](automation-update-management.md) para VMs navegando no portal do Azure. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações. 

O número de grupos de recursos que você pode usar para gerenciar suas VMs é limitado pelos [limites de implantação do Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). As implantações do Resource Manager, que não devem ser confundidas com implantações de atualização, estão limitadas a cinco grupos de recursos por implantação. Dois desses grupos de recursos são reservados para configurar o workspace do Log Analytics, a conta de Automação e os recursos relacionados. Você pode então optar entre três grupos de recursos para gerenciamento pelo Gerenciamento de Atualizações. Esse limite se aplica apenas à configuração simultânea, não ao número de grupos de recursos que podem ser gerenciados por um recurso de Automação.

> [!NOTE]
> Ao habilitar o Gerenciamento de Atualizações, somente determinadas regiões são compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. No portal do Azure, navegue até **máquinas virtuais**.

2. Use as caixas de seleção para escolher as VMs a serem adicionadas ao Gerenciamento de Atualizações. Você pode adicionar computadores para até três grupos de recursos diferentes por vez. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta da Automação.

    ![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Use os controles de filtro para selecionar VMs em assinaturas diferentes, locais e grupos de recursos. Você pode clicar na caixa de seleção superior para selecionar todas as máquinas virtuais em uma lista.

    ![Habilitar Gerenciamento de Atualizações](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Clique em **Serviços** e selecione **Gerenciamento de Atualizações** para o recurso Gerenciamento de Atualizações. 

4. A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

5. Um workspace do Log Analytics existente e a conta de Automação são selecionados por padrão. Se desejar usar um workspace do Log Analytics diferente e a conta de Automação, clique em **PERSONALIZAR** para selecioná-los na página Configuração Personalizada. Quando você escolhe um workspace do Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma conta de Automação. Se uma conta de Automação vinculada for encontrada, você verá a tela a seguir. Quando terminar, clique em **OK**.

    ![Selecione workspace e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Se o workspace selecionado não estiver vinculado a uma conta de Automação, você verá a tela a seguir. Selecione uma conta de Automação e clique em **OK** ao concluir.

    ![Nenhum workspace](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. As VMs que não podem ser habilitadas já estão desmarcadas.

8. Clique em **Habilitar** para habilitar o recurso que você selecionou. A instalação leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Próximas etapas

* Para usar o Gerenciamento de Atualizações para VMs, confira [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para configurações de escopo, confira [Trabalhar com configurações de escopo para o Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).
* Se você não precisar mais do workspace do Log Analytics, confira as instruções em [Desvincular workspace da conta da Automação para Gerenciamento de Atualizações](automation-unlink-workspace-update-management.md).
* Para excluir VMs do Gerenciamento de Atualizações, confira [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, confira [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
