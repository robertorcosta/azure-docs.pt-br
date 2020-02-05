---
title: Principais conceitos para administrar soluções VMware do Azure (AVS)
description: Descreve os principais conceitos para administrar soluções VMware do Azure (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025224"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Conceitos principais para a administração de soluções VMware do Azure (AVS)

Administrar as soluções do Azure VMware (AVS) requer uma compreensão dos seguintes conceitos:

* Serviço AVS, que é exibido como soluções VMware do Azure (AVS)-serviço
* O nó AVS, que é exibido como Azure VMware Solutions (AVS)-nó
* Nuvem privada de AVS
* Rede de serviço
* Máquina virtual AVS, que é exibida como soluções VMware do Azure (AVS)-máquina virtual

## <a name="avs-service"></a>Serviço AVS

Com o serviço AVS, você pode criar e gerenciar todos os recursos associados às soluções VMware (AVS) no portal do Azure. Crie um recurso de serviço em todas as regiões em que você pretende usar o serviço.

Saiba mais sobre o [serviço AVS](cloudsimple-service.md).

## <a name="avs-node"></a>Nó AVS

Um nó AVS é um host de armazenamento e de computação com e sem sistema operacional dedicado, em que o hipervisor VMware ESXi é implantado. Esse nó é então incorporado às plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de rede AVS e os serviços de rede de borda também estão habilitados. Cada nó serve como uma unidade de computação e capacidade de armazenamento que você pode provisionar para criar [nuvens privadas de AVS](cloudsimple-private-cloud.md). Você provisiona ou reserva nós em uma região onde o serviço AVS está disponível.

Saiba mais sobre os [nós da AVS](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Nuvem privada de AVS

Uma nuvem privada de AVS é um ambiente de pilha VMware isolado gerenciado por um vCenter Server em seu próprio domínio de gerenciamento. O VMware Stack inclui hosts ESXi, vSphere, vCenter, vSAN e NSX. A pilha é executada em nós dedicados (hardware bare-metal dedicado e isolado) e é consumida pelos usuários por meio de ferramentas nativas do VMware que incluem o vCenter e o NSX Manager. Os nós dedicados são implantados em locais do Azure e são gerenciados pelo Azure. Cada nuvem privada de AVS pode ser segmentada e protegida usando serviços de rede, como VLANs e sub-redes e tabelas de firewall. As conexões com seu ambiente local e a rede do Azure são criadas usando conexões seguras, VPN privadas e Azure ExpressRoute.

Saiba mais sobre a [nuvem privada de AVS](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviço

O serviço AVS fornece uma rede por região em que o serviço AVS é implantado. A rede é um único espaço de endereço de camada TCP 3 com roteamento habilitado por padrão. Todas as nuvens e sub-redes privadas da AVS criadas nessa região se comunicam entre si sem nenhuma configuração adicional. Você cria grupos de portas distribuídas no vCenter usando as VLANs. Você pode usar os seguintes recursos de rede para configurar e proteger seus recursos de carga de trabalho em sua nuvem privada de AVS:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Conexão de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Máquina virtual AVS

Com o serviço AVS, você pode gerenciar máquinas virtuais VMware do portal do Azure. Um ou mais clusters ou pools de recursos do seu ambiente vSphere podem ser mapeados para a assinatura na qual o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais de AVS](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
