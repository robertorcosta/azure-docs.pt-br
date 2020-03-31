---
title: 'Como configurar o OpenVPN no Gateway Azure VPN: PowerShell'
description: Etapas para configurar o OpenVPN para Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162400"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configure o OpenVPN para o Gateway VPN ponto a ponto do Azure

Este artigo ajuda você a configurar **o Protocolo OpenVPN®** no Gateway Azure VPN. O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não tiver, use as instruções na etapa 1 para criar uma VPN ponto a site.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Crie uma VPN ponto a ponto

Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Veja [Criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificado nativa do Azure. 

> [!IMPORTANT]
> Não há suporte para o SKU Básico no OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Habilite o OpenVPN no gateway

Habilite o OpenVPN no gateway. Verifique se o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os comandos a seguir:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
