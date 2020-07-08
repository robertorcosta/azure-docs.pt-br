---
title: 'Cenário: isolamento personalizado para VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento – impedir que o VNets selecionado seja capaz de alcançar um ao outro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85566999"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Cenário: isolamento personalizado para VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Em um cenário de isolamento personalizado para VNets, o objetivo é impedir que um conjunto específico de VNets seja capaz de alcançar outro conjunto específico de VNets. No entanto, os VNets são necessários para alcançar todos os branches (VPN/ER/usuário VPN).

Nesse cenário, as conexões VPN, ExpressRoute e VPN de usuário (coletivamente chamadas de ramificações) estão associadas à mesma tabela de rotas (tabela de rotas padrão). Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Fluxo de trabalho do cenário

Na **Figura 1**, há conexões VNet azuis e vermelhas.

* Os VNets conectados à Blue podem alcançar um ao outro, bem como acessar todas as conexões de branches (VPN/ER/P2S).
* O VNets vermelho pode alcançar um ao outro, bem como acessar todas as conexões de branches (VPN/ER/P2S).

Considere as etapas a seguir ao configurar o roteamento.

1. Crie duas tabelas de rotas personalizadas na portal do Azure, **RT_BLUE** e **RT_RED**.
2. Para a tabela de rotas **RT_BLUE**, em:
   * **Associação**: selecione todos os VNets azuis
   * **Propagação**: para branches, selecione a opção para branches, que implicam conexões de ramificação (VPN/er/P2S) propagarão rotas para esta tabela de rotas.
3. Repita as mesmas etapas para **RT_RED** tabela de rotas para VNets vermelho e branches (VPN/er/P2S).

Isso resultará nas alterações de configuração de roteamento, como mostra a figura abaixo

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
