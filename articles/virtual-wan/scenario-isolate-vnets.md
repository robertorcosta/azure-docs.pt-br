---
title: 'Cenário: isolando VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para VNets de isolamento de roteamento
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85566997"
---
# <a name="scenario-isolating-vnets"></a>Cenário: isolando VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é impedir que o VNets seja capaz de acessar outros. Isso é conhecido como isolar VNets. A carga de trabalho na VNet permanece isolada e não é capaz de se comunicar com outros VNets, como em um cenário de qualquer para qualquer. No entanto, os VNets são necessários para alcançar todos os branches (VPN, ER e VPN de usuário). Nesse cenário, todas as conexões VPN, ExpressRoute e VPN de usuário são associadas à mesma tabela de rotas e a uma. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar esse cenário, leve em consideração as seguintes etapas:

1. Crie uma tabela de rotas personalizada. No exemplo, a tabela de rotas é **RT_VNet**. Para criar uma tabela de rotas, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md). Para obter mais informações sobre tabelas de rotas, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
2. Ao criar a tabela de rotas **RT_VNet** , defina as seguintes configurações:

   * **Associação**: selecione o VNets que você deseja isolar.
   * **Propagação**: selecione a opção para branches, que implicam conexões de ramificação (VPN/er/P2S) propagarão rotas para esta tabela de rotas.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="VNets isolado":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
