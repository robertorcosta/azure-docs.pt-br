---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir de uma VM do Azure
description: Este artigo informa como habilitar o Gerenciamento de Atualizações a partir de uma VM do Azure.
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327775"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Habilitar o Gerenciamento de Atualizações de uma VM do Azure

Este artigo descreve como você pode usar uma VM do Azure para habilitar o recurso de [Gerenciamento de Atualizações](update-mgmt-overview.md) em outras máquinas. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Gerenciamento de Atualizações.

> [!NOTE]
> Quando habilitamos o Gerenciamento de Atualizações, somente determinadas regiões têm suporte para a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento com suporte, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../index.yml) para gerenciar máquinas.
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Habilitar o recurso para implantação

1. No [portal do Azure](https://portal.azure.com), selecione **Máquinas virtuais** ou procure e selecione **Máquinas virtuais** na página inicial.

2. Selecione a VM para a qual você deseja habilitar o Gerenciamento de Atualizações. As VMs podem existir em qualquer região, independentemente do local da sua conta da Automação. Você

3. Na página VM, em **operações**, selecione **convidado + atualizações de host**.

    ![Selecione convidado + atualizações de host no painel esquerdo](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. Você precisa ter a permissão `Microsoft.OperationalInsights/workspaces/read` para determinar se a VM está habilitada para um espaço de trabalho. Para saber mais sobre as permissões adicionais que são necessárias, confira [Permissões necessárias para habilitar os computadores](../automation-role-based-access-control.md#feature-setup-permissions). Para saber como habilitar vários computadores de uma só vez, consulte [Habilitar Gerenciamento de Atualizações a partir de uma conta da Automação](update-mgmt-enable-automation-account.md).

5. Na página habilitar Gerenciamento de Atualizações, escolha o espaço de trabalho Log Analytics e a conta de automação e clique em **habilitar** para habilitar o gerenciamento de atualizações. Depois de habilitar Gerenciamento de Atualizações, pode levar cerca de 15 minutos para que você possa exibir a avaliação de atualização da VM.

    ![Habilitar Gerenciamento de Atualizações](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>Próximas etapas

* Para usar o Gerenciamento de Atualizações para VMs, confira [Gerenciar atualizações e patches para suas VMs do Azure](update-mgmt-manage-updates-for-vm.md).

* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](../troubleshoot/update-management.md).
