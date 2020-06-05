---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir de uma VM do Azure
description: Este artigo informa como habilitar o Gerenciamento de Atualizações a partir de uma VM do Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743955"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Habilitar o Gerenciamento de Atualizações de uma VM do Azure

Este artigo descreve como você pode usar uma VM do Azure para habilitar o recurso de [Gerenciamento de Atualizações](automation-update-management.md) em outras máquinas. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações. 

> [!NOTE]
> Quando habilitamos o Gerenciamento de Atualizações, somente determinadas regiões têm suporte para a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento com suporte, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. No [portal do Azure](https://portal.azure.com), selecione **Máquinas virtuais** ou procure e selecione **Máquinas virtuais** na página inicial.

2. Selecione a VM para a qual você deseja habilitar o Gerenciamento de Atualizações. As VMs podem existir em qualquer região, independentemente do local da sua conta da Automação. Você

3. Na página da VM, em **Operações**, selecione **Gerenciamento de Atualizações**.

4. Você precisa ter a permissão `Microsoft.OperationalInsights/workspaces/read` para determinar se a VM está habilitada para um espaço de trabalho. Para saber mais sobre as permissões adicionais que são necessárias, confira [Permissões necessárias para habilitar os computadores](automation-role-based-access-control.md#feature-setup-permissions). Para saber como habilitar vários computadores de uma só vez, consulte [Habilitar Gerenciamento de Atualizações a partir de uma conta da Automação](automation-onboard-solutions-from-automation-account.md).

5. Selecione o workspace do Log Analytics e a conta da Automação e clique em **Habilitar** para habilitar o Gerenciamento de Atualizações. A instalação leva até 15 minutos para ser concluída. 

    ![Habilitar Gerenciamento de Atualizações](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verificar a configuração de escopo

O Gerenciamento de Atualizações usa uma configuração de escopo dentro do workspace para definir os computadores a serem habilitados para o recurso. A configuração de escopo é um grupo de uma ou mais pesquisas salvas utilizadas para limitar o escopo do recurso a computadores específicos. Para obter mais informações, consulte [Trabalhar com configurações de escopo para o Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Próximas etapas

* Para usar o Gerenciamento de Atualizações para VMs, consulte [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para configurações de escopo, consulte [Trabalhar com configurações de escopo para o Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).
* Se você não precisar mais do workspace do Log Analytics, consulte as instruções em [Desvincular workspace da conta da Automação para Gerenciamento de Atualizações](automation-unlink-workspace-update-management.md).
* Para excluir VMs do Gerenciamento de Atualizações, consulte [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).
* Para corrigir erros gerais do Gerenciamento de Atualizações, consulte [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, consulte [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, consulte[Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
