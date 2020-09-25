---
title: 'Cenário: rotear o tráfego por meio de NVAs usando configurações personalizadas'
titleSuffix: Azure Virtual WAN
description: Esse cenário ajuda a rotear o tráfego por meio de NVAs usando um NVA diferente para o tráfego de entrada na Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267492"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Cenário: rotear o tráfego por meio de NVAs-personalizado (visualização)

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Neste cenário de NVA (solução de virtualização de rede), o objetivo é rotear o tráfego por meio de um NVA para comunicação entre VNets e branches e usar um NVA diferente para tráfego de Internet. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Neste cenário, usaremos a Convenção de nomenclatura:

* "VNet de serviço" para redes virtuais em que os usuários implantaram um NVA (VNet 4 na **Figura 1**) para inspecionar o tráfego de não Internet.
* "Rede virtual DMZ" para redes virtuais em que os usuários implantaram um NVA a ser usado para inspecionar o tráfego vinculado à Internet (VNet 5 na **Figura 1**).
* "NVA spokes" para redes virtuais conectadas a uma VNet NVA (VNet 1, VNet 2 e VNet 3 na **Figura 1**).
* "Hubs" para hubs de WAN virtual gerenciados pela Microsoft.

A matriz de conectividade a seguir resume os fluxos com suporte neste cenário:

**Matriz de conectividade**

| De          | Para:|*NVA spokes*|*VNet de serviço*|*Rede virtual DMZ*|*Ramificações estáticas*|
|---|---|---|---|---|---|
| **NVA spokes**| &#8594;|      X |            X |   Emparelhamento |    Estático    |
| **VNet de serviço**| &#8594;|    X |            X |      X    |      X       |
| **Rede virtual DMZ** | &#8594;|       X |            X |      X    |      X       |
| **Branches** | &#8594;|  Estático |            X |      X    |      X       |

Cada uma das células na matriz de conectividade descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha) aprende um prefixo de destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico) para um fluxo de tráfego específico. Um "X" significa que a conectividade é fornecida nativamente pela WAN virtual e "estática" significa que a conectividade é fornecida pela WAN virtual usando rotas estáticas. Vamos detalhar as diferentes linhas:

* NVA spokes:
  * Os spokes alcançarão outros spokes diretamente sobre os hubs de WAN virtual.
  * Os spokes obterão conectividade com os branches por meio de uma rota estática apontando para a VNet do serviço. Eles não devem aprender prefixos específicos dos branches (caso contrário, eles seriam mais específicos e substituem o resumo).
  * Os spokes enviarão tráfego de Internet para a rede virtual DMZ por meio de um emparelhamento VNet direto.
* Filia
  * As ramificações chegarão a spokes por meio de um roteamento estático apontando para a VNet do serviço. Eles não devem aprender prefixos específicos do VNets que substituem a rota estática resumida.
* A VNet de serviço será semelhante a uma VNet de serviços compartilhados que precisa ser acessada de cada VNet e de cada ramificação.
* A VNet da DMZ não precisa realmente ter conectividade sobre a WAN virtual, pois o único tráfego com suporte será fornecido por emparelhamentos de VNet direta. No entanto, usaremos o mesmo modelo de conectividade que o rede virtual DMZ para simplificar a configuração.

Portanto, nossa matriz de conectividade fornece três padrões distintos de conectividade, que se traduz em três tabelas de rotas. As associações para os diferentes VNets serão as seguintes:

* NVA spokes:
  * Tabela de rotas associada: **RT_V2B**
  * Propagando para tabelas de rotas: **RT_V2B** e **RT_SHARED**
* NVA VNets (interno e Internet):
  * Tabela de rotas associada: **RT_SHARED**
  * Propagando para tabelas de rotas: **RT_SHARED**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_SHARED** e **padrão**

Precisamos dessas rotas estáticas para garantir que o tráfego de VNet para ramificação e de ramificação para VNet passe pelo NVA na VNet do serviço (VNet 4):

