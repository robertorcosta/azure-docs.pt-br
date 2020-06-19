---
title: 'Gateway de VPN do Azure: Sobre perfis de cliente VPN P2S'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650673"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre perfis de cliente VPN P2S

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo ajudará você a entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A pasta **OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para saber mais, confira [Configurar os clientes OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação do Azure AD estiver selecionada no gateway de VPN, essa pasta não estará presente no arquivo zip. Em vez disso, navegue até a pasta AzureVPN e localize o arquivo azurevpnconfig.xml.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre conexões ponto a site, confira [Sobre ponto a site](point-to-site-about.md).
