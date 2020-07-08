---
title: Remover VMs do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo informa como remover computadores gerenciados com o Gerenciamento de Atualizações.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610040"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs do Gerenciamento de Atualizações

Quando terminar de implantar atualizações nas VMs em seu ambiente, você poderá removê-las do recurso [Gerenciamento de Atualizações](automation-update-management.md).

## <a name="to-remove-your-vms"></a>Para remover suas VMs

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Use o comando a seguir para identificar o UUID de um computador que você deseja remover do gerenciamento do.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Em seu espaço de trabalho do Log Analytics em **geral**, acesse as pesquisas salvas para a configuração do escopo `MicrosoftDefaultScopeConfig-Updates` .

4. Para a pesquisa salva `MicrosoftDefaultComputerGroup`, clique nas reticências à direita e selecione **Editar**.

5. Remova a UUID da VM.

6. Repita as etapas para todas as outras VMs a serem removidas.

7. Salve a pesquisa salva quando tiver terminado de editá-la.

>[!NOTE]
>Os computadores ainda serão mostrados depois que você tiver cancelado o registro porque relatamos em todos os computadores avaliados nas últimas 24 horas. Depois de desconectar o computador, você precisa aguardar 24 horas antes que eles não sejam mais listados.

## <a name="next-steps"></a>Próximas etapas

* Para continuar trabalhando com o Gerenciamento de Atualizações, confira [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de recursos, confira [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para problemas com o Windows Update Agent, confira [Solucionar problemas do Windows Update Agent](troubleshoot/update-agent-issues.md).
* Para problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).