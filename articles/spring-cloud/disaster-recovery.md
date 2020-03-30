---
title: Recuperação de geodesastres da Nuvem de Azure Spring | Microsoft Docs
description: Saiba como proteger seu aplicativo Spring Cloud contra paralisações regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279149"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação de desastres da Nuvem da Primavera de Azure

Este artigo explica algumas estratégias que você pode usar para proteger seus aplicativos do Azure Spring Cloud de experimentar tempo de inatividade.  Qualquer região ou data center pode sofrer inatividade causada por desastres regionais, mas um planejamento cuidadoso pode mitigar o impacto em seus clientes.

## <a name="plan-your-application-deployment"></a>Planeje a implantação do aplicativo

Os aplicativos Azure Spring Cloud são executados em uma região específica.  O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma geografia, contendo um ou mais data centers.  Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. O Azure serializa as atualizações da plataforma (manutenção planejada) entre pares regionais, garantindo que apenas uma região em cada par seja atualizada por vez. Em caso de interrupção que afete várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

Garantir alta disponibilidade e proteção contra desastres requer que você implante seus aplicativos spring cloud em várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que você possa planejar suas implantações da Nuvem de Primavera para pares regionais.  Recomendamos que você considere três fatores-chave ao projetar sua arquitetura de microserviço: disponibilidade de região, regiões emparelhadas do Azure e disponibilidade de serviços.

*  Disponibilidade da região: Escolha uma área geográfica próxima aos seus usuários para minimizar o atraso da rede e o tempo de transmissão.
*  Regiões emparelhadas do Azure: Escolha regiões emparelhadas dentro da área geográfica escolhida para garantir atualizações coordenadas da plataforma e priorize os esforços de recuperação, se necessário.
*  Disponibilidade do serviço: Decida se suas regiões emparelhadas devem funcionar quente/quente, quente/quente ou quente/frio.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fornece balanceamento de carga de tráfego baseado em DNS e pode distribuir o tráfego de rede em várias regiões.  Use o Azure Traffic Manager para direcionar os clientes para a instância de serviço mais próxima da Nuvem de Primavera do Azure para eles.  Para obter o melhor desempenho e redundância, direcione todo o tráfego de aplicativos através do Azure Traffic Manager antes de enviá-lo para o serviço Azure Spring Cloud.

Se você tiver aplicativos Azure Spring Cloud em várias regiões, use o Azure Traffic Manager para controlar o fluxo de tráfego para seus aplicativos em cada região.  Defina um ponto final do Azure Traffic Manager para cada serviço usando o IP do serviço. Os clientes devem se conectar a um nome DNS do Azure Traffic Manager apontando para o serviço Azure Spring Cloud.  A carga do Azure Traffic Manager equilibra o tráfego nos pontos finais definidos.  Se um desastre atingir um data center, o Azure Traffic Manager direcionará o tráfego dessa região para seu par, garantindo a continuidade do serviço.