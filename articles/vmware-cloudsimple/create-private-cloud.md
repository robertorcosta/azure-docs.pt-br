---
title: Soluções VMware do Azure (AVS)-criar nuvem privada de AVS
description: Descreve como criar uma nuvem privada de AVS para estender cargas de trabalho do VMware para a nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024782"
---
# <a name="create-an-avs-private-cloud"></a>Criar uma nuvem privada de AVS

Uma nuvem privada de AVS é uma pilha VMware isolada que dá suporte a hosts ESXi, vCenter, vSAN e NSX. As nuvens privadas da AVS são gerenciadas por meio do portal da AVS. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

A criação de uma nuvem privada de AVS ajuda a lidar com uma variedade de necessidades comuns de infraestrutura de rede:

* **Crescimento**. Se você atingiu um ponto de atualização de hardware para sua infraestrutura existente, uma nuvem privada de AVS permite que você expanda sem nenhum novo investimento de hardware necessário.

* **Expansão rápida**. Se alguma necessidade de capacidade temporária ou não planejada ocorrer, uma nuvem privada da AVS permitirá que você crie a capacidade adicional sem atraso.

* **Maior proteção**. Com uma nuvem privada de AVS de três ou mais nós, você obtém redundância automática e proteção de alta disponibilidade.

* **Necessidades de infraestrutura de longo prazo**. Se os data centers estiverem na capacidade ou se você quiser reestruturar para reduzir os custos, uma nuvem privada de AVS permitirá que você desative os data centers e migre para uma solução baseada em nuvem e, ao mesmo tempo, seja compatível com suas operações corporativas.

Ao criar uma nuvem privada de AVS, você obtém um único cluster vSphere e todas as VMs de gerenciamento criadas nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa criar sua nuvem privada de AVS. Para obter mais informações sobre os nós de provisionamento, consulte [provisionar nós para soluções VMware do Azure (AVS)](create-nodes.md).

Aloque um intervalo CIDR para sub-redes vSphere/vSAN para a nuvem privada da AVS. Uma nuvem privada de AVS é criada como um ambiente de pilha VMware isolado (com hosts ESXi, vCenter, vSAN e NSX) gerenciado por um vCenter Server. Os componentes de gerenciamento são implantados na rede selecionada para o CIDR de sub-redes vSphere/vSAN. O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação. O espaço de endereço de sub-rede vSphere/vSAN deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente da AVS. As redes que se comunicam com o AVS incluem redes locais e redes virtuais do Azure. Para obter mais informações sobre sub-redes vSphere/vSAN, consulte Visão geral de VLANs e sub-redes.

* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN mínima:/24
* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN máxima:/21


## <a name="access-the-avs-portal"></a>Acessar o portal da AVS

Acesse o [portal da AVS](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Criar uma nova nuvem privada de AVS

1. Selecione **Todos os serviços**.
2. Procure **serviços de sincronização automática**.
3. Selecione o serviço AVS no qual você deseja criar sua nuvem privada de AVS.
4. Em **visão geral**, clique em **criar nuvem privada de AVS** para abrir uma nova guia do navegador para o portal da AVS. Se solicitado, entre com suas credenciais de entrada do Azure.

    ![Criar uma nuvem privada de AVS do Azure](media/create-private-cloud-from-azure.png)

5. No portal da AVS, forneça um nome para sua nuvem privada de AVS.
6. Selecione o **local** para sua nuvem privada de AVS.
7. Selecione o **tipo de nó**, consistente com o que você comprou no Azure.
8. Especifique a **contagem de nós**. Pelo menos três nós são necessários para criar uma nuvem privada de AVS.
5. No portal do CloudSimple, forneça um nome para sua nuvem privada.
6. Selecione o **local** para sua nuvem privada.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**.  Pelo menos três nós são necessários para criar uma nuvem privada.

    ![Criar uma nuvem privada de AVS-informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até nove nós, um intervalo CIDR/23 dá suporte a até 41 nós, e um intervalo CIDR/22 e/21 dá suporte a até 64 nós (o número máximo de nós em uma nuvem privada de AVS).

    > [!IMPORTANT]
    > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada da AVS. Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

O processo de provisionamento da nuvem privada AVS é iniciado. Pode levar até duas horas para que a nuvem privada de AVS seja provisionada.

Para obter instruções sobre como expandir uma nuvem privada de AVS existente, consulte [expandir uma nuvem privada de AVS](expand-private-cloud.md).
