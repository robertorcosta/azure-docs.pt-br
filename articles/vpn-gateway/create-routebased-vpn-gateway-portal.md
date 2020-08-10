---
title: 'Criar um gateway de VPN baseado em rota: Portal'
titleSuffix: Azure VPN Gateway
description: Use o portal do Azure para criar rapidamente um gateway de VPN do Azure baseado em rota, para uma conexão VPN com sua rede local ou para conectar redes virtuais.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: e6d44c9a92ad239c37620ee2e19efcc1777ec3de
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032622"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Criar um gateway de VPN baseado em rotas usando o portal do Azure

Este artigo ajuda você a criar rapidamente um gateway de VPN do Azure baseadas em rota usando o portal do Azure.  Um gateway de VPN é usado ao criar uma conexão VPN à rede local. Você também pode usar um gateway de VPN para se conectar a VNets. 

As etapas neste artigo criarão uma rede virtual, uma sub-rede, uma sub-rede do gateway e um gateway VPN com base em rota (gateway de rede virtual). Concluída a criação de gateway, você pode criar conexões. Estas etapas exigem uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Configurar e criar o gateway

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>O SKU do gateway básico não oferece suporte à autenticação IKEv2 ou RADIUS. Se você planeja ter clientes Mac conectados à sua rede virtual, não use a SKU básica.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Veja o Gateway de VPN

1. Depois de criar o gateway, navegue para VNet1 no portal. O gateway de VPN é exibido na página Visão geral de como um dispositivo conectado.

   ![Dispositivos conectados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivos conectados")

2. Na lista de dispositivos, clique em **VNet1GW** para exibir mais informações.

   ![Exibir gateway de VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Exibir gateway de VPN")

## <a name="next-steps"></a>Próximas etapas

Uma vez que o gateway tenha sido criado, você pode criar uma conexão entre sua rede virtual e outra rede virtual. Ou criar uma conexão entre sua rede virtual e redes locais.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão com outra rede virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
