---
title: Desvincular workspace da conta da Automação para o Controle de Alterações e o Inventário
description: Este artigo informa como desvincular um workspace do Log Analytics da conta da Automação para o Controle de Alterações e o Inventário
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828246"
---
# <a name="unlink-workspace-from-automation-account"></a>Desvincular o workspace da conta de Automação

Você pode optar por não integrar sua conta da Automação com um workspace do Log Analytics ao habilitar as operações de [Controle de Alterações e Inventário](change-tracking.md). Este artigo explica como desvincular o workspace da sua conta.

1. Entre no Azure em https://portal.azure.com.

2. Remova o Gerenciamento de Atualizações das suas VMs. Consulte [Remover VMs do Controle de Alterações e do Inventário](automation-remove-vms-from-change-tracking.md).

3. Se o Controle de Alterações e Inventário incluir versões anteriores do monitoramento SQL do Azure, a configuração do recurso pode ter criado ativos da Automação que você deve remover. Localize e remova esses ativos.

4. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos relacionados** à esquerda.

5. Na página Desvincular workspace, clique em **Desvincular workspace** e responda às solicitações.

   ![Página Desvincular workspace](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Enquanto a Automação do Azure tenta desvincular o workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

Como alternativa, você pode desvincular o workspace do Log Analytics da sua conta da Automação no workspace:

1. No seu workspace, selecione **Conta da Automação** em **Recursos relacionados** . 
2. Na página Conta da Automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o Controle de Alterações e o Inventário, consulte [Gerenciar o Controle de Alterações e o Inventário](change-tracking-file-contents.md).
* Para solucionar problemas gerais de recurso, consulte [Solucionar problemas do Controle de Alterações e do Inventário](troubleshoot/change-tracking.md).
