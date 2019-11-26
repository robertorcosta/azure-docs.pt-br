---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485490"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Grupos | Default/maximum limit | 
| --- | --- |
| Redes virtuais |1\.000 |
| Sub-redes por rede virtual |3\.000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500.000 |
| Network interface cards |65,536 |
| Grupos de Segurança de Rede |5\.000 |
| Regras de NSG por NSG |1\.000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4\.000 |
| Grupos de segurança do aplicativo |3\.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4\.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="publicip-address"></a>Limites do endereço IP público
| Grupos | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmicos | 1,000 for Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 1,000 for Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 1,000 for Standard.|Entre em contato com o suporte. |
| Public IP prefix length | / 28 | Entre em contato com o suporte. |

#### <a name="load-balancer"></a>Load balancer limits
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Grupos | Default/maximum limit |
| --- | --- |
| Balanceadores de carga | 1\.000 | 
| Regras por recurso, Básico | 250 |
| Regras por recurso, Standard | 1\.500 | 
| Regras por configuração de IP | 299 |
| Regras por NIC | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Grupos | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de redes locais |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4\.096 |4\.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| Grupos de segurança de rede (NSG) |200 |200 |
| Regras de NSG por NSG |1\.000 |1\.000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| Endereços IP públicos (dinâmicos) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implantação |5 |Entrar em contato com o suporte |
| Private VIP (internal load balancing) per deployment |1 |1 |
| Endpoint access control lists (ACLs) |50 |50 |
