---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3cffd2de0763ea6984b64b965ce1214951d3d569
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67056491"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade do cliente VPN posso ter em minha configuração ponto a site?

Depende do SKU do gateway. Para obter mais informações sobre o número de conexões com suporte, consulte [SKUs de gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="supportedclientos"></a>Quais sistemas operacionais cliente posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais cliente:

* Windows 7 (32 bits e 64 bits)
* Windows Server 2008 R2 (somente 64 bits)
* Windows 8.1 (32 bits e 64 bits)
* Windows Server 2012 (somente 64 bits)
* Windows Server 2012 R2 (somente 64 bits)
* Windows Server 2016 (somente 64 bits)
* Windows 10
* Mac OS X versão 10,11 ou superior
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls usando o recurso ponto a site?

O Azure dá suporte a três tipos de opções de VPN ponto a site:

* SSTP (Secure Socket encapsulating Protocol). O SSTP é uma solução baseada em SSL proprietária da Microsoft que pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP de saída que o SSL 443 usa.

* OpenVPN. O OpenVPN é uma solução baseada em SSL que pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP de saída que o SSL 443 usa.

* VPN IKEv2. A VPN IKEv2 é uma solução de VPN IPsec baseada em padrões que usa as portas UDP de saída 500 e 4500 e o protocolo IP no. 50. Os firewalls nem sempre abrem essas portas, portanto, há a possibilidade de VPN IKEv2 não poder atravessar proxies e firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto a site, a VPN será reconectada automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>O ponto a site dá suporte a reconexão automática e DDNS nos clientes VPN?

A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site coexistir para a mesma rede virtual?

Sim. Para o modelo de implantação do Gerenciador de recursos, você deve ter um tipo de VPN RouteBased para seu gateway. Para o modelo de implantação clássico, você precisa de um gateway dinâmico. Não há suporte para ponto a site para gateways de VPN de roteamento estático ou gateways de VPN PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Não. Um cliente ponto a site só pode se conectar a recursos na VNet em que o gateway de rede virtual reside.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanto taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seu local e a Internet. Para um gateway de VPN com apenas conexões VPN ponto a site IKEv2, a taxa de transferência total que você pode esperar depende do SKU do gateway. Para obter mais informações sobre a taxa de transferência, consulte [SKUs de gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Posso usar qualquer cliente VPN de software para ponto a site que dê suporte a SSTP e/ou IKEv2?

Não. Você só pode usar o cliente VPN nativo no Windows para SSTP e o cliente VPN nativo no Mac para IKEv2. No entanto, você pode usar o cliente OpenVPN em todas as plataformas para se conectar por meio do protocolo OpenVPN. Consulte a lista de sistemas operacionais cliente com suporte.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>O Azure dá suporte à VPN IKEv2 com o Windows?

O IKEv2 tem suporte no Windows 10 e Server 2016. No entanto, para usar IKEv2, você deve instalar atualizações e definir um valor de chave do registro localmente. As versões do sistema operacional anteriores ao Windows 10 não têm suporte e só podem usar o protocolo SSTP ou **OpenVPN®**.

Para preparar o Windows 10 ou o Server 2016 para IKEv2:

1. Instale a atualização.

   | Versão do SO | Data | Número/link |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versão 1709 | 22 de março de 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Defina o valor da chave do registro. Crie ou defina a chave "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" REG_DWORD no registro como 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>O que acontece quando eu configuro SSTP e IKEv2 para conexões VPN P2S?

Quando você configura o SSTP e IKEv2 em um ambiente misto (consistindo em dispositivos Windows e Mac), o cliente VPN do Windows sempre tentará o túnel IKEv2 primeiro, mas fará fallback para SSTP se a conexão IKEv2 não for bem-sucedida. MacOSX se conectará somente via IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Além do Windows e Mac, quais outras plataformas dão suporte ao Azure para VPN P2S?

O Azure dá suporte ao Windows, Mac e Linux para VPN P2S.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Já tenho um gateway de VPN do Azure implantado. Posso habilitar o RADIUS e/ou a VPN IKEv2 nele?

Sim, você pode habilitar esses novos recursos em gateways já implantados usando o PowerShell ou o portal do Azure, desde que o SKU de gateway que você está usando ofereça suporte a RADIUS e/ou IKEv2. Por exemplo, o SKU básico do gateway de VPN não oferece suporte a RADIUS ou IKEv2.
