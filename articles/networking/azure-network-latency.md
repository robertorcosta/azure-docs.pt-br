---
title: Estatísticas de latência de viagens de ida e volta de rede do Azure | Microsoft Docs
description: Saiba mais sobre estatísticas de latência de ida e volta entre regiões do Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082924"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede do Azure

O Azure monitora continuamente a latência (velocidade) das áreas principais de sua rede usando ferramentas de monitoramento internas, bem como as medidas coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como as medições são coletadas?

As medições de latência são coletadas de agentes ThousandEyes, hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente investigações de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média dos exemplos coletados para o mês.

## <a name="february-2020-round-trip-latency-figures"></a>Números de latência de ida e volta de fevereiro de 2020

Os tempos médios de ida e volta mensais entre as regiões do Azure para os últimos 29 dias (terminando em 29 de fevereiro de 2020) são mostrados abaixo. As medidas a seguir são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![estatísticas de latência entre regiões do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
