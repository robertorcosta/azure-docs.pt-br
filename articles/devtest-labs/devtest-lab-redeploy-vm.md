---
title: Reimplantar uma VM em um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como reimplantar uma máquina virtual (mover de um nó do Azure para outro) no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da0edf13adaa0d7ecd84ee2c190f376c19b398db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480228"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reimplantar uma VM em um laboratório no Azure DevTest Labs
Se você não puder se conectar a uma VM (máquina virtual) em um laboratório por meio de uma conexão de área de trabalho remota, reimplante a VM e tente se conectar a ela novamente. Quando você reimplanta uma VM, o DevTest Labs move a VM do nó no qual ela está em execução para um novo nó da infraestrutura do Azure. Em seguida, ele inicia a VM, mantendo todas as opções de configuração e os recursos associados. Esse recurso economiza o tempo gasto na solução de problemas de conexão de área de trabalho remota ou do acesso de aplicativo a VMs baseadas no Windows no laboratório. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Etapas para reimplantar uma VM em um laboratório 
Para reimplantar uma VM em um laboratório no Azure DevTest Labs, siga estas etapas: 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório que inclui a VM que você deseja reimplantar.  
4. No painel esquerdo, selecione **Minhas Máquinas Virtuais**. 
5. Na lista de VMs, selecione uma VM.
6. Na página Máquina Virtual de sua VM, selecione **Reimplantar**, em **OPERAÇÕES**, no menu esquerdo.

    ![Reimplantar](media/devtest-lab-redeploy-vm/redeploy.png)
7. Leia as informações na página e selecione o botão **Reimplantar**. 9. Verifique o status da operação de reimplantação na janela **Notificações**.

    ![Status da reimplantação](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Próximas etapas
Saiba como redimensionar uma VM no Azure DevTest Labs, confira [Redimensionar uma VM](devtest-lab-resize-vm.md).


