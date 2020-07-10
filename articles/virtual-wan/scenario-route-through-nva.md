---
title: 'Cenário: rotear o tráfego por meio de um NVA'
titleSuffix: Azure Virtual WAN
description: Rotear o tráfego por meio de uma NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142975"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Cenário: rotear o tráfego por meio de um NVA

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário de NVA, o objetivo é rotear o tráfego por meio de um NVA (solução de virtualização de rede) para Branch para VNet e VNet para Branch. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

> [!NOTE]
> Alguns dos recursos de roteamento ainda podem estar sendo distribuídos. Se a distribuição ainda não aconteceu em sua região, use as etapas nestas versões dos artigos neste ínterim:
>* [portal do Azure artigo](virtual-wan-route-table-nva-portal.md)
>* [Artigo do PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura de cenário

Na **Figura 1**, há dois hubs; **Hub 1** e **Hub 2**.

* O **Hub 1** e o **Hub 2** estão diretamente conectados ao NVA VNets **vnet 2** e à **VNET 4**.

* **Vnet 5** e **vnet 6** são emparelhadas com **vnet 2**.

* **Vnet 7** e **vnet 8** são emparelhadas com **vnet 4**.

* **VNETs 5, 6, 7, 8** são spokes indiretos, não conectados diretamente a um hub virtual.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar o roteamento via NVA, aqui estão as etapas a serem consideradas:

1. Identifique a conexão VNet do NVA spoke. Na **Figura 1**, eles são **conexão vnet 2 (Eastusconn)** e **conexão vnet 4 (weconn)**.

   Certifique-se de que há UDRs configurado:
   * Da VNET 5 e 6 para o IP NVA da VNET 2
   * Do VNET 7 e 8 para o IP NVA VNET 4 
   
   Você não precisa conectar a VNET 5, 6, 7, 8 aos hubs virtuais diretamente. Verifique se NSGs no VNETs 5, 6, 7, 8 permitem o tráfego para Branch (VPN/ER/P2S) ou VNETs conectado à sua VNETs remota. Por exemplo, VNET 5, 6 deve garantir que NSGs permita o tráfego para prefixos de endereço local e VNETs 7, 8 que estejam conectados ao Hub remoto 2. 

2. Adicione uma entrada de rota estática agregada para VNETs 2, 5, 6 à tabela de rotas padrão do hub 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exemplo":::

3. Configure uma rota estática para o VNETs 5, 6 na conexão de rede virtual da VNET 2. Para configurar a configuração de roteamento para uma conexão de rede virtual, consulte [Roteamento de Hub virtual](how-to-virtual-hub-routing.md#routing-configuration).

4. Adicione uma entrada de rota estática agregada para VNETs 4, 7, 8 à tabela de rotas padrão do hub 1.

5. Repita as etapas 2, 3 e 4 para a tabela de rotas padrão do Hub 2.

Isso resultará nas alterações de configuração de roteamento, como mostra a figura abaixo

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Resultado":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
