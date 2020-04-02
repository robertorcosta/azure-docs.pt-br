---
title: incluir arquivo
description: incluir arquivo
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 95cb29e871cce2ba600ab654d48c685b90ed027e
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573244"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede - Azure Resource Manager
Os seguintes limites se aplicam apenas aos recursos de rede gerenciados através **do Azure Resource Manager** por região por assinatura. Saiba como [visualizar seu uso atual de recursos contra seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver uma coluna de limite máximo, o recurso não terá limites ajustáveis. Se você teve esses limites aumentados pelo suporte no passado e não vê limites atualizados nas tabelas a seguir, [abra uma solicitação de suporte ao cliente on-line sem custo](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Recurso | Limite | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3.000 |
| Emparelhamentos de rede virtual por rede virtual |500 |
| [Gateways de rede virtuais (gateways VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways de rede virtuais (gateways ExpressRoute) por rede virtual](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65.536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Placas de interface de rede |65.536 |
| Grupos de segurança de rede |5.000 |
| Regras de NSG por NSG |1,000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4.000 |
| Grupos de segurança do aplicativo |3.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rota definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rota |400 |
| Certificados de raiz ponto a ponto por Gateway Azure VPN |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limites do endereço IP público
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos<sup>1</sup> | 10 para básico. | Entre em contato com o suporte. |
| Endereços IP públicos estáticos<sup>1</sup> | 10 para básico. | Entre em contato com o suporte. |
| Endereços IP públicos padrão<sup>1</sup> | 10 | Entre em contato com o suporte. |
| Prefixos IP públicos | limitado por número de IPs Públicos Padrão em uma assinatura | Entre em contato com o suporte. |
| Comprimento do prefixo IP público | / 28 | Entre em contato com o suporte. |

<sup>1</sup> Os limites padrão para endereços IP públicos variam de acordo com o tipo de categoria de oferta, como Avaliação Gratuita, Pay-As-You-Go, CSP. Por exemplo, o padrão para assinaturas do Enterprise Agreement é 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limites do balanceador de carga
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [visualizar seu uso atual de recursos contra seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Recurso                                | Limite         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras por recurso                      | 1.500                         |
| Regras por NIC (em todos os IPs em uma NIC) | 300                           |
| Configurações de IP do Frontend              | 600                           |
| Tamanho do pool de back-end                       | 1.000 configurações IP, rede virtual única |
| Recursos de backend <sup>por Balanceador de carga 1<sup> | 150                   |
| Portas de alta disponibilidade                 | 1 por front-end interno       |
| Regras de saída por Balanceador de carga        | 20                            |
| [Tempo de inatividade TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minutos/30 minutos          |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquinavirtual autônomo, recursos de conjunto de disponibilidade e recursos de escala de máquinavirtual.

**Balanceador de carga básico**

| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs em uma NIC) | 300                          |
| Configurações de IP do Frontend              | 200                          |
| Tamanho do pool de back-end                       | Configurações de 300 IP, conjunto de disponibilidade única |
| Conjuntos de disponibilidade por Balanceador de carga     | 150                          |

<a name="virtual-networking-limits-classic"></a>Os seguintes limites se aplicam apenas aos recursos de rede gerenciados através do **modelo clássico** de implantação por assinatura. Saiba como [visualizar seu uso atual de recursos contra seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de redes locais |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4.096 |4.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500.000, até 1.000.000 para duas ou mais NICs. |500.000, até 1.000.000 para duas ou mais NICs. |
| Grupos de segurança de rede (NSG) |200 |200 |
| Regras de NSG por NSG |1,000 |1,000 |
| Tabelas de rota definidas pelo usuário |200 |200 |
| Rotas definidas pelo usuário por tabela de rota |400 |400 |
| Endereços IP públicos (dinâmicos) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implantação |5 |Contate o suporte |
| VIP privado (balanceamento interno de carga) por implantação |1 |1 |
| Listas de controle de acesso de ponto final (ACLs) |50 |50 |
