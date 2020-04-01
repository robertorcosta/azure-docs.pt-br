---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80502042"
---
As rotas definidas pelo usuário com destino 0.0.0.0/0 e NSGs na GatewaySubnet **não são suportadas**. Os gateways criados com essa configuração serão bloqueados da criação. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente. [A propagação da rota BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isso estiver definido como desativado, o gateway não funcionará.
