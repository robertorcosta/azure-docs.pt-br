---
title: 'Como configurar o OpenVPN no gateway de VPN do Azure: PowerShell'
description: Saiba como usar o PowerShell para habilitar o protocolo OpenVPN no gateway de VPN do Azure para um ambiente ponto a site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: c13d14ad2d06cbc43d80c05258bdbd3303da4838
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036820"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurar o OpenVPN para gateway de VPN ponto a site do Azure

Este artigo ajuda você a configurar o **protocolo OpenVPN®** no gateway de VPN do Azure. O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não tiver, use as instruções na etapa 1 para criar uma VPN ponto a site.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. criar uma VPN ponto a site

Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Veja [Criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificado nativa do Azure. 

> [!IMPORTANT]
> Não há suporte para o SKU Básico no OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. habilitar OpenVPN no gateway

Habilite o OpenVPN no gateway. Verifique se o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os comandos a seguir:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
