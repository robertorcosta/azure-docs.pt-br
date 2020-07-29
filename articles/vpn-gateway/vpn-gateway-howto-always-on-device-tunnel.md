---
title: Configurar um túnel de VPN AlwaysOn
titleSuffix: Azure VPN Gateway
description: Etapas para configurar o túnel de VPN Always On para o gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6db48928ebac115c42c643d669f6541a3654a53a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983114"
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