| Descrição | Tabela de rotas | Rota estática              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA spokes  | Padrão     | 10.1.0.0/16-> vnet4conn  |

Agora, a WAN virtual sabe a qual conexão enviar os pacotes, mas a conexão precisa saber o que fazer ao receber esses pacotes: é aqui que as tabelas de rotas de conexão são usadas.

| Descrição | Conexão | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Neste ponto, tudo deve estar em vigor.

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 1**, há um Hub, **Hub 1**.

* O **Hub 1** está diretamente conectado a NVA VNets **vnet 4** e **vnet 5**.

* Espera-se que o tráfego entre VNets 1, 2 e 3 e branches (VPN/ER/P2S) vá via **VNet 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego associado à Internet de VNets 1, 2 e 3 Vá por meio do **VNet 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para configurar o roteamento via NVA, aqui estão as etapas a serem consideradas:

1. Para que o tráfego vinculado à Internet vá por meio da VNet 5, você precisa de VNets 1, 2 e 3 para se conectar diretamente por meio de emparelhamento de VNet à VNet 5. Você também precisa de um UDR configurado no VNets para 0.0.0.0/0 e o próximo salto 10.5.0.5. Atualmente, a WAN virtual não permite um NVA de próximo salto no Hub virtual para 0.0.0.0/0.

1. No portal do Azure, navegue até o Hub virtual e crie uma tabela de rotas personalizada **RT_Shared** que aprenderá as rotas por meio da propagação de todas as conexões de VNets e ramificação. Na **Figura 2**, isso é descrito como uma tabela de rotas personalizada vazia **RT_Shared**.

   * **Rotas:** Você não precisa adicionar rotas estáticas.

   * **Associação:** Selecione VNets 4 e 5, o que significa que as conexões VNets 4 e 5 são associadas à tabela de rotas **RT_Shared**.

   * **Propagação:** Como você deseja que todas as ramificações e conexões de VNet propaguem suas rotas dinamicamente para essa tabela de rotas, selecione branches e todos os VNets.

1. Crie uma **RT_V2B** de tabela de rotas personalizada para direcionar o tráfego de VNets 1, 2 e 3 para branches.

   * **Rotas:** Adicione uma entrada de rota estática agregada para branches (VPN/ER/P2S) (10.2.0.0/16 na **Figura 2**) com o próximo salto como a conexão VNet 4. Você também precisa configurar uma rota estática na conexão da VNet 4 para prefixos de ramificação e indicar o próximo salto como o IP específico do NVA na VNet 4.

   * **Associação:** Selecione todos os VNets 1, 2 e 3. Isso implica que as conexões de VNet 1, 2 e 3 serão associadas a essa tabela de rotas e poderão aprender rotas (estáticas e dinâmicas via propagação) nesta tabela de rotas.

   * **Propagação:** As conexões propagam rotas para tabelas de rotas. Selecionar VNets 1, 2 e 3 permitirá propagar rotas de VNets 1, 2 e 3 para esta tabela de rotas. Não é necessário propagar rotas de conexões de ramificação para RT_V2B, uma vez que o tráfego de VNet de ramificação passa pelo NVA na VNet 4.
  
1. Edite a tabela de rotas padrão **Defaultroutetable**.

   Todas as conexões VPN, ExpressRoute e VPN de usuário são associadas à tabela de rotas padrão. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada de rota estática agregada para VNets 1, 2 e 3 (10.1.0.0/16 na **Figura 2**) com o próximo salto como a conexão VNet 4. Você também precisa configurar uma rota estática na conexão da VNet 4 para os prefixos agregados da VNet 1, 2 e 3 e indicar o próximo salto como o IP específico do NVA na VNet 4.

   * **Associação:** Certifique-se de que a opção para branches (VPN/ER/P2S) esteja selecionada, garantindo que as conexões de ramificação locais estejam associadas a *defaultroutetable*.

   * **Propagação de:** Certifique-se de que a opção para branches (VPN/ER/P2S) esteja selecionada, garantindo que as conexões locais estejam propagando rotas para o *defaultroteiatable*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 2" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
