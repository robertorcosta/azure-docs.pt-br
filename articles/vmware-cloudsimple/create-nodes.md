---
title: Nó de provisão para solução VMware por CloudSimple - Azure
description: Saiba como adicionar nomes ao seu VMWare com a implantação do CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024799"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Nó de fornecimento para a solução Azure VMware por CloudSimple

Provisão de nódulos no portal Azure. Em seguida, você pode configurar a capacidade de pagamento à medida que você vai para o seu ambiente de nuvem privada CloudSimple.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Adicione um nó à sua nuvem privada CloudSimple

1. Selecione **Todos os serviços**.
2. Procure por **nódulos CloudSimple**.

   ![Pesquisar emvocês simples na Nuvem](media/create-cloudsimple-node-search.png)

3. Selecione **'Nós simples' do Cloud**.
4. Clique **em Adicionar** para criar nós.

    ![Adicionar nodos cloudsimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura onde deseja provisionar os nós CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique **em Criar novo**.
7. Digite o prefixo para identificar os nódulos.
8. Selecione o local para os recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós para provisão.
12. Selecione **Revisão + Criar**.
13. Revise as configurações. Para modificar quaisquer configurações, clique **em Previous**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

* [Criar nuvem privada](create-private-cloud.md)
