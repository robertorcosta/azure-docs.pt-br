---
title: Prefixo de endereço IPv6 público na rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o prefixo público de endereço IPv6 na rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 096dbcb6b6a732bd21622658f6f30c5158a821c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420532"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Prefixo de endereço iPv6 público reservado

No Azure, as redes virtuais (VNet) de pilha dupla (IPv4+IPv6) e as máquinas virtuais (VMs) são seguras por padrão, já que não possuem conectividade com a Internet. Você pode facilmente adicionar uma conectividade de Internet IPv6 aos seus Balanceadores de Carga do Azure e VMs com endereços IPv6 públicos que você obtém do Azure.

Todos os IPs públicos que você reserva estão associados a uma região Azure de sua escolha e com sua assinatura do Azure. Você pode mover um IP público IPv6 reservado (estático) entre qualquer um dos Balanceadores de Carga do Azure ou VMs em sua assinatura. Você pode dissociar o IP público IPv6 inteiramente e ele será mantido para seu uso quando estiver pronto.

> [!WARNING]
> Tenha cuidado para não excluir seus endereços IP públicos acidentalmente. A exclusão de um IP público remove-o da sua assinatura e você não será capaz de recuperá-lo (nem mesmo com a ajuda do suporte do Azure).

Além de reservar endereços IPv6 individuais, você pode reservar faixas contíguas de endereços Azure IPv6 (conhecido como prefixo IP) para seu uso.  Semelhante a endereços IP individuais, prefixos reservados estão associados a uma região Azure de sua escolha e com sua assinatura do Azure. Reservar uma gama previsível e contígua de endereços tem muitos usos. Por exemplo, você pode simplificar muito a lista de *whitelisting* IP de seus aplicativos hospedados no Azure por sua empresa e seus clientes, pois suas faixas de IP estáticas podem ser prontamente programadas em firewalls locais.  Você pode criar IPs públicos individuais a partir do seu prefixo IP conforme necessário e, quando você excluir esses IPs públicos individuais, eles são *devolvidos* à sua faixa reservada para que você possa reutilizá-los mais tarde. Todos os endereços IP do seu Prefixo IP estão reservados para seu uso exclusivo até que você exclua seu Prefixo.



## <a name="ipv6-prefix-sizes"></a>Tamanhos de prefixo IPv6
Os seguintes tamanhos públicos de prefixo IP estão disponíveis:

-  Tamanho mínimo do prefixo IPv6: /127 = 2 endereços
-  Tamanho máximo do prefixo IPv6: /124 = 16 endereços

O tamanho do prefixo é especificado como um tamanho de máscara de roteamento interdomínio (CIDR) sem classe. Por exemplo, uma máscara de /128 representa um endereço IPv6 individual, pois os endereços IPv6 são compostos por 128 bits.

## <a name="pricing"></a>Preços
 
Para os custos associados ao uso de IPs Públicos do Azure, tanto endereços IP individuais quanto faixas IP, consulte [Preços de endereço IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitações
O IPv6 é suportado em IPs públicos básicos apenas com alocação "dinâmica", o que significa que o endereço IPv6 mudará se você excluir e reimplantar seu aplicativo (VM's ou balanceadores de carga) no Azure. O suporte do IP público padrão é apenas a alocação estática (reservada), embora os balanceadores de carga INTERNAL padrão também possam suportar a alocação dinâmica de dentro da sub-rede à qual são atribuídos.  

Como uma prática recomendada, recomendamos que você use IPs Públicos Padrão e Balanceadores de Carga Padrão para seus aplicativos IPv6.

## <a name="next-steps"></a>Próximas etapas
- Reserve um prefixo de [endereço IPv6](ipv6-reserve-public-ip-address-prefix.md)público .
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
- Saiba [como criar e usar IPs públicos](virtual-network-public-ip-address.md) (tanto IPv4 quanto IPv6) no Azure.
