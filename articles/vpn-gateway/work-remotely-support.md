---
title: 'Trabalho remoto com P2S: Gateway Azure VPN'
description: Esta página descreve como você pode aproveitar o Azure Bastion para permitir o funcionamento remoto devido à pandemia COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337115"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Trabalho remoto usando o Gateway Gateway Azure VPN ponto a ponto

>[!NOTE]
>Este artigo descreve como você pode aproveitar o Azure VPN Gateway, a rede Azure, a rede Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e mitigar os problemas de rede que você está enfrentando por causa da crise do COVID-19.
>

Este artigo descreve as opções disponíveis às organizações para configurar o acesso remoto para seus usuários ou para complementar suas soluções existentes com capacidade adicional durante a epidemia de COVID-19.

A solução point-to-site do Azure é baseada em nuvem e pode ser provisionada rapidamente para atender à crescente demanda de usuários para trabalhar em casa. Ele pode aumentar facilmente e desligado tão facilmente e rapidamente quando a capacidade aumentada não é mais necessária.

## <a name="about-point-to-site-vpn"></a>Sobre VPN Ponto a Site

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Esta solução é útil para telecommuters que desejam se conectar a Azure VNets ou data centers locais a partir de um local remoto, como de casa ou de uma conferência. Este artigo descreve como permitir que os usuários trabalhem remotamente com base em vários cenários.

A tabela abaixo mostra os sistemas operacionais do cliente e as opções de autenticação disponíveis para eles. Seria útil selecionar o método de autenticação com base no sistema operacional cliente que já está em uso. Por exemplo, selecione OpenVPN com autenticação baseada em certificado se você tiver uma mistura de sistemas operacionais clientes que precisam se conectar. Além disso, observe que a VPN ponto a ponto só é suportada em gateways VPN baseados em rota.

![ponto a ponto](./media/working-remotely-support/ostable.png "Sistema operacional")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>Cenário 1 - Os usuários precisam de acesso aos recursos apenas no Azure

Nesse cenário, os usuários remotos só precisam acessar os recursos que estão no Azure.

![ponto a ponto](./media/working-remotely-support/scenario1.png "Scanario 1")

Em um nível alto, as seguintes etapas são necessárias para permitir que os usuários se conectem aos recursos do Azure com segurança:

1. Crie um Gateway de rede virtual (se não existir)
2. Configure VPN ponto a ponto no gateway
    3. [Para autenticação de certificado, siga este link](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw).
    2.  [Para O OpenVPN, siga este link](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn).
    3.  [Para autenticação Azure AD, siga este link](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant).
    4.  [Para solucionar problemas de conexões ponto a local, siga este link](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems).
3. Baixe e distribua a configuração do cliente VPN
4. Distribua os certificados (se a autenticação do certificado for selecionada) aos clientes
5. Conecte-se ao Azure VPN

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>Cenário 2 - Os usuários precisam ter acesso aos recursos no Azure e/ou nos recursos do Prem

Nesse cenário, os usuários remotos precisam acessar os recursos que estão no Azure e nos data center on-premises.

![ponto a ponto](./media/working-remotely-support/scenario2.png "Cenário 2")

Em um nível alto, as seguintes etapas são necessárias para permitir que os usuários se conectem aos recursos do Azure com segurança:

1. Crie um Gateway de rede virtual (se não existir)
2. Configure vpn ponto a ponto no gateway (veja cenário 1 acima)
3. Configure o túnel local-a-local no Gateway de Rede Virtual do Azure com o BGP ativado
4. Configure o dispositivo de premissa para se conectar ao Gateway de Rede Virtual do Azure
5. Baixe o perfil point-to-site do portal Azure e distribua aos clientes

[Siga este link para saber como configurar um túnel VPN site-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Perguntas frequentes sobre a autenticação nativa de certificado do Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Perguntas frequentes sobre a autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximas etapas

* [Configure uma conexão P2S - Autenticação Azure AD](openvpn-azure-ad-tenant.md)

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**