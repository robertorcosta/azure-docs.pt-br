---
title: Expanda a solução Azure VMware por CloudSimple Private Cloud
description: Descreve como expandir uma nuvem privada cloudsimple existente para adicionar capacidade em um cluster existente ou novo
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025292"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expanda uma nuvem privada cloudsimple

O CloudSimple oferece flexibilidade para expandir dinamicamente uma Nuvem Privada. Você pode começar com uma configuração menor e, em seguida, expandir-se à medida que você precisa de maior capacidade. Ou você pode criar uma Nuvem Privada com base nas necessidades atuais e, em seguida, expandir à medida que o consumo cresce.

Uma Nuvem Privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nódulos.  Ao expandir uma Nuvem Privada, você adiciona nós ao cluster existente ou cria um novo cluster. Para expandir um cluster existente, os nós adicionais devem ser do mesmo tipo (SKU) que os nãos existentes. Para criar um novo cluster, os nódulos podem ser de um tipo diferente. Para obter mais informações sobre os limites da Nuvem Privada, consulte a seção limites no artigo [visão geral da nuvem privada CloudSimple.](cloudsimple-private-cloud.md)

Uma nuvem privada é criada com um **Datacenter** padrão no vCenter.  Cada datacenter serve como uma entidade de gerenciamento de alto nível.  Para um novo cluster, o CloudSimple oferece a opção de adicionar ao data center existente ou criar um novo data center.

Como parte da nova configuração de cluster, o CloudSimple configura a infra-estrutura VMware.  As configurações incluem configurações de armazenamento para grupos de disco vSAN, VMware High Availability e DISTRIBUTED Resource Scheduler (DRS).

Uma Nuvem Privada pode ser expandida várias vezes. A expansão só pode ser feita quando você ficar dentro dos limites gerais do nó. Cada vez que você expande uma Nuvem Privada, você adiciona ao cluster existente ou cria um novo.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa expandir sua Nuvem Privada.  Para obter mais informações sobre nós de provisionamento, consulte [nós de provisão para solução VMware por artigo do CloudSimple - Azure.](create-nodes.md)  Para criar um novo cluster, você deve ter pelo menos três nódulos disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandir uma nuvem privada

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos** e selecione a Nuvem Privada para a qual você deseja expandir.

3. Na seção resumo, clique em **Expandir**.

    ![Expandir nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se deve expandir seu cluster existente ou criar um novo cluster vSphere. À medida que você faz alterações, as informações de resumo na página são atualizadas.

    * Para expandir seu cluster existente, clique em **Expandir o cluster existente**. Selecione o cluster que deseja expandir e digite o número de nós a serem adicionados. Cada cluster pode ter um máximo de 16 nódulos.
    * Para adicionar um novo cluster, clique **em Criar novo cluster**. Insira um nome para o cluster. Selecione um data center existente ou digite um nome para criar um novo data center. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster vSphere, mas não ao expandir um cluster vSphere existente. Selecione o número de nós. Cada novo cluster deve ter pelo menos três nódulos.

    ![Expandir nuvem privada - adicionar nodes](media/resources-expand-private-cloud-add-nodes.png)

5. Clique **em Enviar** para expandir a nuvem privada.

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)