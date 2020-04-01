---
title: Transição para OpenVPN ou IKEv2 da SSTP | Azure VPN Gateway
description: Este artigo ajuda você a entender as maneiras de superar o limite de conexão simultânea de 128 simultâneos do SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 5500d993a4bf3c664f14182d983f9abed8ebb08a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398376"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transição para o protocolo OpenVPN ou IKEv2 do SSTP

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Este artigo se aplica ao modelo de implantação do Resource Manager e fala sobre maneiras de superar o limite de conexão simultânea de 128 simultâneos do SSTP, passando para o protocolo OpenVPN ou IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Qual protocolo o P2S usa?

VPN Ponto a Site pode usar um dos seguintes protocolos:

* **Protocolo&reg; OpenVPN**, um protocolo VPN baseado em SSL/TLS. Uma solução SSL VPN pode penetrar firewalls, já que a maioria dos firewalls abrem a porta TCP 443, que o SSL usa. O OpenVPN pode ser usado para se conectar a partir de dispositivos Android, iOS (versões 11.0 ou superior), Windows, Linux e Mac (versões OSX 10.13 ou superior).

* **Secure Socket Tunneling Protocol (SSTP)**, um protocolo VPN proprietário baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, já que a maioria dos firewalls abrem a porta TCP 443, que o SSL usa. SSTP só tem suporte em dispositivos com Windows. O Azure oferece suporte a todas as versões do Windows com SSTP (Windows 7 e posterior). **O SSTP suporta até 128 conexões simultâneas apenas independentemente do Gateway SKU**.

* VPN IKEv2, uma solução de VPN IPsec baseada em padrões. VPN IKEv2 pode ser usada para se conectar de dispositivos Mac (OSX versões 10.11 e acima).


>[!NOTE]
>O IKEv2 e o OpenVPN para P2S estão disponíveis somente para o modelo de implantação do Resource Manager. Eles não estão disponíveis para o modelo de implantação clássico. O Gateway Básico SKU não suporta protocolos IKEv2 ou OpenVPN. Se você estiver usando o SKU básico, você terá que excluir e recriar um Gateway de Rede Virtual SKU de produção.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migração de SSTP para IKEv2 ou OpenVPN

Pode haver casos em que você deseja suportar mais de 128 conexões P2S simultâneas a um gateway VPN, mas estão usando SSTP. Nesse caso, você precisa mudar para o protocolo IKEv2 ou OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opção 1 - Adicionar IKEv2 além do SSTP no Gateway

Esta é a opção mais simples. SSTP e IKEv2 podem coexistir no mesmo gateway e dar-lhe um maior número de conexões simultâneas. Você pode simplesmente ativar o IKEv2 no gateway existente e rebaixar o cliente.

Adicionar ikev2 a um gateway SSTP VPN existente não afetará os clientes existentes e você pode configurá-los para usar ikev2 em pequenos lotes ou apenas configurar os novos clientes para usar o IKEv2. Se um cliente Windows estiver configurado para SSTP e IKEv2, ele tentará se conectar usando iKEV2 primeiro e se isso falhar, ele voltará ao SSTP.

**O IKEv2 usa portas UDP não padrão, então você precisa garantir que essas portas não estejam bloqueadas no firewall do usuário. As portas em uso são UDP 500 e 4500.**

Para adicionar ikev2 a um gateway existente, basta ir para a guia "configuração ponto a local" no portal Virtual Network Gateway e selecionar **IKEv2 e SSTP (SSL)** na caixa de baixa.

![ponto a ponto](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opção 2 - Remover SSTP e ativar OpenVPN no Gateway

Como o SSTP e o OpenVPN são protocolos baseados em TLS, eles não podem coexistir no mesmo gateway. Se você decidir se afastar do SSTP para o OpenVPN, você terá que desativar o SSTP e ativar o OpenVPN no gateway. Essa operação fará com que os clientes existentes percam a conectividade com o gateway VPN até que o novo perfil seja configurado no cliente.

Você pode ativar o OpenVPN junto com o IKEv2, se desejar. O OpenVPN é baseado em TLS e usa a porta TCP 443 padrão. Para mudar para OpenVPN, vá para a guia "configuração ponto a local" no portal Virtual Network Gateway e selecione **OpenVPN (SSL)** ou **IKEv2 e OpenVPN (SSL)** a partir da caixa de baixa.

![ponto a ponto](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Uma vez configurado o gateway, os clientes existentes não poderão se conectar até que você [implante e configure os Clientes OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Se você estiver usando o Windows 10, você também pode usar o [Cliente Azure VPN para Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

>[!NOTE]
>Para clientes do Windows, é necessário ter direitos de administrador no dispositivo cliente para iniciar a conexão VPN do dispositivo cliente para o Azure.
>

Os usuários usam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure fornece um arquivo compactado de configuração de cliente de VPN que contém as configurações necessárias para esses clientes nativos se conectarem ao Azure.

* Para dispositivos Windows, a configuração de cliente de VPN é composta por um pacote de instalador que os usuários instalam em seus dispositivos.
* Para dispositivos Mac, ela é composta pelo arquivo mobileconfig que os usuários instalem em seus dispositivos.

O arquivo zip também fornece os valores de algumas das configurações importantes no lado do Azure que você pode usar para criar seu próprio perfil para esses dispositivos. Alguns dos valores incluem o endereço de gateway de VPN, os tipos de encapsulamento configurados, rotas e o certificado raiz para validação de gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Qual gateway as SKUs suportam o P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para obter recomendações de SKU de Gateway, confira [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>A SKU Básica não dá suporte à autenticação IKEv2 ou RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Quais políticas IKE/IPsec estão configuradas em gateways VPN para P2S?


**IKEv2**

|**Codificação** | **Integridade** | **Prf** | **Grupo DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**Codificação** | **Integridade** | **Grupo PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Quais políticas TLS estão configuradas em gateways VPN para P2S?
**TLS**

|**Políticas** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Como fazer para configurar uma conexão P2S?

Uma configuração P2S exige algumas etapas específicas. Os seguintes artigos contêm etapas para orientá-lo pela configuração de P2S e links para configurar os dispositivos cliente VPN:

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
