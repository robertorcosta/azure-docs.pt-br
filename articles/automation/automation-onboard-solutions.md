---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure por runbook
description: Este artigo informa como habilitar o Gerenciamento de Atualizações por meio de um runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 39ebdb6937b03d72365e9d3785af9571ebb66186
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836049"
---
# <a name="enable-update-management-from-a-runbook"></a>Habilitar o Gerenciamento de Atualizações de um runbook

Este artigo descreve como você pode usar um runbook para habilitar o recurso de [Gerenciamento de Atualizações](automation-update-management.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações. 

> [!NOTE]
> Ao habilitar o Gerenciamento de Atualizações, somente determinadas regiões são compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. Na sua conta da Automação, selecione **Gerenciamento de Atualizações** em **Gerenciamento de Atualizações**.

2. Selecione o workspace do Log Analytics e clique em **Habilitar**. Enquanto Gerenciamento de Atualizações está sendo habilitado, uma faixa azul é mostrada. 

    ![Habilitar Gerenciamento de Atualizações](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione a VM do Azure a ser gerenciada

Com o Gerenciamento de Atualizações habilitado, você pode adicionar uma VM do Azure para receber atualizações.

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Adicionar VMs do Azure** para adicionar sua VM.

3. Escolha a VM na lista e clique em **Habilitar** para configurar a VM para atualizações. 

   ![Habilitar o Gerenciamento de Atualizações para a VM](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Se você tentar habilitar outro recurso antes que a configuração do Gerenciamento de Atualizações tenha sido concluída, você receberá esta mensagem: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os módulos do Azure mais recentes e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para ter êxito ao habilitar o Gerenciamento de Atualizações para suas VMs.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique em **Sim** no prompt para atualizar todos os módulos do Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)

4. Retorne para **Módulos** em **Recursos Compartilhados**. 
5. Selecione **Procurar na galeria** para abrir a galeria de módulos. 
6. Pesquise `Az.OperationalInsights` e importe esse módulo para a sua conta da Automação.

    ![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importar um runbook para habilitar o Gerenciamento de Atualizações

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
2. Selecione **Procurar na galeria**.
3. Pesquise `update and change tracking`.
4. Selecione o runbook e clique em **Importar** na página Exibir Origem. 
5. Clique **OK** para importar o runbook para a conta de Automação.

   ![Importar runbook para configuração](media/automation-onboard-solutions/import-from-gallery.png)

6. Na página Runbook, selecione **Editar** e, em seguida, selecione **Publicar**. 
7. Na caixa de diálogo Publicar Runbook, selecione **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Você deve ter habilitado o Gerenciamento de Atualizações para uma VM do Azure para iniciar este runbook. Ele requer uma VM e um grupo de recursos existentes com o recurso habilitado para parâmetros.

1. Abra o runbook **Enable-MultipleSolution**.

   ![Runbook de várias soluções](media/automation-onboard-solutions/runbook-overview.png)

2. Clique no botão Iniciar e insira os valores de parâmetros nos seguintes campos:

   * **VMNAME** – O nome de uma VM existente a ser adicionada ao Gerenciamento de Atualizações. Deixe esse campo em branco para adicionar todas as VMs no grupo de recursos.
   * **VMRESOURCEGROUP** – O nome do grupo de recursos para as VMs a serem habilitadas.
   * **SUBSCRIPTIONID** – A ID da assinatura da nova VM a ser habilitada. Deixe esse campo em branco para usar a assinatura do workspace. Ao usar uma ID de assinatura diferente, adicione a conta Executar como da sua conta da Automação como um colaborador da assinatura.
   * **ALREADYONBOARDEDVM** – O nome da VM que já está habilitada manualmente para atualizações.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – o nome do grupo de recursos ao qual a VM pertence.
   * **SOLUTIONTYPE** – Insira **Atualizações**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Selecione **OK** para iniciar o trabalho de runbook.
4. Monitore o andamento e os erros na página de trabalho do runbook.

## <a name="next-steps"></a>Próximas etapas

* Para agendar um runbook, consulte [Gerenciar agendamentos na Automação do Azure](shared-resources/schedules.md).
* Para usar o Gerenciamento de Atualizações para VMs, consulte [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para configurações de escopo, consulte [Trabalhar com configurações de escopo](automation-scope-configurations-update-management.md).
* Se você não precisar mais do workspace do Log Analytics, consulte as instruções em [Desvincular workspace da conta da Automação para Gerenciamento de Atualizações](automation-unlink-workspace-update-management.md).
* Para excluir VMs do Gerenciamento de Atualizações, consulte [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, consulte [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, consulte [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, consulte [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).