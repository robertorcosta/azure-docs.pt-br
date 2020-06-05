---
title: Habilitar Controle de Alterações e Inventário da Automação do Azure por uma VM do Azure
description: Este artigo conta como habilitar o Controle de Alterações e Inventário por uma VM do Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5379f2c46bbeaba4ee8509603b7b739b75d08f04
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836780"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Habilitar Controle de Alterações e Inventário de uma VM do Azure

Este artigo descreve como você pode usar uma VM do Azure para habilitar o recurso de [Controle de Alterações e Inventário](change-tracking.md) em outras máquinas. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Controle de Alterações e Inventário. 

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. No [portal do Azure](https://portal.azure.com), selecione **Máquinas virtuais** ou procure e selecione **Máquinas virtuais** na página inicial.

2. Selecione a VM para a qual você deseja habilitar o Controle de Alterações e Inventário. As VMs podem existir em qualquer região, independentemente do local da sua conta da Automação.

3. Na página da VM, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

4. Você precisa ter a permissão `Microsoft.OperationalInsights/workspaces/read` para determinar se a VM está habilitada para um workspace. Para saber mais sobre as permissões adicionais necessárias, confira [Permissões de configuração de recurso](automation-role-based-access-control.md#feature-setup-permissions). Para saber como habilitar vários computadores de uma só vez, confira [Habilitar Controle de Alterações e Inventário em uma conta da Automação](automation-enable-changes-from-auto-acct.md).

5. Escolha o workspace do Log Analytics e a conta da Atomação e clique em **Habilitar** para habilitar o Controle de Alterações e Inventário na VM. A instalação leva até 15 minutos para ser concluída. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verificar a configuração de escopo

O Controle de Alterações e Inventário usa uma configuração de escopo dentro do workspace a fim de definir os computadores a serem habilitados para o recurso. A configuração de escopo é um grupo de uma ou mais pesquisas salvas utilizadas para limitar o escopo do recurso a computadores específicos. Para obter mais informações, confira [Trabalhar com configurações de escopo para o Controle de Alterações e Inventário](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes do trabalho com o recurso, confira [Gerenciar Controle de Alterações e Inventário](change-tracking-file-contents.md).
* Para obter mais informações sobre as configurações de escopo, confira [Trabalhar com configurações de escopo para Controle de Alterações e Inventário](automation-scope-configurations-change-tracking.md).
* Para saber como usar o recurso para identificar o software instalado em seu ambiente, confira [Descobrir qual software está instalado em suas VMs](automation-tutorial-installed-software.md).
* Se você não quiser integrar sua conta de Automação a um workspace do Log Analytics ao habilitar o recurso, confira [Desvincular o workspace da conta de Automação](automation-unlink-workspace-change-tracking.md).
* Ao concluir a implantação de alterações nas VMs, você pode removê-las conforme descrito em [Remover VMs do Controle de Alterações e Inventário](automation-remove-vms-from-change-tracking.md).
* Para solucionar problemas gerais com o recurso, confira [Solucionar problemas de Controle de Alterações e Inventário](troubleshoot/change-tracking.md).
