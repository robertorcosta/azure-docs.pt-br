---
title: 'Quickstart: Crie o serviço VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como criar o serviço CloudSimple, comprar nós e reservas
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024425"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Quickstart - Crie a solução Azure VMware pelo serviço CloudSimple

Para começar, crie a Solução Azure VMware pelo CloudSimple no portal Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solução VMware por CloudSimple - Visão geral do serviço

O serviço CloudSimple permite que você consuma a Solução VMware do CloudSimple.  A criação do serviço permite que você prover nódulos, reservas e criar nuvens privadas.  Você adiciona o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de borda do Azure VMware Solution by CloudSimple.  Essa rede de borda é usada para serviços que incluem VPN, ExpressRoute e conectividade com a Internet com suas nuvens privadas.

Para adicionar o serviço CloudSimple, você deve criar uma sub-rede de gateway. A sub-rede gateway é usada ao criar a rede de borda e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Ele não pode se sobrepor a nenhum dos espaços de endereço situoto da rede local ou ao espaço de endereço da rede virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco /28 CIDR para a sub-rede gateway.  Uma sub-rede gateway é necessária por serviço CloudSimple e é exclusiva da região em que é criada. A sub-rede gateway é usada para a Solução Azure VMware por serviços de rede cloudSimple edge e requer um bloco DE R$ 28. O espaço de endereço da sub-rede gateway deve ser único. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente CloudSimple.  As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais Do Zure.

Revisar [pré-requisitos de rede](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure por **Serviço CloudSimple**.

    ![Pesquisar serviço cloudsimple](media/create-cloudsimple-service-search.png)

3. Selecione **Serviços cloudsimple**.
4. Clique **em Adicionar** para criar um novo serviço.

    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a assinatura onde deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique **em Criar novo**.
7. Digite o nome para identificar o serviço.
8. Digite o CIDR para o gateway de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma de suas sub-redes no local, sub-redes Azure ou sub-redes CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="provision-nodes"></a>Provisionar nós

Para configurar a capacidade de pagamento para um ambiente CloudSimple Private Cloud, primeiro provisionar os nós no portal Azure.

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

* [Criar nuvem privada e configurar ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [o serviço CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
