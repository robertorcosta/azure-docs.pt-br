---
title: 'WAN virtual: crie tabela de rota de hub virtual para NVA: portal Azure'
description: Tabela de rota do hub virtual WAN virtual para direcionar o tráfego para um aparelho virtual de rede usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402930"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rota de hub WAN virtual para NVAs: portal Azure

Este artigo mostra como direcionar o tráfego de um ramo (site local) conectado ao hub WAN virtual para uma rede virtual Spoke (VNet) através de um Network Virtual Appliance (NVA).

![Diagrama de WAN virtual](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios:

*  Você tem um Network Virtual Appliance (NVA). Um Network Virtual Appliance é um software de terceiros de sua escolha que é normalmente provisionado do Azure Marketplace em uma rede virtual.

    * Um endereço IP privado deve ser atribuído à interface de rede NVA.

    * O NVA não é implantado no hub virtual. Ele deve ser implantado em uma rede virtual separada.

    *  A rede virtual NVA pode ter uma ou muitas redes virtuais conectadas a ela. Neste artigo, nos referimos à rede virtual NVA como uma 'VNet falada indireta'. Essas redes virtuais podem ser conectadas ao VNet NVA usando o peering VNet. Os links do VNet Peering são representados por setas pretas na figura acima entre VNet 1, VNet 2 e VNet NVA.
*  Você criou duas redes virtuais. Eles serão usados como vnets falados.

    * Os espaços de endereço sspoke vnet são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se você precisar de informações sobre como criar uma rede virtual, consulte [Criar uma rede virtual](../virtual-network/quick-create-portal.md).

    * Certifique-se de que não há gateways de rede virtuais em nenhum dos VNets.

    * Os VNets não requerem uma sub-rede de gateway.

## <a name="1-sign-in"></a><a name="signin"></a>1. Faça login

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Crie uma WAN virtual

Crie uma WAN virtual. Use os seguintes valores de exemplo:

* **Nome de WAN virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** Oeste dos EUA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Crie um hub

Crie o hub. Use os seguintes valores de exemplo:

* **Localização:** Oeste dos EUA
* **Nome:** westushub
* **Espaço de endereço privado do hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Crie e aplique uma tabela de rota de hub

Atualize o hub com uma tabela de rota de hub. Use os seguintes valores de exemplo:

* **Falou espaços de endereços VNet:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **DMZ NVA interface de rede endereço IP privado:** 10.0.4.5

1. Navegue até sua WAN virtual.
2. Clique no hub para o qual deseja criar uma tabela de rotas.
3. Clique no **...**, e depois clique em **Editar hub virtual**.
4. Na página **Editar hub virtual,** role para baixo e selecione a caixa de seleção **Use a tabela para roteamento**.
5. No **prefixo Destino é** coluna, adicione os espaços de endereço. Na próxima coluna **Enviar para o próximo salto,** adicione o endereço IP privado da interface de rede DMZ NVA.
6. Clique **em Confirmar** para atualizar o recurso do hub com as configurações da tabela de rota.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Crie as conexões VNet

Crie uma conexão de rede virtual a partir de cada VNet com falei indireta (VNet1 e VNet2) para o hub. Essas conexões de rede virtuais são representadas pelas setas azuis na figura acima. Em seguida, crie uma conexão VNet do VNet NVA para o hub (seta preta na figura).

 Para esta etapa, você pode usar os seguintes valores:

| Nome da rede virtual| Nome da conexão|
| --- | --- |
| VNet1 | conexão de teste1 |
| VNet2 | conexão de teste2 |
| NVAVNet | conexão de teste3 |

Repita o seguinte procedimento para cada rede virtual que deseja conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
