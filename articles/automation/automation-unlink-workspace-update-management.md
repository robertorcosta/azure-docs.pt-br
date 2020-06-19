---
title: Desvincular o workspace da conta da Automação para o Gerenciamento de Atualizações
description: Este artigo explica como desvincular um workspace do Log Analytics da conta da Automação para o Gerenciamento de Atualizações
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835777"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Desvincular o workspace da conta da Automação para o Gerenciamento de Atualizações

Você pode optar por não integrar sua conta da Automação a um workspace do Log Analytics durante as operações do [Gerenciamento de Atualizações](automation-update-management.md). Este artigo explica como desvincular o workspace de sua conta.

1. Entre no Azure em https://portal.azure.com.

2. Remova o Gerenciamento de Atualizações de suas VMs. Confira [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).

3. Se o Gerenciamento de Atualizações incluir versões anteriores do monitoramento SQL do Azure, a configuração do recurso pode ter criado ativos de automação que você deve remover. Para o Gerenciamento de Atualizações, convém remover os seguintes itens que não são mais necessários:

   * Atualizar agendamentos - cada um tem um nome que corresponde à implantação de atualização que você criou.
   * Grupos de trabalho híbridos criados para o Gerenciamento de Atualizações - cada um recebe um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos Relacionados** à esquerda.

5. Na página Desvincular workspace, clique em **Desvincular workspace** e responda às solicitações.

   ![Página Desvincular workspace](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Enquanto a Automação do Azure tenta desvincular o workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

Como alternativa, você pode desvincular o workspace do Log Analytics da sua conta da Automação no workspace:

1. No seu workspace, selecione **Conta da Automação** em **Recursos Relacionados**. 
2. Na página Conta da Automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o recurso, confira [Gerenciar atualizações e patches para as VMs do Azure](automation-tutorial-update-management.md).
* Para solucionar erros de recursos, confira [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar erros do agente de atualização do Windows, confira [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar erros do agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
