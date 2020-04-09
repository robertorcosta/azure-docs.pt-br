---
title: Estatísticas de latência de ida e volta da rede Azure | Microsoft Docs
description: Conheça as estatísticas de latência de ida e volta entre as regiões do Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886935"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estatísticas de latência de ida e volta da rede Azure

O Azure monitora continuamente a latência (velocidade) das áreas centrais de sua rede usando ferramentas internas de monitoramento, bem como medições coletadas pelo [ThousandEyes](https://thousandeyes.com), um serviço de monitoramento sintético de terceiros.

## <a name="how-are-the-measurements-collected"></a>Como são coletadas as medidas?

As medidas de latência são coletadas de agentes do ThousandEyes, hospedados em regiões de nuvem do Azure em todo o mundo, que enviam continuamente sondas de rede entre si em intervalos de 1 minuto. As estatísticas mensais de latência são derivadas da média das amostras coletadas para o mês.

## <a name="march-2020-round-trip-latency-figures"></a>Números da latência de ida e volta de março de 2020

Os tempos médios mensais de ida e volta entre as regiões do Azure nos últimos 31 dias (terminando em 31 de março de 2020) são mostrados abaixo. As seguintes medidas são alimentadas por [ThousandEyes](https://thousandeyes.com).

[![Estatísticas de latência inter-região do Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Conheça [as regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
