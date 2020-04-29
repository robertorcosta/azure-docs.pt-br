---
title: Integrar soluções de atualização, controle de alterações e inventário à Automação do Azure
description: Saiba como integrar soluções de atualização e controle de alterações à Automação do Azure.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457613"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Integrar soluções de atualização, controle de alterações e inventário à Automação do Azure

Neste tutorial, você aprenderá a integrar automaticamente soluções de Atualização, Controle de Alterações e Inventário para VMs à Automação do Azure:

> [!div class="checklist"]
> * Integrar uma VM do Azure
> * Habilitar soluções
> * Instalar e atualizar módulos
> * Importar o runbook de integração
> * Iniciar o runbook

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="onboard-an-azure-vm"></a>Integrar uma VM do Azure

Há várias maneiras de integrar computadores, por exemplo, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [procurando em vários computadores](automation-onboard-solutions-from-browse.md), [de sua conta de Automação](automation-onboard-solutions-from-automation-account.md) ou por runbook. Este tutorial percorre a habilitação do Gerenciamento de Atualizações por meio de um runbook. Para integrar Máquinas Virtuais do Azure em larga escala, uma VM existente deverá ser integrada ao controle de alterações ou à solução de gerenciamento de atualizações. Nesta etapa, você integra uma máquina virtual ao gerenciamento de atualizações e ao controle de alterações.

### <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

A solução de Controle de Alterações e Inventário permite a você [acompanhar alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) em suas máquinas virtuais. Nesta etapa, você deve habilitar as soluções em uma máquina virtual.

1. No portal do Azure, selecione **Contas de Automação** e, em seguida, selecione sua conta de automação na lista.
1. Selecione **Inventário** em **Gerenciamento de Configuração**.
1. Crie um workspace do Log Analytics ou selecione um existente. 
1. Clique em **Habilitar**.

    ![Integrar solução de atualização](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

A solução de Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure. Você pode avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM. Nesta etapa, você deve habilitar a solução para a VM.

1. Na sua conta de Automação, selecione **Gerenciamento de Atualizações** na seção **Gerenciamento de Atualizações**.
1. O workspace do Log Analytics selecionado é o workspace usado na etapa anterior. Clique em **Habilitar** para integrar a solução de Gerenciamento de atualizações. Enquanto a solução de gerenciamento de atualizações está sendo instalada, uma faixa azul é mostrada. 

    ![Integrar solução de atualização](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Selecione a VM do Azure a ser gerenciada

Agora que as soluções são habilitadas, você pode adicionar uma VM do Azure para ser integrada a essas soluções.

1. Na sua conta de Automação, selecione **Controle de Alterações** em **Gerenciamento de Configuração**. 
2. Na página Controle de alterações, clique em **Adicionar VMs do Azure** para adicionar sua VM.

3. Selecione sua VM na lista e clique em **Habilitar**. Essa ação habilita a solução de Controle de Alterações e Inventário para a VM.

   ![Habilitar a solução de atualização para VM](media/automation-onboard-solutions/enable-change-tracking.png)

4. Quando a notificação de integração da VM é concluída, selecione **Gerenciamento de Atualizações** em **Gerenciamento de Atualizações**.

5. Selecione **Adicionar VMs do Azure** para adicionar sua VM.

6. Selecione sua VM na lista e selecione **Habilitar**. Essa ação habilita a solução de Gerenciamento de Atualizações para a VM.

   ![Habilitar a solução de atualização para VM](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se você não aguardar até que a outra solução seja concluída, receberá a seguinte mensagem ao Habilitar a próxima solução: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os módulos do Azure mais recentes e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para automatizar a integração da solução com êxito.

## <a name="update-azure-modules"></a>Atualizar Módulos do Azure

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique em **Sim** no prompt para atualizar todos os módulos do Azure existentes para a versão mais recente.

![Atualizar módulos](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Instalar o módulo Az.OperationalInsights

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione **Procurar galeria** para abrir a galeria de módulos. 
3. Pesquise por Az.OperationalInsights e importe este módulo para a conta da Automação.

![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importar o runbook de integração

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
1. Selecione **Procurar na galeria**.
1. Pesquise `update and change tracking`.
3. Selecione o runbook e clique em **Importar** na página Exibir Origem. 
4. Clique **OK** para importar o runbook para a conta de Automação.

   ![Importar runbook de integração](media/automation-onboard-solutions/import-from-gallery.png)

6. Na página Runbook, selecione **Editar** e, em seguida, selecione **Publicar**. 
7. Na caixa de diálogo Publicar Runbook, selecione **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Você precisa ter integrado a solução de controle de alterações de atualização a uma VM do Azure para iniciar esse runbook. Ele precisa de uma máquina virtual e um grupo de recursos existente com a solução incorporada para ter parâmetros.

1. Abra o runbook **Enable-MultipleSolution**.

   ![Runbook de várias soluções](media/automation-onboard-solutions/runbook-overview.png)

1. Clique no botão Iniciar e insira os valores de parâmetros a seguir.

   * **VMNAME** – deixe em branco. O nome de uma VM existente em que a solução de atualização ou controle de alterações deve ser carregada. Deixando esse valor em branco, todas as VMs no grupo de recursos serão integradas.
   * **VMRESOURCEGROUP** – o nome do grupo de recursos para as VMs a serem integradas.
   * **SUBSCRIPTIONID** – deixe em branco. A ID da assinatura da nova VM a ser integrada. Se for deixada em branco, a assinatura do workspace será usada. Quando uma ID de assinatura diferente é fornecida, a conta Executar como da conta de automação deve ser adicionada como um colaborador a essa assinatura também.
   * **ALREADYONBOARDEDVM** – o nome da VM que foi integrada à solução Updates ou ChangeTracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – o nome do grupo de recursos ao qual a VM pertence.
   * **SOLUTIONTYPE** – insira **Updates** ou **ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecione **OK** para iniciar o trabalho de runbook.
1. Monitore o andamento e os erros na página de trabalho do runbook.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover uma VM do Gerenciamento de Atualizações:

1. No workspace do Log Analytics, remova a VM da pesquisa salva para a configuração de escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no workspace.
2. Remova o [agente do Log Analytics para Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Integrar uma máquina virtual do Azure manualmente.
> * Instalar e atualizar módulos do Azure necessários.
> * Importar um runbook que integra VMs do Azure.
> * Inicia o runbook que integra VMs do Azure automaticamente.

Siga este link para saber mais sobre o agendamento de runbooks.

> [!div class="nextstepaction"]
> [Agendando runbooks](automation-schedules.md).
