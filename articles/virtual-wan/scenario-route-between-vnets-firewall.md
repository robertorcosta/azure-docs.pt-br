---
title: 'Cenário: roteamento personalizado do firewall do Azure para WAN virtual'
titleSuffix: Azure Virtual WAN
description: Cenários para tráfego de roteamento de roteamento entre VNets diretamente, mas use o Firewall do Azure para VNet->Internet/Branch e Branch para fluxos de tráfego de VNet
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567192"
---
# <a name="scenario-azure-firewall---custom"></a>Cenário: Firewall do Azure-personalizado

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é rotear o tráfego entre VNets diretamente, mas usar o Firewall do Azure para fluxos de tráfego de VNet para a Internet/Branch e de ramificação para rede virtual. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Nesse cenário, você deseja rotear o tráfego por meio do firewall do Azure para tráfego de VNet a Internet, de VNet a ramificação ou de ramificação para rede virtual, mas gostaria de ir direto para o tráfego de VNet para VNet. Se você usou o Gerenciador de firewall do Azure, as configurações de rota serão preenchidas automaticamente na **tabela de rotas padrão**. O tráfego privado se aplica a VNet e branches, o tráfego da Internet se aplica a 0.0.0.0/0.

As conexões VPN, ExpressRoute e VPN de usuário são chamadas coletivamente de ramificações e associadas à mesma tabela de rotas (padrão). Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas. Para configurar esse cenário, leve em consideração as seguintes etapas:

1. Crie uma **RT_VNET**de tabela de rotas personalizada.
1. Crie uma rota para ativar VNet a Internet e VNet a Branch: 0.0.0.0/0 com o próximo salto apontando para o Firewall do Azure. Na seção de propagação, você garantirá que VNets sejam selecionados, o que garantiria rotas mais específicas, permitindo assim o fluxo de tráfego direto de VNet para VNet.

   * Em **associação:** selecione VNets, o que significa que o VNets alcançará o destino de acordo com as rotas desta tabela de rotas.
   * Em **propagação:** selecione VNets, o que significa que o VNets se propaga para esta tabela de rotas; em outras palavras, rotas mais específicas serão propagadas para essa tabela de rotas, garantindo assim o fluxo de tráfego direto entre VNet para VNet.

1. Adicione uma rota estática agregada para VNets na **tabela de rotas padrão** para ativar o fluxo de Branch para VNet por meio do firewall do Azure. 

   * Lembre-se de que as ramificações estão associadas e se propagando para a tabela de rotas padrão.
   * As ramificações não se propagam para RT_VNET tabela de rotas. Isso garante o fluxo de tráfego de VNet para ramificação por meio do firewall do Azure.

Isso resultará nas alterações de configuração de roteamento, conforme mostrado na **Figura 1**.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
* Para obter mais informações sobre como configurar o roteamento de Hub virtual, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).
