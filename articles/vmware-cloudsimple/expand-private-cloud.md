---
title: Expanda a nuvem privada do Azure VMware Solutions (AVS)
description: Descreve como expandir uma nuvem privada de AVS existente para adicionar capacidade em um cluster novo ou existente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025292"
---
# <a name="expand-an-avs-private-cloud"></a>Expanda uma nuvem privada de AVS

A AVS fornece a flexibilidade para expandir dinamicamente uma nuvem privada de AVS. Você pode começar com uma configuração menor e, em seguida, expandir conforme precisar de maior capacidade. Ou você pode criar uma nuvem privada de AVS com base nas necessidades atuais e expandir conforme o consumo cresce.

Uma nuvem privada de AVS consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Ao expandir uma nuvem privada de AVS, você adiciona nós ao cluster existente ou cria um novo cluster. Para expandir um cluster existente, nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre os limites de nuvem privada da AVS, consulte a seção limites no artigo [visão geral da nuvem privada da AVS](cloudsimple-private-cloud.md) .

Uma nuvem privada de AVS é criada com um **datacenter** padrão no vCenter. Cada datacenter serve como uma entidade de gerenciamento de nível superior. Para um novo cluster, a AVS fornece a opção de adicionar ao datacenter existente ou criar um novo datacenter.

Como parte da nova configuração de cluster, a AVS configura a infra-estrutura do VMware. As configurações incluem configurações de armazenamento para grupos de discos vSAN, alta disponibilidade do VMware e Distributed Resource Scheduler (DRS).

Uma nuvem privada de AVS pode ser expandida várias vezes. A expansão só pode ser feita quando você permanece dentro dos limites de nó gerais. Cada vez que você expande uma nuvem privada de AVS, você adiciona ao cluster existente ou cria um novo.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados para que você possa expandir sua nuvem privada de AVS. Para obter mais informações sobre os nós de provisionamento, consulte o artigo [provisionar nós para solução VMware por AVS – Azure](create-nodes.md) . Para criar um novo cluster, você deve ter pelo menos três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Expanda uma nuvem privada de AVS

1. [Acesse o portal da AVS](access-cloudsimple-portal.md).

2. Abra a página **recursos** e selecione a nuvem privada AVS para a qual você deseja expandir.

3. Na seção Resumo, clique em **expandir**.

    ![Expandir a nuvem privada da AVS](media/resources-expand-private-cloud.png)

4. Escolha se deseja expandir o cluster existente ou criar um novo cluster vSphere. Conforme você faz alterações, as informações resumidas na página são atualizadas.

    * Para expandir o cluster existente, clique em **expandir cluster existente**. Selecione o cluster que você deseja expandir e insira o número de nós a serem adicionados. Cada cluster pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique em **criar novo cluster**. Insira um nome para o cluster. Selecione um datacenter existente ou insira um nome para criar um novo datacenter. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster vSphere, mas não ao expandir um cluster vSphere existente. Selecione o número de nós. Cada cluster novo deve ter pelo menos três nós.

    ![Expandir a nuvem privada da AVS-adicionar nós](media/resources-expand-private-cloud-add-nodes.png)

5. Clique em **Enviar** para expandir a nuvem privada da AVS.

## <a name="next-steps"></a>Próximos passos

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre as [nuvens privadas da AVS](cloudsimple-private-cloud.md)