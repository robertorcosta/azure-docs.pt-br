---
title: Configurar clientes OpenVPN para a WAN virtual do Azure
description: Etapas para configurar clientes OpenVPN para a WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3e9674916ea9bb5e756a5e57ff18517f53ca7497
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427550"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurar um cliente OpenVPN para a WAN virtual do Azure

Este artigo ajuda você a configurar clientes do **&reg; protocolo OpenVPN** . Você também pode usar o cliente VPN do Azure para Windows 10 para se conectar por meio do protocolo OpenVPN 

## <a name="before-you-begin"></a>Antes de começar

Crie uma configuração de VPN de usuário (ponto a site). Certifique-se de selecionar "OpenVPN" para o tipo de túnel. Para obter as etapas, consulte [criar uma configuração de P2S para a WAN virtual do Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre VPN de usuário (ponto a site), consulte [criar conexões VPN de usuário](virtual-wan-point-to-site-portal.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
