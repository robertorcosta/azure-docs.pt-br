---
title: 'Azure ExpressRoute: Sobre criptografia'
description: Saiba mais sobre a criptografia ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: 7dd9106539b6756d74629ac663241a5b5562cefb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75437054"
---
# <a name="expressroute-encryption"></a>Criptografia ExpressRoute
 
O ExpressRoute suporta algumas tecnologias de criptografia para garantir a confidencialidade e a integridade dos dados que atravessam entre sua rede e a rede da Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Criptografia ponto a ponto por MACsec FAQ
MACsec é um [padrão IEEE](https://1.ieee802.org/security/802-1ae/). Ele criptografa dados no nível de controle de acesso de mídia (MAC) ou na camada de rede 2. Você pode usar o MACsec para criptografar os links físicos entre seus dispositivos de rede e dispositivos de rede da Microsoft quando você se conectar à Microsoft via [ExpressRoute Direct](expressroute-erdirect-about.md). O MACsec está desativado nas portas ExpressRoute Direct por padrão. Você traz sua própria chave MACsec para criptografia e armazene-a no [Azure Key Vault](../key-vault/key-vault-overview.md). Você decide quando girar a chave. Veja outras perguntas frequentes abaixo.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Posso ativar o MACsec no meu circuito ExpressRoute provisionado por um provedor ExpressRoute?
Não. A MACsec criptografa todo o tráfego em um link físico com uma chave de propriedade de uma entidade (ou seja, cliente). Portanto, está disponível apenas no ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Posso criptografar alguns circuitos do ExpressRoute nas minhas portas ExpressRoute Direct e deixar outros circuitos nas mesmas portas descriptografados? 
Não. Uma vez que o MACsec esteja ativado todo o tráfego de controle de rede, por exemplo, o tráfego de dados BGP e o tráfego de dados do cliente são criptografados. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando eu ativar/desativar o MACsec ou atualizar a tecla MACsec, minha rede no local perderá a conectividade com a Microsoft pelo ExpressRoute?
Sim. Para a configuração MACsec, suportamos apenas o modo de chave pré-compartilhado. Isso significa que você precisa atualizar a chave em seus dispositivos e na Microsoft (através de nossa API). Esta mudança não é atômica, então você perderá conectividade quando houver uma incompatibilidade chave entre os dois lados. Recomendamos fortemente que você agende uma janela de manutenção para a mudança de configuração. Para minimizar o tempo de inatividade, sugerimos que você atualize a configuração em um link do ExpressRoute Direct de cada vez depois de mudar o tráfego da rede para o outro link.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>O tráfego continuará a fluir se houver uma incompatibilidade na chave MACsec entre meus dispositivos e os da Microsoft?
Não. Se o MACsec estiver configurado e ocorrer uma incompatibilidade de chaves, você perderá a conectividade com a Microsoft. Em outras palavras, não voltaremos a uma conexão não criptografada, expondo seus dados. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Habilitar o MACsec no ExpressRoute Direct degradará o desempenho da rede?
A criptografia e descriptografia MACsec ocorre no hardware dos roteadores que usamos. Não há impacto de desempenho do nosso lado. No entanto, você deve verificar com o fornecedor de rede os dispositivos que você usa e ver se o MACsec tem alguma implicação de desempenho.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>quais suítes cifradas são suportadas para criptografia?
Apoiamos o AES128 e o AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Criptografia de ponta a ponta por FAQ IPsec
IPsec é um [padrão IETF](https://tools.ietf.org/html/rfc6071). Ele criptografa dados no nível ip (Internet Protocol) ou na Camada de Rede 3. Você pode usar o IPsec para criptografar uma conexão de ponta a ponta entre sua rede local e sua rede virtual (VNET) no Azure. Veja outras perguntas frequentes abaixo.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Posso habilitar o IPsec além do MACsec nas minhas portas ExpressRoute Direct?
Sim. O MACsec protege as conexões físicas entre você e a Microsoft. O IPsec protege a conexão de ponta a ponta entre você e suas redes virtuais no Azure. Você pode habilitá-los independentemente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Posso usar o gateway Azure VPN para configurar o túnel IPsec entre minha rede local e minha rede virtual Azure?
Sim. Você pode configurar este túnel IPsec sobre o Microsoft Peering do seu circuito ExpressRoute. Siga nosso [guia de configuração](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Posso usar o gateway Azure VPN para configurar o túnel IPsec sobre o Azure Private Peering?
Se você adotar o Azure Virtual WAN, poderá seguir [essas etapas](../virtual-wan/vpn-over-expressroute.md) para criptografar a conexão de ponta a ponta. Se você tiver o Azure VNET regular, você pode implantar um gateway VPN de terceiros em seu VNET e estabelecer um túnel IPsec entre ele e seu gateway VPN no local.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual é o throughput que terei depois de ativar o IPsec na minha conexão ExpressRoute?
Se o gateway Azure VPN for usado, verifique os [números de desempenho aqui](../vpn-gateway/vpn-gateway-about-vpngateways.md). Se um gateway VPN de terceiros for usado, verifique com o fornecedor os números de desempenho.

## <a name="next-steps"></a>Próximas etapas
Consulte [Configurar MACsec](expressroute-howto-macsec.md) para obter mais informações sobre a configuração do MACsec.

Consulte [Configurar IPsec](site-to-site-vpn-over-microsoft-peering.md) para obter mais informações sobre a configuração do IPsec.
