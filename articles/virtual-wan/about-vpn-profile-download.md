---
title: 'WAN virtual do Azure: perfis de cliente VPN de usuário'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753137"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhando com perfis de cliente VPN de usuário

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo ajuda você a obter e entender as informações necessárias para um perfil de cliente VPN de usuário.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A pasta **OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre VPN de usuário de WAN virtual, consulte [criar uma conexão VPN de usuário](virtual-wan-point-to-site-portal.md).
