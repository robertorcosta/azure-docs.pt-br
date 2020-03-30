---
title: Garanta a alta disponibilidade do aplicativo ao ser executado no VMware no Azure
description: Descreve os recursos de alta disponibilidade do CloudSimple para lidar com cenários comuns de falha de aplicativos para aplicativos em execução em uma nuvem privada cloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025343"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garanta a alta disponibilidade do aplicativo ao ser executado no VMware no Azure

A solução CloudSimple oferece alta disponibilidade para seus aplicativos em execução no VMware no ambiente Azure. A tabela a seguir lista cenários de falha e os recursos de alta disponibilidade associados.

| Cenário de falha | Aplicativo protegido? | Recurso de Plataforma HA | Recurso VMware HA | Recurso Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Falha no Disco | YES | Substituição rápida do nó com falha | [Sobre a política de armazenamento padrão vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Falha do ventilador | YES | Ventiladores redundantes, substituição rápida de nó falho |  |  |
| Falha de NIC | YES | NIC redundante, substituição rápida do nó falho
| Falha de energia do host | YES | Fonte de alimentação redundante |  |  |
| Falha do host ESXi | YES | substituição rápida de nó falhou | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Falha em VM | YES | [Balanceadores de carga](load-balancers.md)  | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) | Balanceador de carga azure para VMs VMware apátridas |
| Falha da porta do switch de folha | YES | NIC redundante |  |  |
| Falha no switch de folha | YES | Interruptores de folha redundantes |  |  |
| Falha no rack | YES | Grupos de posicionamento |  |  |
| Conectividade de rede para DC no local | YES  | Serviços de rede redundantes |  | Circuitos ER redundantes |
| Conectividade de rede para o Azure | YES | |  | Circuitos ER redundantes |
| Falha no data center | YES |  |  | Zonas de disponibilidade |
| Fracasso Regional | YES  |  |  | Regiões do Azure |

A solução Azure VMware by CloudSimple fornece os seguintes recursos de alta disponibilidade.

## <a name="fast-replacement-of-failed-node"></a>Substituição rápida do nó com falha

O software do plano de controle CloudSimple monitora continuamente a saúde dos clusters VMware e detecta quando um nó ESXi falha. Em seguida, adiciona automaticamente um novo host ESXi ao cluster VMware afetado a partir de seu pool de nódulos prontamente disponíveis e tira o nó com falha do cluster. Essa funcionalidade garante que a capacidade de reposição no cluster VMware seja restaurada rapidamente para que a resiliência do cluster fornecida pelo vSAN e vMware HA seja restaurada.

## <a name="placement-groups"></a>Grupos de posicionamento

Um usuário que cria uma Nuvem Privada pode selecionar uma região Azure e um grupo de colocação dentro da região selecionada. Um grupo de colocação é um conjunto de nós espalhados por vários racks, mas dentro do mesmo segmento de rede de coluna. Os nódulos dentro do mesmo grupo de colocação podem chegar uns aos outros com um máximo de dois saltos extras de switch. Um grupo de colocação está sempre dentro de uma única zona de disponibilidade do Azure e abrange vários racks. O plano de controle CloudSimple distribui os nós de uma Nuvem Privada em vários racks com base no melhor esforço. Os nós em diferentes grupos de colocação são garantidos para serem colocados em diferentes racks.

## <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege seus aplicativos e dados contra falhas no data center. As zonas de disponibilidade são locais físicos especiais dentro de uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Cada região tem uma zona de disponibilidade. Para obter mais informações, consulte [Quais são as zonas de disponibilidade no Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Circuitos Redundantes Azure ExpressRoute

A conectividade do data center ao Azure vNet usando o ExpressRoute tem circuitos redundantes para fornecer um link de conectividade de rede altamente disponível.

## <a name="redundant-networking-services"></a>Serviços de rede redundantes

Todos os serviços de rede CloudSimple para a Nuvem Privada (incluindo VLAN, firewall, endereços IP públicos, Internet e VPN) foram projetados para estarem altamente disponíveis e capazes de suportar o SLA de serviço.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Balanceador de carga azure layer 7 para VMs VMware apátridas

Os usuários podem colocar um Balanceador de carga Azure Layer 7 na frente das VMs de nível web apátridas em execução no ambiente VMware para obter alta disponibilidade para o nível web.

## <a name="azure-regions"></a>Regiões do Azure

Uma região do Azure é um conjunto de data centers implantados dentro de um perímetro definido por latência e conectados por meio de uma rede regional dedicada de baixa latência. Para obter detalhes, consulte [Azure Regions](https://azure.microsoft.com/global-infrastructure/regions).
