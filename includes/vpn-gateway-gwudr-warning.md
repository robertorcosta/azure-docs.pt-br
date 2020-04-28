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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275231"
---
**Não há suporte para**rotas definidas pelo usuário com destino 0.0.0.0/0 e NSGs em GatewaySubnet. Os gateways criados com essa configuração serão bloqueados da criação. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente. A [propagação de rota BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve ser definida como "Enabled" no GatewaySubnet para garantir a disponibilidade do gateway. Se estiver definido como desabilitado, o gateway não funcionará.
