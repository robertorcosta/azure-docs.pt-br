---
title: Excluir nós para soluções VMware (AVS) – Azure
description: Saiba como excluir nós de seu VMWare com a implantação da AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024731"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Excluir nós da solução do Azure VMware por AVS

Os nós da AVS são medidos assim que são criados. Os nós devem ser excluídos para interromper a medição dos nós. Você exclui os nós que não são usados no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser excluído sob as seguintes condições:

* Uma nuvem privada de AVS criada com os nós é excluída. Para excluir uma nuvem privada de AVS, consulte [excluir uma solução do Azure VMware por uma nuvem privada de AVS](delete-private-cloud.md).
* O nó foi removido da nuvem privada da AVS, reduzindo a nuvem privada da AVS. Para reduzir uma nuvem privada de AVS, consulte [reduzir a solução do Azure VMware por uma nuvem privada de AVS](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Excluir nó AVS

1. Selecione **Todos os serviços**.

2. Pesquise **nós AVS**.

   ![Pesquisar nós de AVS](media/create-cloudsimple-node-search.png)

3. Selecione **nós AVS**.

4. Selecione os nós que não pertencem a uma nuvem privada de AVS para excluir. A coluna de **nome da nuvem privada AVS** mostra o nome da nuvem privada AVS ao qual um nó pertence. Se um nó não for usado por uma nuvem privada de AVS, o valor estará vazio. 

    ![Selecionar nós AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Somente os nós que não fazem parte da nuvem privada da AVS podem ser excluídos.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a [nuvem privada da AVS](cloudsimple-private-cloud.md)
