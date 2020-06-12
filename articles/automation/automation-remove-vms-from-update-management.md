---
title: Remover VMs do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo mostra como remover VMs do Gerenciamento de Atualizações.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836644"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs do Gerenciamento de Atualizações

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

Quando terminar de implantar atualizações nas VMs em seu ambiente, você poderá removê-las do recurso [Gerenciamento de Atualizações](automation-update-management.md).

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Use o comando a seguir para identificar a UUID de uma VM que você deseja remover do gerenciamento.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No workspace do Log Analytics, em **Geral**, acesse as pesquisas salvas.

4. Para a pesquisa salva `MicrosoftDefaultComputerGroup`, clique nas reticências à direita e selecione **Editar**. 

5. Remova a UUID da VM.

6. Repita as etapas para todas as outras VMs a serem removidas.

7. Salve a pesquisa quando tiver terminado de editá-la. 

## <a name="next-steps"></a>Próximas etapas

* Para continuar trabalhando com o Gerenciamento de Atualizações, confira [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de recursos, confira [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para problemas com o Windows Update Agent, confira [Solucionar problemas do Windows Update Agent](troubleshoot/update-agent-issues.md).
* Para problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).