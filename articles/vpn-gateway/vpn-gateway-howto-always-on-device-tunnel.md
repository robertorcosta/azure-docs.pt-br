---
title: Configurar um túnel de VPN AlwaysOn
titleSuffix: Azure VPN Gateway
description: Saiba como configurar Always On, que mantém uma conexão VPN com base em gatilhos, como entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927038"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurar um túnel de dispositivo VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

Configure o gateway de VPN para usar a autenticação IKEv2 e baseada em certificado usando o artigo [Configurar uma conexão VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Configurar o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
