---
title: 'Cenário: rota para serviços compartilhados VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento – configure rotas para acessar uma VNet de serviço compartilhado com uma carga de trabalho que você deseja que cada VNet e Branch acessem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567068"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Cenário: rota para serviços compartilhados VNets

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é configurar rotas para acessar uma VNet de **serviço compartilhado** com carga de trabalho que você deseja que cada VNet e ramificação (VPN/er/P2S) acesse. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar esse cenário, leve em consideração as seguintes etapas:

1. Identifique a VNet **dos serviços compartilhados** .
2. Crie uma tabela de rotas personalizada. No exemplo, nos referimos a essa tabela de rotas como **RT_SHARED**. Para obter as etapas para criar uma tabela de rotas, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md). Use os seguintes valores como uma diretriz:

   * **Associação**
     * Para **VNets, *exceto* a VNet dos serviços compartilhados**, selecione o VNets a ser isolado. Isso implicará que todos esses VNets (exceto a VNet dos serviços compartilhados) poderão alcançar o destino com base nas rotas de RT_SHARED tabela de rotas.

   * **Propagação**
      * Para **branches**, propagar rotas para esta tabela de rotas, além de outras tabelas de rotas que você já tenha selecionado. Devido a essa etapa, RT_SHARED tabela de rotas aprenderá as rotas de todas as conexões de ramificação (VPN/ER/VPN de usuário).
      * Para **VNets**, selecione a **VNet dos serviços compartilhados**. Devido a essa etapa, RT_SHARED tabela de rotas aprenderá as rotas da conexão VNet de serviços compartilhados.

     Isso resultará nas alterações de configuração de roteamento, como mostra a figura abaixo

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNet de serviços compartilhados":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
