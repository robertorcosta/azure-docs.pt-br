---
title: Como configurar clientes do OpenVPN para o Gateway de VPN do Azure | Microsoft Docs
description: Etapas para configurar clientes do OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984096"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para o gateway de VPN do Azure

Este artigo ajuda você a configurar clientes do ** &reg; protocolo OpenVPN** .

## <a name="before-you-begin"></a>Antes de começar

Verifique se você concluiu as etapas para configurar o OpenVPN para seu gateway de VPN. Para obter detalhes, confira [Configurar o OpenVPN para Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximas etapas

Se você quiser que os clientes VPN possam acessar recursos em outra VNet, siga as instruções no artigo de [vnet para vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma conexão de vnet para vnet. Certifique-se de habilitar o BGP nos gateways e nas conexões, caso contrário, o tráfego não fluirá.

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
