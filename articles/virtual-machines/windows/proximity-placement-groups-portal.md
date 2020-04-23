---
title: Criar um grupo de posicionamento de proximidade usando o portal
description: Saiba como criar um grupo de posicionamento de proximidade usando o portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098638"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Criar um grupo de posicionamento de proximidade usando o portal

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](co-location.md#proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento de proximidade

1. Digite **grupo de posicionamento de proximidade** na pesquisa.
1. Em **Serviços** nos resultados da pesquisa, selecione **grupos de posicionamento de proximidade**.
1. Na página **grupos de posicionamento de proximidade** , selecione **Adicionar**.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Em **grupo de recursos** , selecione **criar novo** para criar um novo grupo ou selecione um grupo de recursos existente na lista suspensa.
1. Em **região** , selecione o local onde você deseja que o grupo de posicionamento de proximidade seja criado.
1. Em **proximidade posição do grupo nome** , digite um nome e, em seguida, selecione **revisar + criar**.
1. Depois que a validação for aprovada, selecione **criar** para criar o grupo de posicionamento de proximidade.

    ![Captura de tela da criação de um grupo de posicionamento de proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Ao criar uma VM no portal, vá para a guia **avançado** . 
1. Na seleção de **grupo de posicionamento de proximidade** , selecione o grupo de posicionamento correto. 

    ![Captura de tela da seção grupo de posicionamento de proximidade ao criar uma nova VM no portal](./media/ppg/vm-ppg.png)

1. Quando terminar de fazer todas as outras seleções necessárias, selecione **revisar + criar**.
1. Depois de passar na validação, selecione **criar** para implantar a VM no grupo de posicionamento.




## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de posicionamento de proximidade.

