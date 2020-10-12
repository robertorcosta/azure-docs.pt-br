---
title: Habilitar Controle de Alterações e Inventário da Automação do Azure por uma VM do Azure
description: Este artigo conta como habilitar o Controle de Alterações e Inventário por uma VM do Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5ff6e0ffd4040393a040dc67a511aab47887f6e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186258"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Habilitar Controle de Alterações e Inventário de uma VM do Azure

Este artigo descreve como você pode usar uma VM do Azure para habilitar o recurso de [Controle de Alterações e Inventário](change-tracking.md) em outras máquinas. Para habilitar VMs do Azure em escala, você deve habilitar uma VM existente usando o Controle de Alterações e Inventário. 

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](./index.yml) para gerenciar máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. No [portal do Azure](https://portal.azure.com), selecione **Máquinas virtuais** ou procure e selecione **Máquinas virtuais** na página inicial.

2. Selecione a VM para a qual você deseja habilitar o Controle de Alterações e Inventário. As VMs podem existir em qualquer região, independentemente do local da sua conta da Automação.

3. Na página da VM, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

4. Você precisa ter a permissão `Microsoft.OperationalInsights/workspaces/read` para determinar se a VM está habilitada para um workspace. Para saber mais sobre as permissões adicionais necessárias, confira [Permissões de configuração de recurso](automation-role-based-access-control.md#feature-setup-permissions). Para saber como habilitar vários computadores de uma só vez, confira [Habilitar Controle de Alterações e Inventário em uma conta da Automação](automation-enable-changes-from-auto-acct.md).

5. Escolha o workspace do Log Analytics e a conta da Atomação e clique em **Habilitar** para habilitar o Controle de Alterações e Inventário na VM. A instalação leva até 15 minutos para ser concluída. 

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes do trabalho com o recurso, confira [Gerenciar Controle de Alterações e Inventário](change-tracking-file-contents.md).
* Para solucionar problemas gerais com o recurso, confira [Solucionar problemas de Controle de Alterações e Inventário](troubleshoot/change-tracking.md).
