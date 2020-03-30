---
title: Configure um túnel VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o túnel do dispositivo Always On VPN para WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502848"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configure um túnel de dispositivo Always On VPN para WAN Virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a ponto e editar a atribuição do hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição do hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Configure o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN virtual, consulte o [FAQ](virtual-wan-faq.md).