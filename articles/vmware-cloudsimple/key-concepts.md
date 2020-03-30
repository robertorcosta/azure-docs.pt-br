---
title: Principais conceitos para administrar a Solução Azure VMware pela CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve conceitos-chave para administrar as soluções Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564612"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Principais conceitos para administração de Soluções Azure VMware pela CloudSimple

Administrar as soluções Azure VMware pela CloudSimple requer uma compreensão dos seguintes conceitos:

* Serviço CloudSimple, que é exibido como Azure VMware Solutions by CloudSimple - Service
* Nó CloudSimple, que é exibido como Azure VMware Solutions by CloudSimple - Node
* NuvemNuvemSimples nuvem privada
* Rede de serviços
* CloudSimple máquina virtual, que é exibida como Azure VMware Solutions por CloudSimple - Máquina virtual

## <a name="cloudsimple-service"></a>Serviço CloudSimple

Com o serviço CloudSimple, você pode criar e gerenciar todos os recursos associados às Soluções VMware pela CloudSimple a partir do portal Azure. Crie um recurso de serviço em todas as regiões onde você pretende usar o serviço.

Saiba mais sobre o [serviço CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nó CloudSimple

Um nó CloudSimple é um host de computação e armazenamento dedicado, de metal nu e hiperconvergente no qual o hipervisor VMware ESXi é implantado. Esse nó é então incorporado nas plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de rede CloudSimple e edge networking também estão habilitados. Cada nó serve como uma unidade de computação e capacidade de armazenamento que você pode provisão para criar [nuvens privadas CloudSimple](cloudsimple-private-cloud.md). Você disponibiliza ou reserva de nós em uma região onde o serviço CloudSimple está disponível.

Saiba mais sobre nós [do CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>NuvemNuvemSimples nuvem privada

Uma nuvem privada CloudSimple é um ambiente isolado de pilha vMware gerenciado por um servidor vCenter em seu próprio domínio de gerenciamento. A pilha VMware inclui hosts ESXi, vSphere, vCenter, vSAN e NSX. A pilha é executada em nós dedicados (hardware dedicado e isolado de metal nu) e é consumida pelos usuários através de ferramentas vMware nativas que incluem vCenter e NSX Manager. Nós dedicados são implantados em locais do Azure e são gerenciados pelo Azure. Cada nuvem privada pode ser segmentada e protegida usando serviços de rede, como VLANs e sub-redes e tabelas de firewall. As conexões com seu ambiente local e a rede Azure são criadas usando conexões VPN seguras e privadas e azure ExpressRoute.

Saiba mais sobre [cloudSimple private cloud](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviços

O serviço CloudSimple fornece uma rede por região onde seu serviço CloudSimple é implantado. A rede é um único espaço de endereço TCP Layer 3 com roteamento ativado por padrão. Todas as nuvens privadas e sub-redes criadas nesta região se comunicam entre si sem qualquer configuração adicional. Você cria grupos de portas distribuídas no vCenter usando as VLANs. Você pode usar os seguintes recursos de rede para configurar e proteger seus recursos de carga de trabalho em sua nuvem privada:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Conexão de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual CloudSimple

Com o serviço CloudSimple, você pode gerenciar máquinas virtuais VMware a partir do portal Azure. Um ou mais clusters ou pools de recursos do seu ambiente vSphere podem ser mapeados para a assinatura na qual o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
