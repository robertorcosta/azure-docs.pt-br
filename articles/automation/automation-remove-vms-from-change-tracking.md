---
title: Remoção de VMs dos recursos Controle de Alterações e Inventário da Automação do Azure
description: Este artigo descreve como remover as VMs dos Controle de Alterações e Inventário.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84169447"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controle de Alterações e do Inventário

Quando terminar de implantar atualizações nas VMs em seu ambiente, remova-as dos recursos [Controle de Alterações e Inventário](change-tracking.md).

## <a name="to-remove-your-vms"></a>Para remover suas VMs

1. Na sua conta de Automação, selecione **Controle de Alterações** ou **Inventário** em **Gerenciamento de Configuração**.

2. Use o comando a seguir para identificar a UUID de uma VM que você quer remover do gerenciamento.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Em seu espaço de trabalho do Log Analytics em **geral**, acesse as pesquisas salvas para a configuração do escopo `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Para a pesquisa salva `MicrosoftDefaultComputerGroup`, clique nas reticências à direita e selecione **Editar**. 

5. Remova a UUID da VM.

6. Repita as etapas para todas as outras VMs a serem removidas.

7. Salve a pesquisa salva quando tiver terminado de editá-la. 

## <a name="next-steps"></a>Próximas etapas

* Para continuar trabalhando com os recursos Controle de Alterações e Inventário, consulte [Gerenciar o Controle de Alterações e o Inventário](change-tracking-file-contents.md).
* Para resolver problemas gerais com recursos, veja [Solução de problemas do Controle de Alterações e do Inventário](troubleshoot/change-tracking.md).