---
title: 'WAN virtual do Azure: perfis de cliente VPN de usuário'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066196"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhando com perfis de cliente VPN de usuário

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo ajuda você a obter e entender as informações necessárias para um perfil de cliente VPN de usuário.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre VPN de usuário de WAN virtual, consulte [criar uma conexão VPN de usuário](virtual-wan-point-to-site-portal.md).
