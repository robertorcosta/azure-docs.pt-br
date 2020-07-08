---
title: Estatísticas de latência de viagens de ida e volta de rede do Azure | Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre regiões do Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83993993"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede do Azure

O Azure monitora continuamente a latência (velocidade) das áreas principais de sua rede usando ferramentas de monitoramento internas, bem como as medidas coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como as medições são coletadas?

As medições de latência são coletadas de agentes ThousandEyes, hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente investigações de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média dos exemplos coletados para o mês.

## <a name="april-2020-round-trip-latency-figures"></a>Números de latência de ida e volta de abril de 2020

Os tempos médios de ida e volta mensais entre as regiões do Azure para os últimos 30 dias (terminando em 30 de abril de 2020) são mostrados abaixo. As medidas a seguir são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência entre regiões do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
