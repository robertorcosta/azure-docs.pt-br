---
title: Integrar soluções de atualização e controle de alterações à Automação do Azure
description: Saiba como integrar soluções de atualização e controle de alterações à Automação do Azure.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: cacd9206eb0213d410a11ee3a2dcf5c0e28989c6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372824"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Integrar soluções de atualização e controle de alterações à Automação do Azure

Neste tutorial, você aprenderá a integrar automaticamente soluções de Atualização, Controle de Alterações e Inventário para VMs à Automação do Azure:

> [!div class="checklist"]
> * Integrar uma VM do Azure
> * Habilitar soluções
> * Instalar e atualizar módulos
> * Importar o runbook de integração
> * Iniciar o runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="onboard-an-azure-vm"></a>Integrar uma VM do Azure

Há várias maneiras de integrar computadores, por exemplo, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [procurando em vários computadores](automation-onboard-solutions-from-browse.md), [a partir da sua conta de Automação](automation-onboard-solutions-from-automation-account.md) ou por runbook. Este tutorial percorre a habilitação do Gerenciamento de Atualizações por meio de um runbook. Para integrar Máquinas Virtuais do Azure em larga escala, uma VM existente deverá ser integrada ao controle de alterações ou à solução de gerenciamento de atualizações. Nesta etapa, você integra uma máquina virtual ao gerenciamento de atualizações e ao controle de alterações.

### <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

A solução de Controle de Alterações e Inventário fornece a capacidade de [acompanhar alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) em suas máquinas virtuais. Nesta etapa, você deve habilitar a solução em uma máquina virtual.

1. No menu à esquerda, selecione **Contas de Automação** e, em seguida, selecione sua conta de automação na lista.
1. Selecione **Inventário** em **GERENCIAMENTO DE CONFIGURAÇÃO**.
1. Selecione um espaço de trabalho do Log Analytics existente ou crie um novo. Clique no botão **Habilitar**.

![Integrar solução de atualização](media/automation-onboard-solutions/inventory-onboard.png)

Quando a notificação da integração da solução de inventário e controle de alterações for concluída, clique em **Gerenciamento de Atualizações** sob **GERENCIAMENTO DE CONFIGURAÇÃO**.

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

A solução de Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure. Você pode avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM. Nesta etapa, você deve habilitar a solução para a VM.

1. Na sua conta de automação, selecione **Gerenciamento de Atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.
1. O espaço de trabalho do Log Analytics selecionado é o mesmo espaço de trabalho usado na etapa anterior. Clique em **Habilitar** para integrar a solução de Gerenciamento de atualizações.

![Integrar solução de atualização](media/automation-onboard-solutions/update-onboard.png)

Enquanto a solução de gerenciamento de atualizações está sendo instalada, uma faixa azul é mostrada. Quando a solução for habilitada, selecione **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** para ir para a próxima etapa.

### <a name="select-azure-vm-to-be-managed"></a>Selecione a VM do Azure a ser gerenciada

Agora que as soluções são habilitadas, você pode adicionar uma VM do Azure para ser integrada a essas soluções.

1. De sua Conta de Automação, na página **Controle de alterações**, selecione **+ Adicionar VM do Azure** para adicionar a máquina virtual.

1. Selecione sua VM na lista e selecione **Habilitar**. Essa ação habilita a solução de Controle de Alterações e Inventário para a máquina virtual.

   ![Habilitar a solução de atualização para VM](media/automation-onboard-solutions/enable-change-tracking.png)

1. Quando a notificação de integração da VM é concluída, da sua conta de automação, selecione **Gerenciamento de Atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

1. Selecione **+ Adicionar VM do Azure** para adicionar a máquina virtual.

1. Selecione sua VM na lista e selecione **Habilitar**. Essa ação habilita a solução de Gerenciamento de Atualizações para a máquina virtual.

   ![Habilitar a solução de atualização para VM](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se você não aguardar até que a outra solução seja concluída, ao Habilitar a próxima solução uma mensagem de erro será exibida informando: *A instalação de outra solução está em andamento nesta máquina virtual ou em uma diferente. Quando a instalação for concluída, o botão Habilitar será habilitado e você poderá solicitar a instalação da solução nesta máquina virtual.*

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os módulos do Azure mais recentes e importar `AzureRM.OperationalInsights` a fim de automatizar a integração da solução com êxito.

## <a name="update-azure-modules"></a>Atualizar Módulos do Azure

Na sua Conta de Automação, selecione **Módulos** em **RECURSOS COMPARTILHADOS**. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. Selecione **Sim** no prompt para atualizar todos os módulos do Azure existentes para a versão mais recente.

![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Instalar o módulo AzureRM.OperationalInsights

Da página **Módulos**, selecione **Procurar galeria** para abrir a galeria de módulos. Procure AzureRM.OperationalInsights e importe este módulo para a conta da Automação.

![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importar o runbook de integração

1. Na sua Conta de Automação, selecione **Runbooks** sob a **AUTOMAÇÃO DE PROCESSOS**.
1. Selecione **Procurar na galeria**.
1. Pesquise por **controle de alterações e atualização**, clique no runbook e selecione **Importar** na página **Exibir Código-fonte**. Selecione **OK** para importar o runbook para a Conta de Automação.

   ![Importar runbook de integração](media/automation-onboard-solutions/import-from-gallery.png)

1. Na página **Runbook**, selecione **Editar** e, em seguida, selecione **Publicar**. Na caixa de diálogo **Publicar Runbook**, selecione **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Você precisa ter integrado a solução de controle de alterações de atualização a uma VM do Azure para iniciar esse runbook. Ele precisa de uma máquina virtual e um grupo de recursos existente com a solução incorporada para ter parâmetros.

1. Abra o runbook Enable-MultipleSolution.

   ![Runbook de várias soluções](media/automation-onboard-solutions/runbook-overview.png)

1. Clique no botão Iniciar e insira os valores de parâmetros a seguir.

   * **VMNAME** – deixe em branco. O nome de uma VM existente em que a solução de atualização ou controle de alterações deve ser carregada. Deixando esse valor em branco, todas as VMs no grupo de recursos serão integradas.
   * **VMRESOURCEGROUP** – o nome do grupo de recursos para as VMs a serem integradas.
   * **SUBSCRIPTIONID** – deixe em branco. A ID da assinatura da nova VM a ser integrada. Se for deixada em branco, a assinatura do workspace será usada. Quando uma ID de assinatura diferente é fornecida, a conta Executar como da conta de automação deve ser adicionada como um colaborador a essa assinatura também.
   * **ALREADYONBOARDEDVM** – o nome da VM que foi integrada à solução Updates ou ChangeTracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – o nome do grupo de recursos do qual a VM é membro.
   * **SOLUTIONTYPE** – insira **Updates** ou **ChangeTracking**

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecione **OK** para iniciar o trabalho de runbook.
1. Monitore o andamento e os erros na página de trabalho do runbook.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a Configuração de Escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no workspace.
* Remover o [agente do Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Integrar uma máquina virtual do Azure manualmente.
> * Instalar e atualizar módulos do Azure necessários.
> * Importar um runbook que integra VMs do Azure.
> * Inicia o runbook que integra VMs do Azure automaticamente.

Siga este link para saber mais sobre o agendamento de runbooks.

> [!div class="nextstepaction"]
> [Agendando runbooks](automation-schedules.md).
