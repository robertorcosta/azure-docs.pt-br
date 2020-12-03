---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557699"
---
1. Na página da sua WAN Virtual, selecione **Configurações de VPN do usuário**.
1. Na parte superior da página, selecione **Baixar configurações de VPN do usuário**. Ao baixar a configuração no nível de WAN, você obtém um perfil de VPN de usuário baseado no Gerenciador de Tráfego interno. Para obter mais informações sobre perfis globais ou perfis baseados em hub, confira [Perfis de hub](../articles/virtual-wan/global-hub-profile.md). Cenários de failover são simplificados com o perfil global.

   Se, por algum motivo, um hub não estiver disponível, o gerenciamento de tráfego interno fornecido pelo serviço garantirá a conectividade (por meio de um hub diferente) com os recursos do Azure para usuários de ponto a site. Você sempre pode baixar uma configuração de VPN específica do hub navegando até o hub. Em **VPN de Usuário (ponto a site)** , baixe o perfil de **VPN de Usuário** do hub virtual.
1. Depois que o arquivo for criado, selecione o link para baixá-lo.
1. Use o arquivo de perfil para configurar os clientes VPN.