---
title: incluir arquivo
description: incluir arquivo
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 12/09/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: cf79911c24998c3d00937937cce5c68bbb564f1e
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751573"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede-Azure Resource Manager os seguintes limites se aplicam somente a recursos de rede gerenciados por meio de **Azure Resource Manager** por região por assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver uma coluna de limite máximo, o recurso não terá limites ajustáveis. Se você tivesse esses limites aumentados pelo suporte no passado e não vir os limites atualizados nas tabelas a seguir, [abra uma solicitação de atendimento ao cliente online sem encargos](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Grupos | Limite máximo/padrão | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3\.000 |
| Emparelhamentos de rede virtual por rede virtual |500 |
| [Gateways de rede virtual (gateways de VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65.536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Placas de interface de rede |65.536 |
| Grupos de Segurança de Rede |5\.000 |
| Regras de NSG por NSG |1,000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4\.000 |
| Grupos de segurança do aplicativo |3\.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4\.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados raiz ponto a site por gateway de VPN do Azure |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="publicip-address"></a>Limites do endereço IP público
| Grupos | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmicos | 1\.000 para Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 1\.000 para Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 1\.000 para Standard.|Entre em contato com o suporte. |
| Comprimento do prefixo IP público | / 28 | Entre em contato com o suporte. |

#### <a name="load-balancer"></a>Limites de balanceador de carga
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Grupos                                | Limite máximo/padrão         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras por recurso                      | 1\.500                         |
| Regras por NIC (em todos os IPs em uma NIC) | 300                           |
| Configurações de IP de front-end             | 600                           |
| Tamanho do pool de back-end                          | 1\.000 instâncias, rede virtual única |
| Recursos de back-end por balanceador de carga<sup>1<sup>   | 150                 |
| Portas de alta disponibilidade                 | 1 por front-end interno      |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquina virtual autônoma, recursos de conjunto de disponibilidade e recursos de conjunto de escala de máquina virtual.

**Load Balancer básica**

| Grupos                                | Limite máximo/padrão        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs em uma NIC) | 300                          |
| Configurações de IP de front-end             | 200                          |
| Tamanho do pool de back-end                           | 100 instâncias, conjunto de disponibilidade único |

#### <a name="virtual-networking-limits-classic"></a>Os limites a seguir se aplicam somente a recursos de rede gerenciados por meio do modelo de implantação **clássico** por assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Grupos | Limite padrão | Limite máximo |
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
| VIP público por implantação |5 |Entrar em contato com o suporte |
| VIP privado (balanceamento de carga interno) por implantação |1 |1 |
| Listas de controle de acesso de ponto de extremidade (ACLs) |50 |50 |
