---
title: Limites da solução VMware do Azure
description: Limitações da solução VMware do Azure.
ms.topic: include
ms.date: 02/25/2021
ms.openlocfilehash: 06160e967e7094cc3a6a51c1775873d0e4ab86b7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102045778"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela a seguir descreve os limites máximos para a solução Azure VMware.

| **Recurso** | **Limite** |
| --- | --- |
| Clusters por nuvem privada | 4 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nós por nuvem privada | 64 |
|  vCenter por nuvem privada | 1  |
| Emparelhamentos do site HCX | 3 com a Advanced Edition, 10 com Enterprise Edition |
| Limites de ExpressRoute | 4 SDDCs, 10 SDDCs quando o ultra gateway é usado |
| IPs públicos expostos por meio de vWAN | 256 |
|  limites de capacidade do vSAN | 75% do total utilizável (manter 25% disponível para SLA)  |
