---
title: Configure um túnel de usuário VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de usuário Always On VPN para sua WAN virtual
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502861"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configure um túnel de usuário Always On VPN para WAN Virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a ponto e editar a atribuição do hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição do hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Configure um túnel de usuário

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o comando a seguir:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e limpe a caixa de seleção **Conectar automaticamente.**

   ![Limpeza](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN virtual, consulte o [FAQ](virtual-wan-faq.md).
