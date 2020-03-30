---
title: O que é o endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16 e como ele funciona com seus recursos.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114367"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual usado para facilitar um canal de comunicação com os recursos da plataforma do Azure. Os clientes podem definir qualquer espaço de endereço para sua rede virtual privada no Azure. Portanto, os recursos da plataforma do Azure devem ser apresentados como um endereço IP público exclusivo. Esse endereço IP público virtual facilita o seguinte:

- Habilita o agente de VM a se comunicar com a plataforma do Azure para sinalizar que ele está em um estado "Pronto".
- Habilita a comunicação com o servidor virtual do DNS para fornecer a resolução de nome filtrada para os recursos (como VM) que não têm um servidor DNS personalizado. Essa filtragem garante que os clientes possam resolver os nomes de host de seus recursos.
- Permite que [sondas de saúde do balanceador de carga do Azure](../load-balancer/load-balancer-custom-probe-overview.md) determinem o estado de saúde das VMs.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.
- Habilita mensagens de pulsação de Agente Convidado para a função de PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Escopo de endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é utilizado em todas as regiões e em todas as nuvens nacionais. Este endereço IP público especial é de propriedade da Microsoft e não será alterado. Recomendamos que você permita este endereço IP em qualquer política local (na VM) de firewall (direção de saída). A comunicação entre esse endereço IP especial e os recursos é segura porque a plataforma interna do Azure pode originar uma mensagem desse endereço IP. Se esse endereço for bloqueado, um comportamento inesperado poderá ocorrer em uma variedade de cenários. 168.63.129.16 é um [IP virtual do nó host](../virtual-network/security-overview.md#azure-platform-considerations) e, como tal, não está sujeito a rotas definidas pelo usuário.

- O Agente VM requer comunicação de saída sobre as portas 80, 443, 32526 com wireserver (168.63.129.16). Estes devem ser abertos no firewall local na VM. A comunicação nessas portas com 168.63.129.16 não está sujeita aos grupos de segurança de rede configurados.
- 168.63.129.16 pode fornecer serviços de DNS para a VM. Se isso não for desejado, esse tráfego pode ser bloqueado no firewall local na VM. Por padrão, a comunicação DNS não está sujeita aos grupos de segurança de rede configurados, a menos que especificamente direcionado para aproveitar a tag de serviço [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags)
- Quando a VM faz parte de um pool de backend balanceador de carga, a comunicação do teste de [saúde](../load-balancer/load-balancer-custom-probe-overview.md) deve ser autorizada a ter origem a partir de 168.63.129,16. A configuração padrão do grupo de segurança da rede tem uma regra que permite essa comunicação. Essa regra aproveita a tag de serviço [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) Se desejar, esse tráfego pode ser bloqueado configurando o grupo de segurança da rede, no entanto, isso resultará em testes que falham.

Em um cenário de rede não virtual (Classic), o teste de saúde é originado de um IP privado e 168.63.129.16 não é usado.

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou excluir um Grupo de Segurança de Rede](manage-network-security-group.md)
