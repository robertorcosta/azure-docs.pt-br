---
title: Habilitar Controle de Alterações e Inventário da Automação do Azure por um runbook
description: Este artigo mostra como habilitar o Controle de Alterações e Inventário por um runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826735"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Habilitar Controle de Alterações e Inventário de um runbook

Este artigo descreve como você pode usar um runbook para habilitar o recurso de [Controle de Alterações e Inventário](change-tracking.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, habilite uma VM existente usando os recursos Controle de Alterações e Inventário. 

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário 

1. No portal do Azure, escolha **Contas de Automação** e sua conta de Automação na lista.
1. Selecione **Inventário** em **Gerenciamento de Configuração**.
1. Crie um workspace do Log Analytics ou selecione um existente. 
1. Clique em **Habilitar**.

    ![Habilitar Controle de Alterações e Inventário](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione a VM do Azure a ser gerenciada

Com o Controle de Alterações e Inventário habilitado, é possível adicionar uma VM do Azure para gerenciamento pelo recurso.

1. Na sua conta de Automação, selecione **Controle de Alterações** ou **Inventário** em **Gerenciamento de Configuração**.

2. Clique em **Adicionar VMs do Azure** para adicionar sua VM.

3. Escolha sua VM na lista e clique em **Habilitar**. Essa ação habilita o Controle de Alterações e Inventário para a VM.

   ![Habilitar o Controle de Alterações e Inventário para a VM](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Se tentar habilitar outro recurso antes que a configuração do Controle de Alterações e Inventário tenha sido concluída, você receberá esta mensagem: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os módulos do Azure mais recentes e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para habilitar o Controle de Alterações e Inventário para sua VM.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique em **Sim** no prompt para atualizar todos os módulos do Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-enable-changes-from-runbook/update-modules.png)

4. Retorne para **Módulos** em **Recursos Compartilhados**. 
5. Selecione **Procurar galeria** para abrir a galeria de módulos. 
6. Pesquise por Az.OperationalInsights e importe este módulo para a conta da Automação.

    ![Importar o módulo OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importar um runbook para habilitar o Controle de Alterações e Inventário

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
2. Selecione **Procurar na galeria**.
3. Pesquise `update and change tracking`.
4. Selecione o runbook e clique em **Importar** na página Exibir Origem. 
5. Clique **OK** para importar o runbook para a conta de Automação.

   ![Importar runbook para configuração](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Na página Runbook, selecione **Editar** e, em seguida, selecione **Publicar**. 
7. Na caixa de diálogo Publicar Runbook, selecione **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Você deve ter habilitado Controle de Alterações e Inventário para uma VM do Azure para iniciar este runbook. Ele requer uma VM e um grupo de recursos existentes com o recurso habilitado para parâmetros.

1. Abra o runbook **Enable-MultipleSolution**.

   ![Runbook de várias soluções](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Clique no botão Iniciar e insira os valores de parâmetros nos seguintes campos:

   * **VMNAME**: o nome de uma VM existente a ser adicionada ao Controle de Alterações e Inventário. Deixe esse campo em branco para adicionar todas as VMs no grupo de recursos.
   * **VMRESOURCEGROUP** – O nome do grupo de recursos para as VMs a serem habilitadas.
   * **SUBSCRIPTIONID** – A ID da assinatura da nova VM a ser habilitada. Deixe esse campo em branco para usar a assinatura do workspace. Ao usar uma ID de assinatura diferente, adicione a conta Executar como da sua conta da Automação como um colaborador da assinatura.
   * **ALREADYONBOARDEDVM**: o nome da VM que já está habilitada manualmente para alterações.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – o nome do grupo de recursos ao qual a VM pertence.
   * **SOLUTIONTYPE**: insira **ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selecione **OK** para iniciar o trabalho de runbook.
1. Monitore o andamento e os erros na página de trabalho do runbook.

## <a name="next-steps"></a>Próximas etapas

* Para agendar um runbook, consulte [Gerenciar agendamentos na Automação do Azure](shared-resources/schedules.md).
* Para obter detalhes do trabalho com o recurso, veja [Gerenciar Controle de Alterações e Inventário](change-tracking-file-contents.md).
* Para obter mais informações sobre as configurações de escopo, veja [Trabalhar com configurações de escopo para Controle de Alterações e Inventário](automation-scope-configurations-change-tracking.md).
* Para saber como usar o recurso para identificar o software instalado em seu ambiente, veja [Descobrir qual software está instalado em suas VMs](automation-tutorial-installed-software.md).
* Se você não quiser integrar sua conta de Automação a um workspace do Log Analytics ao habilitar o recurso, veja [Desvincular o workspace da conta de Automação](automation-unlink-workspace-change-tracking.md).
* Quando concluir a implantação de alterações nas VMs, remova-as conforme descrito em [Remover VMs do Controle de Alterações e Inventário](automation-remove-vms-from-change-tracking.md).
* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](troubleshoot/change-tracking.md).