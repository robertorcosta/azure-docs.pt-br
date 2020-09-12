---
title: 'Gateway de VPN do Azure: Sobre perfis de cliente VPN P2S'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424830"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre perfis de cliente VPN P2S

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo ajudará você a entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A pasta **OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para saber mais, confira [Configurar os clientes OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação do Azure AD estiver selecionada no gateway de VPN, essa pasta não estará presente no arquivo zip. Em vez disso, navegue até a pasta AzureVPN e localize o arquivo azurevpnconfig.xml.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre conexões ponto a site, confira [Sobre ponto a site](point-to-site-about.md).
