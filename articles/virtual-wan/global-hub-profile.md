---
title: Baixar perfis VPN do Azure virtual WAN global ou baseado em Hub | Microsoft Docs
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: be2ea92ddbcce7c1e0ab5ba7d172cda0e05984fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753374"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Baixar um perfil global ou baseado em Hub para clientes VPN de usuário

A WAN virtual do Azure oferece dois tipos de conectividade para usuários remotos: global e baseado em Hub. Use as seções a seguir para saber mais sobre o e baixar um perfil. 

> [!IMPORTANT]
> A autenticação RADIUS dá suporte apenas ao perfil baseado em Hub.

## <a name="global-profile"></a>Perfil global

O perfil aponta para um balanceador de carga que inclui todos os hubs de VPN de usuário ativos. O usuário é direcionado para o Hub que está mais próximo do local geográfico do usuário. Esse tipo de conectividade é útil quando os usuários viajam para locais diferentes com frequência. Para baixar o perfil **global** :

1. Navegue até a WAN virtual.
2. Clique em **configuração de VPN de usuário**.
3. Realce a configuração para a qual você deseja baixar o perfil.
4. Clique em **baixar perfil VPN de usuário de WAN virtual**.

   ![Perfil global](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Perfil baseado em Hub

O perfil aponta para um único Hub. O usuário só pode se conectar ao Hub específico usando esse perfil. Para baixar o perfil **baseado em Hub** :

1. Navegue até a WAN virtual.
2. Clique em **Hub** na página Visão geral.

    ![Perfil de Hub 1](./media/global-hub-profile/hub1.png)
3. Clique em **VPN do usuário (ponto a site)**.
4. Clique em **baixar perfil de VPN de usuário do Hub virtual**.

   ![Perfil do Hub 2](./media/global-hub-profile/hub2.png)
5. Verifique **EAPTLS**.
6. Clique em **gerar e baixar perfil**.

   ![Perfil do Hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
