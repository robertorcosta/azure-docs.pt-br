---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/16/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b8a61586acd888301350277d924f3d4fe176b4c8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148200"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Etapa 1 – Navegar para o gateway de rede virtual

1. No [portal do Azure](https://portal.azure.com), navegue até **Todos os recursos**. 
2. Para abrir a página do gateway de rede virtual, navegue até o gateway de rede virtual que você deseja excluir e clique nele.

### <a name="step-2-delete-connections"></a>Etapa 2: Excluir conexões

1. Na páina do gateway de rede virtual, clique em **Conexões** para exibir todas as conexões com o gateway.
2. Clique em **'...'** na linha do nome da conexão e selecione **Excluir** na lista suspensa.
3. Clique em **Sim** para confirmar que você deseja excluir a conexão. Se tiver várias conexões, exclua cada uma delas.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Etapa 3: Excluir o gateway de rede virtual

Tenha em mente que se você tiver uma configuração de P2S nessa VNet além da configuração de S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.

1. Na página do gateway de rede virtual, clique em **Visão geral**.
2. Na página **Visão geral**, clique em **Excluir** para excluir o gateway.
