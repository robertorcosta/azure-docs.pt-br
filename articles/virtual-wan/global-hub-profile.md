---
title: Baixe perfis globais de WAN virtual do Azure ou vpn baseados em hub | Microsoft Docs
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965223"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Baixe um perfil global ou baseado em hub para clientes DE USUÁRIO VPN

O Azure Virtual WAN oferece dois tipos de conectividade para usuários remotos: global e baseado em Hub. Use as seguintes seções para aprender e baixar um perfil.

## <a name="global-profile"></a>Perfil global

O perfil aponta para um balanceador de carga que inclui todos os hubs de VPN do usuário ativo. O usuário é direcionado para o hub mais próximo da localização geográfica do usuário. Esse tipo de conectividade é útil quando os usuários viajam para diferentes locais com frequência. Para baixar o perfil **global:**

1. Navegue até a WAN virtual.
2. Clique **na configuração user VPN**.
3. Destaque a configuração para a qual deseja baixar o perfil.
4. Clique **em Baixar o perfil virtual do usuário WAN VPN**.

   ![Perfil global](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Perfil baseado em hub

O perfil aponta para um único hub. O usuário só pode se conectar ao hub específico usando este perfil. Para baixar o perfil **baseado no hub:**

1. Navegue até a WAN virtual.
2. Clique em **Hub** na página Visão Geral.

    ![Perfil do hub 1](./media/global-hub-profile/hub1.png)
3. Clique **em User VPN (Ponto a ponto)**.
4. Clique **em Baixar o perfil VPN do Usuário do Hub virtual**.

   ![Perfil do hub 2](./media/global-hub-profile/hub2.png)
5. Verifique **eaptls**.
6. Clique **em Gerar e baixar o perfil**.

   ![Perfil do hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
