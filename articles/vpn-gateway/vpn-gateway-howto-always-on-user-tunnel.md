---
title: Configurar um túnel de usuário de VPN AlwaysOn
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de usuário de VPN Always On para seu gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984131"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de usuário Always On VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

 Use as instruções no artigo [Configurar uma conexão VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para configurar o gateway de VPN para usar a autenticação IKEv2 e baseada em certificado.

## <a name="configure-a-user-tunnel"></a>Configurar um túnel de usuário

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o comando a seguir:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e desmarque a caixa de seleção **conectar automaticamente** .

   ![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas de conexão que podem ocorrer, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
