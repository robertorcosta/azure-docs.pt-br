---
title: Provisionar nós para soluções VMware (AVS) – Azure
description: Saiba como adicionar nós ao seu VMWare com a implantação da AVS
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024799"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Provisionar nós para soluções VMware do Azure (AVS)

Provisionar nós no portal do Azure. Em seguida, você pode configurar a capacidade paga conforme o uso para seu ambiente de nuvem privada da AVS.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Adicionar um nó à sua nuvem privada de AVS

1. Selecione **Todos os serviços**.
2. Pesquise **nós AVS**.

   ![Pesquisar nós de AVS](media/create-cloudsimple-node-search.png)

3. Selecione **nós AVS**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nós AVS](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura em que você deseja provisionar nós AVS.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local dos recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós a serem provisionados.
12. Selecione **Examinar + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximos passos

* [Criar uma nuvem privada de AVS](create-private-cloud.md)
