---
title: Configurar um túnel de VPN AlwaysOn
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o túnel de dispositivo VPN Always On para WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502848"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configurar um túnel de dispositivo VPN Always On para WAN virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a site e editar a atribuição de Hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição de Hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Configurar o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN virtual, consulte as [perguntas frequentes](virtual-wan-faq.md).