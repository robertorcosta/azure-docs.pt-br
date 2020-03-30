---
title: 'Gateway Azure VPN: sobre os perfis de clientes da P2S VPN'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528499"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes p2S VPN

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo irá ajudá-lo a obter e entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [Configurar clientes OpenVPN para Gateway Azure VPN](vpn-gateway-howto-openvpn-clients.md#windows). Esta pasta não estará presente no arquivo zip se a autenticação Do Azure AD for selecionada no gateway VPN. Em vez disso, o azurevpnconfig.xml estará na pasta AzureVPN.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre ponto a ponto, consulte [Sobre ponto a ponto](point-to-site-about.md).
