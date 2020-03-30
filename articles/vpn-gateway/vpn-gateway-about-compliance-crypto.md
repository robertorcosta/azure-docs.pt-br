---
title: 'Gateway Azure VPN: Requisitos criptográficos'
description: Este artigo discute os requisitos criptográficos e os gateways de VPN do Azure
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902834"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos criptográficos e os gateways de VPN do Azure

Este artigo discute como é possível configurar os gateways de VPN do Azure para satisfazer seus requisitos criptográficos para ambos os túneis de VPN S2S entre instalações e as conexões Rede Virtual a Rede Virtual no Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Sobre ikev1 e IKEv2 para conexões Azure VPN

Tradicionalmente, permitimos conexões IKEv1 apenas para SKUs básicos e permitimos conexões IKEv2 para todas as SKUs de gateway VPN que não sejam SKUs básicas. As SKUs Básicas permitem apenas 1 conexão e, juntamente com outras limitações, como desempenho, os clientes que usam dispositivos legados que suportam apenas protocolos IKEv1 estavam tendo pouca experiência. A fim de melhorar a experiência dos clientes usando protocolos IKEv1, agora estamos permitindo conexões IKEv1 para todos os SKUs gateway VPN, exceto Basic SKU. Para obter mais informações, consulte [VPN Gateway SKUs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Conexões Azure VPN Gateway IKEv1 e IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando as conexões IKEv1 e IKEv2 são aplicadas ao mesmo gateway VPN, o trânsito entre essas duas conexões é ativado automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros de política IKE e IPsec para os gateways de VPN do Azure

O padrão de protocolo IKE e IPsec suporta uma ampla gama de algoritmos criptográficos em várias combinações. Se você não solicitar uma combinação específica de algoritmos e parâmetros criptográficos, os gateways azure VPN usarão um conjunto de propostas padrão. Os conjuntos de políticas padrão foram escolhidos para maximizar a interoperabilidade com uma ampla gama de dispositivos de VPN de terceiros em configurações padrão. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de intensidades de chave e algoritmos criptográficos disponíveis.

A política padrão definida para o gateway Azure VPN está listada no artigo: [Sobre dispositivos VPN e parâmetros IPsec/IKE para conexões DE Gateway VPN site-to-site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos

Para comunicações que requerem algoritmos ou parâmetros criptográficos específicos, normalmente devido a requisitos de conformidade ou segurança, agora você pode configurar seus gateways Azure VPN para usar uma política iPsec/IKE personalizada com algoritmos criptográficos específicos e pontos fortes chave, em vez dos conjuntos de políticas padrão do Azure.

Por exemplo, as políticas de modo principal ikEv2 para gateways Azure VPN utilizam apenas diffie-hellman group 2 (1024 bits), enquanto você pode precisar especificar grupos mais fortes para serem usados no IKE, como o Grupo 14 (2048-bit), o Grupo 24 (2048 bits MODP Group) ou o ECP (curva elíptica) grupos) 256 ou 384 bits (Grupo 19 e Grupo 20, respectivamente). Requisitos semelhantes aplicam-se também às políticas de modo rápido do IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política de IPsec/IKE personalizada com gateways de VPN do Azure

Os gateways de VPN agora suportam política de IPsec/IKE personalizada por conexão. Para uma conexão Site a Site ou Rede Virtual a Rede Virtual, você pode escolher uma combinação específica de algoritmos criptográficos para IPsec e IKE com a intensidade de chave desejada, como mostrado neste exemplo:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Você pode criar uma política de IPsec/IKE e aplicar a uma conexão nova ou existente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie redes virtuais, gateways de VPN ou gateways de rede locais para a sua topologia de conectividade, conforme descrito em outros documentos de instruções
2. Criar uma política de IPsec/IKE
3. Você pode aplicar a política ao criar uma conexão VNet-to-VNet ou S2S
4. Se a conexão já foi criada, você poderá aplicar ou atualizar a política para uma conexão existente

## <a name="ipsecike-policy-faq"></a>FAQ sobre a política de IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte [Configurar a política de IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter as instruções passo a passo sobre a configuração da política de IPsec/IKE personalizada em uma conexão.

Consulte também [Conectar vários dispositivos de VPN baseados em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção UsePolicyBasedTrafficSelectors.
