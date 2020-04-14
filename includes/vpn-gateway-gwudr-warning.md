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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275231"
---
As rotas definidas pelo usuário com destino 0.0.0.0/0 e NSGs na GatewaySubnet **não são suportadas**. Os gateways criados com essa configuração serão bloqueados da criação. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente. [A propagação da rota BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isso estiver definido como desativado, o gateway não funcionará.
