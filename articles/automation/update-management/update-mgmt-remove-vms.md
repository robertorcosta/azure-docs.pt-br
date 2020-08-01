---
title: Remover VMs do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo informa como remover computadores gerenciados com o Gerenciamento de Atualizações.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449851"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs do Gerenciamento de Atualizações

Ao concluir o gerenciamento de atualizações em suas VMs em seu ambiente, você pode parar de gerenciar VMs com o recurso [Gerenciamento de atualizações](update-mgmt-overview.md) .

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

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

Para reabilitar o gerenciamento de sua máquina virtual, consulte [habilitar gerenciamento de atualizações navegando na portal do Azure](update-mgmt-enable-portal.md) ou [habilitando gerenciamento de atualizações de uma VM do Azure](update-mgmt-enable-vm.md).