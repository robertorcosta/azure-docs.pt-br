---
title: Como configurar clientes do OpenVPN para o Gateway de VPN do Azure | Microsoft Docs
description: Etapas para configurar clientes do OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066181"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para o gateway de VPN do Azure

Este artigo ajuda você a configurar clientes do **protocolo OpenVPN &reg; ** .

## <a name="before-you-begin"></a>Antes de começar

Verifique se você concluiu as etapas para configurar o OpenVPN para seu gateway de VPN. Para obter detalhes, confira [Configurar o OpenVPN para Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximas etapas

Se você quiser que os clientes VPN possam acessar recursos em outra VNet, siga as instruções no artigo de [vnet para vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma conexão de vnet para vnet. Certifique-se de habilitar o BGP nos gateways e nas conexões, caso contrário, o tráfego não fluirá.

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
