---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ec684e5e6fa2ef8e9ed30be49f59e8aa7ef3a28b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117121"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN posso ter em minha configuração ponto a site?

Isso depende da SKU de gateway. Para obter mais informações sobre o número de conexões compatíveis, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Quais sistemas operacionais de cliente posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais de cliente:

* Windows 7 (32 bits e 64 bits)
* Windows Server 2008 R2 (somente 64 bits)
* Windows 8.1 (32 bits e 64 bits)
* Windows Server 2012 (somente 64 bits)
* Windows Server 2012 R2 (somente 64 bits)
* Windows Server 2016 (somente 64 bits)
* Windows 10
* Mac OS X versão 10.11 ou superior
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls usando o recurso de ponto a site?

O Azure suporta três tipos de opções de VPN ponto a ponto:

* SSTP (Secure Socket Tunneling Protocol). SSTP é uma solução baseada em SSL proprietária da Microsoft que pode penetrar em firewalls, já que a maioria dos firewalls abrem a porta TCP de saída que o 443 SSL usa.

* Openvpn. OpenVPN é uma solução baseada em SSL que pode penetrar firewalls, já que a maioria dos firewalls abrem a porta TCP de saída que o 443 SSL usa.

* VPN IKEv2. IKEv2 VPN é uma solução iPsec VPN baseada em padrões que usa portas UDP de saída 500 e 4500 e protocolo IP no. 50. Nem sempre os firewalls abrem essas portas, por isso, há uma possibilidade de a VPN IKEv2 não conseguir atravessar proxies e firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto a site, a VPN se reconectará automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A opção de ponto para site dá suporte a reconexão automática e DDNS nos clientes de VPN?

A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site que coexistam para a mesma rede virtual?

Sim. Para o modelo de implantação do Gerenciador de Recursos, você deve ter um tipo de VPN RouteBased para o gateway. Para o modelo de implantação clássica, você precisará de um gateway dinâmico. Não damos suporte a Ponto a Site para o roteamento estático de gateways de VPN ou gateways de VPN PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Posso configurar um cliente Point-to-Site para se conectar a vários gateways de rede virtuais ao mesmo tempo?

Dependendo do software VPN Client usado, você pode ser capaz de se conectar a vários Gateways de Rede Virtual, desde que as redes virtuais conectadas não tenham espaços de endereço conflitantes entre eles ou a rede de com o cliente esteja se conectando.  Embora o Cliente Azure VPN suporte muitas conexões VPN, apenas uma conexão pode ser conectada a qualquer momento.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente de ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Sim, conexões ponto a ponto para um Gateway de rede virtual implantado em um VNet que é associado com outros VNets podem ter acesso a outros VNets peered.  Desde que os VNets peered estejam usando os recursos UseRemoteGateway / AllowGatewayTransit, o cliente Point-to-Site poderá se conectar a esses VNets peered.  Para mais informações, consulte [este](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) artigo.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanta taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis de VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seus locais e na Internet. Para um Gateway de VPN apenas com conexões de VPN de Ponto a Site IKEv2, a taxa de transferência total que você pode esperar depende do SKU do Gateway. Para saber mais sobre taxa de transferência, confira [SKUs de gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Posso usar qualquer cliente de VPN de software para Ponto a Site que dê suporte a SSTP e/ou IKEv2?

Não. Você só pode usar o cliente VPN nativo no Windows para SSTP, e o cliente VPN nativo no Mac para IKEv2. No entanto, você pode usar o cliente OpenVPN em todas as plataformas para se conectar através do protocolo OpenVPN. Consulte a lista dos sistemas operacionais de cliente com suporte.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>O Azure oferece suporte à VPN IKEv2 com o Windows?

O IKEv2 tem suporte no Windows 10 e Server 2016. No entanto, para usar IKEv2, você deve instalar as atualizações e definir um valor de chave do Registro localmente. As versões do Sistema operacional antes do Windows 10 não são suportadas e só podem usar SSTP ou **OpenVPN® Protocol**.

Para preparar o Windows 10 ou Server 2016 para IKEv2:

1. Instale a atualização.

   | Versão do SO | Data | Número/Link |
   |---|---|---|
   | Windows Server 2016<br>Windows 10, versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 Versão 1709 | 22 de março de 2018 | [BDC4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Defina o valor da chave do Registro. Crie ou defina a chave "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" REG_DWORD do Registro como 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>O que acontece quando eu configuro SSTP e IKEv2 para conexões VPN de P2S?

Quando você configura o SSTP e IKEv2 em um ambiente misto (composto por dispositivos com Windows e Mac), o cliente de VPN do Windows sempre tentará primeiro o túnel IKEv2, mas realizará o fail back para SSTP se a conexão IKEv2 não for bem-sucedida. MacOSX só se conecta por meio de IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Além do Windows e Mac, quais outras plataformas possuem suporte Azure para VPN P2S?

O Azure oferece suporte a Windows, Mac e Linux para VPN P2S.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Eu já tenho um Gateway de VPN do Azure implantado. É possível habilitar RADIUS e/ou IKEv2 VPN nele?

Sim, você pode habilitar esses novos recursos em gateways já implantados usando o Powershell ou o portal do Azure, desde que o SKU do gateway que você está usando dê suporte a RADIUS e/ou a IKEv2. Por exemplo, o SKU Básico do gateway de VPN não dá suporte a RADIUS ou a IKEv2.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Como removo a configuração de uma conexão P2S?

Uma configuração P2S pode ser removida usando o Azure CLI e o PowerShell usando os seguintes comandos:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
