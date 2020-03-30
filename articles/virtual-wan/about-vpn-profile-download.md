---
title: 'Azure Virtual WAN: Perfis de clientes do usuário VPN'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066196"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhando com perfis de clientes do Usuário VPN

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo ajuda você a obter e entender as informações necessárias para um perfil de cliente DO Usuário VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [Configurar clientes OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a VPN do Usuário WAN Virtual, consulte [Criar uma conexão VPN do usuário](virtual-wan-point-to-site-portal.md).
