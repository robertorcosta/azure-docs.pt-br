---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275231"
---
**Não há suporte** para rotas definidas pelo usuário com um destino 0.0.0.0/0 e NSGs no GatewaySubnet. Os gateways criados com essa configuração terão a criação bloqueada. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente. A [Propagação de Rotas BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve ser definida como "Habilitada" no GatewaySubnet para garantir a disponibilidade do gateway. Se ela estiver definida como desabilitada, o gateway não funcionará.
