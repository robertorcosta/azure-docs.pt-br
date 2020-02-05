---
title: Início rápido de soluções do Azure VMware (AVS)-criar serviço
description: Saiba como criar o serviço AVS, nós de compra e nós de reserva
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024425"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Início rápido-criar serviço de soluções VMware do Azure (AVS)

Para começar, crie as soluções do Azure VMware (AVS) no portal do Azure.

## <a name="vmware-solutions-avs---service-overview"></a>Soluções VMware (AVS)-visão geral do serviço

O serviço AVS permite que você consuma a solução Azure VMware pela AVS. A criação do serviço permite provisionar nós, reservar nós e criar nuvens privadas de AVS. Você adiciona o serviço AVS em cada região do Azure onde o serviço AVS está disponível. O serviço define a rede de borda da solução do Azure VMware por AVS. Essa rede de borda é usada para serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas da AVS.

Para adicionar o serviço AVS, você deve criar uma sub-rede de gateway. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não pode se sobrepor a nenhum dos espaços de endereço de rede local nem do espaço de endereço de rede virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway. Uma sub-rede de gateway é necessária por serviço AVS e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada para a solução do Azure VMware por serviços de rede de borda de sincronização automática e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente da AVS. As redes que se comunicam com o AVS incluem redes locais e redes virtuais do Azure.

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Pesquise o **serviço AVS**.

    ![Pesquisar serviço AVS](media/create-cloudsimple-service-search.png)

3. Selecione **serviços de sincronização automática**.
4. Clique em **Adicionar** para criar um novo serviço.

    ![Adicionar serviço AVS](media/create-cloudsimple-service-add.png)

5. Selecione a assinatura na qual você deseja criar o serviço AVS.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede/28 que não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-redes de AVS planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço AVS](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="provision-nodes"></a>Provisionar nós

Para configurar a capacidade paga conforme o uso para um ambiente de nuvem privada da AVS, primeiro provisionar nós na portal do Azure.

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

* [Criar uma nuvem privada de AVS e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre o [serviço AVS](https://docs.azure.cloudsimple.com/cloudsimple-service)
