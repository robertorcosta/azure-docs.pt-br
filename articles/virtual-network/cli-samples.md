---
title: Exemplos de CLI do Azure para rede virtual
description: Exemplos de CLI do Azure para rede virtual.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: ff1017c8a6bc3c2806da6ea2a53b931aaf28644f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092091"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Exemplos de CLI do Azure para rede virtual

A tabela a seguir inclui links para bash scripts com comandos de CLI do Azure:

| | |
|----|----|
| [Criar uma rede virtual para aplicativos de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end é limitado a MySQL, porta 3306. |
| [Emparelhar duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Cria e conecta duas redes virtuais na mesma região. |
| [Rotear o tráfego por meio de uma solução de virtualização de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM capaz de rotear o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de entrada e saída de rede da VM](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP, HTTPS e SSH. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Basic Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Load Balancer básico com endereços IP públicos do IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Standard Load Balancer com endereços IP públicos do IPv4 e IPv6. |