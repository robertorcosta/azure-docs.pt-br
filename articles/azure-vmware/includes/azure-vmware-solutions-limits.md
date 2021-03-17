---
title: Limites da solução VMware do Azure
description: Limitações da solução VMware do Azure.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622143"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela a seguir descreve os limites máximos para a solução Azure VMware.

| **Recurso** | **Limite** |
| :-- | :-- |
| Clusters por nuvem privada | 12 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nós por nuvem privada | 64 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos do site HCX | 3 com a Advanced Edition, 10 com Enterprise Edition |
| Número máximo de SDDCs vinculados para AVS ExpressRoute | 4 |
| AVS de ExpressRoute portspeed | 10 Gbps<br />O gateway de rede virtual usado determina a largura de banda real. Para obter mais detalhes, consulte [sobre gateways de rede virtual do ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| IPs públicos expostos por meio de vWAN | 100 |
| limites de capacidade do vSAN | 75% do total utilizável (manter 25% disponível para SLA)  |

Para outros limites específicos do VMware, use a [ferramenta de configuração máxima do VMware!](https://configmax.vmware.com/).
