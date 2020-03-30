---
title: Crie um grupo de colocação de proximidade usando o portal
description: Aprenda a criar um grupo de colocação de proximidade usando o portal Azure.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180245"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Crie um grupo de colocação de proximidade usando o portal

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um [grupo de colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados perto um do outro. Grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Crie o grupo de colocação de proximidade

1. Digite **o grupo de colocação de proximidade** na pesquisa.
1. Em **Serviços** nos resultados da pesquisa, selecione **grupos de colocação de proximidade**.
1. Na página **Grupos de colocação de proximidade,** selecione **Adicionar**.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. No **grupo Resource,** selecione **Criar um novo** para criar um novo grupo ou selecionar um grupo de recursos existente a partir da queda.
1. Em **Região** selecione o local onde deseja que o grupo de colocação de proximidade seja criado.
1. Em **Nome do grupo de colocação Proximity** digite um nome e, em seguida, selecione **'Revisar + criar**.
1. Após a validação passar, selecione **Criar** para criar o grupo de colocação de proximidade.

    ![Captura de tela de criação de um grupo de colocação de proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Ao criar uma VM no portal, vá para a guia **Avançado.** 
1. Na seleção do **grupo de colocação De proximidade,** selecione o grupo de colocação correto. 

    ![Captura de tela da seção grupo de colocação de proximidade ao criar uma nova VM no portal](./media/ppg/vm-ppg.png)

1. Quando terminar de fazer todas as outras seleções necessárias, selecione **'Revisar + criar '''**
1. Depois de passar na validação, selecione **Criar** para implantar a VM no grupo de colocação.




## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de colocação de proximidade.

