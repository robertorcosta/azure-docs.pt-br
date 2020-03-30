---
title: Excluir álos para solução VMware por CloudSimple - Azure
description: Saiba como excluir nomes do seu VMWare com a implantação do CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024731"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Exclua os álos da solução Azure VMware pelo CloudSimple

Os nós CloudSimple são medidos assim que são criados.  Os nódulos devem ser excluídos para parar a medição dos nódulos.  Você exclui os nós que não são usados no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser excluído as seguintes condições:

* Uma nuvem privada criada com os nós é excluída.  Para excluir uma nuvem privada, consulte [Excluir uma solução Azure VMware pela CloudSimple Private Cloud](delete-private-cloud.md).
* O nó foi removido da Nuvem Privada encolhendo a Nuvem Privada.  Para encolher uma nuvem privada, consulte [Shrink Azure VMware Solution by CloudSimple Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Excluir nó CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **nódulos CloudSimple**.

   ![Pesquisar emvocês simples na Nuvem](media/create-cloudsimple-node-search.png)

3. Selecione **'Nós simples' do Cloud**.

4. Selecione nós que não pertencem a uma Nuvem Privada para excluir.  **A** coluna PRIVATE CLOUD NAME mostra o nome Private Cloud ao qual um nó pertence.  Se um nó não for usado por uma Nuvem Privada, o valor estará vazio. 

    ![Selecione nodos cloudsimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Somente nós que não fazem parte da Nuvem Privada podem ser excluídos.

## <a name="next-steps"></a>Próximas etapas

* Conheça [o Private Cloud](cloudsimple-private-cloud.md)
