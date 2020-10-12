---
title: Remover VMs do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo informa como remover computadores gerenciados com o Gerenciamento de Atualizações.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89648635"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs do Gerenciamento de Atualizações

Ao concluir o gerenciamento de atualizações em suas VMs em seu ambiente, você pode parar de gerenciar VMs com o recurso [Gerenciamento de atualizações](update-mgmt-overview.md) . Para parar de gerenciá-los, você editará a consulta de pesquisa salva `MicrosoftDefaultComputerGroup` em seu espaço de trabalho do log Analytics que está vinculado à sua conta de automação.

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover suas VMs

1. Na portal do Azure, inicie o **Cloud Shell** na navegação superior da portal do Azure. Se você não estiver familiarizado com Azure Cloud Shell, consulte [visão geral do Azure cloud Shell](../../cloud-shell/overview.md).

2. Use o comando a seguir para identificar o UUID de um computador que você deseja remover do gerenciamento do.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Na portal do Azure, navegue até **espaços de trabalho do log Analytics**. Selecione o workspace da lista.

4. No espaço de trabalho Log Analytics, selecione **logs** e, em seguida, escolha **Gerenciador de consultas** no menu de ações superior.

5. No **Gerenciador de consultas** no painel direito, expanda **Queries\Updates salvas** e selecione a consulta de pesquisa salva `MicrosoftDefaultComputerGroup` para editá-lo.

6. No editor de consultas, examine a consulta e localize o UUID para a VM. Remova o UUID para a VM e repita as etapas para todas as outras VMs que você deseja remover.

7. Salve a pesquisa salva quando tiver terminado de editá-la selecionando **salvar** na barra superior.

>[!NOTE]
>Os computadores ainda serão mostrados depois que você tiver cancelado o registro porque relatamos em todos os computadores avaliados nas últimas 24 horas. Depois de remover a máquina, você precisa aguardar 24 horas antes que elas não sejam mais listadas.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar o gerenciamento de sua máquina virtual, consulte [habilitar gerenciamento de atualizações navegando na portal do Azure](update-mgmt-enable-portal.md) ou [habilitando gerenciamento de atualizações de uma VM do Azure](update-mgmt-enable-vm.md).