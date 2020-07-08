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
ms.openlocfilehash: 511354633b9f88f3d6cd2e2170ce3b7ca1f4ecdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82096035"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede-Azure Resource Manager
Os limites a seguir se aplicam somente a recursos de rede gerenciados por meio de **Azure Resource Manager** por região por assinatura. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver uma coluna de limite máximo, o recurso não terá limites ajustáveis. Se você tivesse esses limites aumentados pelo suporte no passado e não vir os limites atualizados nas tabelas a seguir, [abra uma solicitação de atendimento ao cliente online sem encargos](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Recurso | Limite | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3\.000 |
| Emparelhamentos de rede virtual por rede virtual |500 |
| [Gateways de rede virtual (gateways de VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways de rede virtual (gateways de ExpressRoute) por rede virtual](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65.536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Placas de interface de rede |65.536 |
| Grupos de segurança de rede |5\.000 |
| Regras de NSG por NSG |1,000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4.000 |
| Grupos de segurança do aplicativo |3\.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados raiz ponto a site por gateway de VPN do Azure |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limites do endereço IP público
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos<sup>1</sup> | 10 para básico. | Entre em contato com o suporte. |
| Endereços IP públicos estáticos<sup>1</sup> | 10 para básico. | Entre em contato com o suporte. |
| Endereços IP públicos padrão<sup>1</sup> | 10 | Entre em contato com o suporte. |
| Prefixos IP públicos | limitado pelo número de IPs públicos padrão em uma assinatura | Entre em contato com o suporte. |
| Comprimento do prefixo IP público | / 28 | Entre em contato com o suporte. |

<sup>1</sup> Os limites padrão para endereços IP públicos variam por tipo de categoria de oferta, como avaliação gratuita, pré-pago, CSP. Por exemplo, o padrão para assinaturas de Enterprise Agreement é 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limites de balanceador de carga
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Recurso                                | Limite         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras por recurso                      | 1\.500                         |
| Regras por NIC (em todos os IPs em uma NIC) | 300                           |
| Configuração do IP de front-end              | 600                           |
| Tamanho do pool de back-end                       | 1.000 configurações de IP, rede virtual única |
| Recursos de back-end por Load Balancer <sup> 1<sup> | 150                   |
| Portas de alta disponibilidade                 | 1 por front-end interno       |
| Regras de saída por Load Balancer        | 600                           |
| [Tempo limite de ociosidade de TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minutos/30 minutos          |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquina virtual autônoma, recursos de conjunto de disponibilidade e grupos de posicionamento de conjunto de dimensionamento de máquinas virtuais.

**Load Balancer Básico**

| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs em uma NIC) | 300                          |
| Configuração do IP de front-end              | 200                          |
| Tamanho do pool de back-end                       | 300 configurações de IP, conjunto de disponibilidade único |
| Conjuntos de disponibilidade por Load Balancer     | 150                          |

<a name="virtual-networking-limits-classic"></a>Os limites a seguir se aplicam somente a recursos de rede gerenciados por meio do modelo de implantação **clássico** por assinatura. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de redes locais |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4\.096 |4\.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500.000, até 1 milhão para duas ou mais NICs. |500.000, até 1 milhão para duas ou mais NICs. |
| Grupos de segurança de rede (NSG) |200 |200 |
| Regras de NSG por NSG |1,000 |1,000 |
| Tabelas de rotas definidas pelo usuário |200 |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmicos) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implantação |5 |Contate o suporte |
| VIP privado (balanceamento de carga interno) por implantação |1 |1 |
| Listas de controle de acesso de ponto de extremidade (ACLs) |50 |50 |
