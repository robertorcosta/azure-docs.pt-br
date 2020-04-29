---
title: Configurar um túnel de usuário de VPN AlwaysOn
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de usuário de VPN Always On para sua WAN virtual
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502861"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configurar um túnel de usuário de VPN Always On para WAN virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a site e editar a atribuição de Hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição de Hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Configurar um túnel de usuário

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e desmarque a caixa de seleção **conectar automaticamente** .

   ![Limpeza](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN virtual, consulte as [perguntas frequentes](virtual-wan-faq.md).
