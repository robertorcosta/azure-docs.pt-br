---
title: 'Gateway de VPN do Azure: sobre perfis de cliente VPN P2S'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528499"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre perfis de cliente VPN P2S

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo o ajudará a obter e entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows). Essa pasta não estará presente no arquivo zip se a autenticação do Azure AD estiver selecionada no gateway de VPN. Em vez disso, o azurevpnconfig. xml estará na pasta AzureVPN.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre ponto a site, consulte [sobre ponto a site](point-to-site-about.md).
