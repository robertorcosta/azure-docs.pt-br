---
title: Configure um túnel de usuário VPN sempre ligado
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de usuário Always On VPN para o seu gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502264"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de usuário Always On VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

 Use as instruções no artigo Configurar um artigo [de conexão VPN ponto a ponto para o site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para configurar o gateway VPN para usar ikev2 e autenticação baseada em certificados.

## <a name="configure-a-user-tunnel"></a>Configure um túnel de usuário

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o comando a seguir:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e limpe a caixa de seleção **Conectar automaticamente.**

   ![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas de conexão que possam ocorrer, consulte [os problemas de conexão ponto a local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
