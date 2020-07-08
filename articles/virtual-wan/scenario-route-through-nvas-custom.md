---
title: 'Cenário: rotear o tráfego por meio de NVAs usando configurações personalizadas'
titleSuffix: Azure Virtual WAN
description: Esse cenário ajuda a rotear o tráfego por meio de NVAs usando um NVA diferente para o tráfego de entrada na Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567070"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Cenário: rotear o tráfego por meio de NVAs-personalizado (visualização)

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Neste cenário de NVA (solução de virtualização de rede), o objetivo é rotear o tráfego por meio de um NVA para > Branch de < VNet e usar um NVA diferente para tráfego de Internet. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura de cenário

Na **Figura 1**, há um Hub, **Hub 1**.

* O **Hub 1** está diretamente conectado a NVA VNets **vnet 4** e **vnet 5**.

* Espera-se que o tráfego entre VNETs 1, 2 e 3 e branches (VPN/ER/P2S) vá via **VNET 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego associado à Internet de VNETs 1, 2 e 3 Vá por meio do **VNET 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar o roteamento via NVA, aqui estão as etapas a serem consideradas:

1. Para que o tráfego de entrada na Internet vá via VNET5, você precisa de VNETs 1, 2 e 3 para se conectar diretamente via emparelhamento VNet à VNET 5. Você também precisa de um UDR configurado no VNets para 0.0.0.0/0 e o próximo salto 10.5.0.5. Atualmente, a WAN virtual não permite um NVA de próximo salto no Hub virtual para 0.0.0.0/0.

1. No portal do Azure, navegue até o Hub virtual e crie uma tabela de rotas personalizada **RT_Shared** que aprenderá as rotas por meio da propagação de todas as conexões de VNets e ramificação. Na **Figura 2**, isso é descrito como uma tabela de rotas personalizada vazia **RT_Shared**.

   * **Rotas:** Você não precisa adicionar rotas estáticas.

   * **Associação:** Selecione VNETs 4 e 5, o que significa que as conexões VNETs 4 e 5 são associadas à tabela de rotas **RT_Shared**.

   * **Propagação:** Como você deseja que todas as ramificações e conexões de VNet propaguem suas rotas dinamicamente para essa tabela de rotas, selecione branches e todos os VNets.

1. Crie uma **RT_V2B** de tabela de rotas personalizada para direcionar o tráfego de VNETs 1, 2 e 3 para branches.

   * **Rotas:** Adicione uma entrada de rota estática agregada para branches (VPN/ER/P2S) (10.2.0.0/16 na **Figura 2**) com o próximo salto como a conexão VNET 4. Você também precisa configurar uma rota estática na conexão da VNET 4 para prefixos de ramificação e indicar o próximo salto como o IP específico do NVA na VNET 4.

   * **Associação:** Selecione todos os VNETs 1, 2 e 3. Isso implica que as conexões de VNet 1, 2 e 3 serão associadas a essa tabela de rotas e poderão aprender rotas (estáticas e dinâmicas via propagação) nesta tabela de rotas.

   * **Propagação:** As conexões propagam rotas para tabelas de rotas. Selecionar VNETs 1, 2 e 3 permitirá propagar rotas de VNETs 1, 2 e 3 para esta tabela de rotas. Não é necessário propagar rotas de conexões de ramificação para RT_V2B, uma vez que o tráfego de VNet de ramificação passa pelo NVA em VNET4.
  
1. Edite a tabela de rotas padrão **Defaultroutetable**.

   Todas as conexões VPN, ExpressRoute e VPN de usuário são associadas à tabela de rotas padrão. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada de rota estática agregada para VNETs 1, 2 e 3 (10.1.0.0/16 na **Figura 2**) com o próximo salto como a conexão VNET 4. Você também precisa configurar uma rota estática na conexão da VNET 4 para os prefixos agregados da VNET 1, 2 e 3 e indicar o próximo salto como o IP específico do NVA na VNET 4.

   * **Associação:** Certifique-se de que a opção para branches (VPN/ER/P2S) esteja selecionada, garantindo que as conexões de ramificação locais estejam associadas a *defaultroutetable*.

   * **Propagação de:** Certifique-se de que a opção para branches (VPN/ER/P2S) esteja selecionada, garantindo que as conexões locais estejam propagando rotas para o *defaultroteiatable*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 2":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
