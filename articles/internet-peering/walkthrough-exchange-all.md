---
title: Passo a passo do emparelhamento do Exchange
titleSuffix: Azure
description: Passo a passo do emparelhamento do Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775167"
---
# <a name="exchange-peering-walkthrough"></a>Passo a passo do emparelhamento do Exchange

Esta seção explica os passos que você precisa seguir para configurar e gerenciar um peering exchange.

## <a name="create-an-exchange-peering"></a>Criar um peering de troca
> [!div class="mx-imgBorder"]
> ![Intercâmbio de fluxo de trabalho e estados de conexão](./media/exchange-peering.png)

As seguintes etapas devem ser seguidas para provisão de um peering exchange:
1. Revise a [política de peering da](https://peering.azurewebsites.net/peering) Microsoft para entender os requisitos para peering do Exchange.
1. Encontre o local de peering da Microsoft e o id de instalação de peering no [PeeringDB](https://www.peeringdb.com/net/694)
1. Solicitar troca de pares para um local de peering usando as instruções em [Criar e modificar um peering Exchange usando](howto-exchange-powershell.md) o artigo PowerShell para obter mais detalhes.
1. Depois de enviar uma solicitação de peering, a Microsoft revisará a solicitação e entrará em contato com você, se necessário.
1. Uma vez aprovado, o estado de conexão muda para Aprovado
1. Configure a sessão BGP no seu final e notifique a Microsoft
1. Vamos provisionar a sessão BGP com a política DENY ALL e validar de ponta a ponta.
1. Se for bem-sucedido, você receberá uma notificação de que o estado de conexão de peering está ativo.
1. O tráfego será permitido através do novo peering.

Observe que os estados de conexão não devem ser confundidos com os estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure
As seguintes etapas devem ser seguidas para converter um peering exchange legado para o recurso DoZure:
1. Siga as instruções em [Converter um correspondente legado do Exchange para o recurso DoZure](howto-legacy-exchange-powershell.md)
1. Depois de enviar a solicitação de conversão, a Microsoft analisará a solicitação e entrará em contato com você, se necessário.
1. Uma vez aprovado, você verá o seu Exchange olhando com o estado de conexão como Ativo.

## <a name="deprovision-exchange-peering"></a>Peering de deprovision Exchange
Entre em contato com [a Microsoft para](mailto:peering@microsoft.com) desprovisionar o peering do Exchange.

Quando um peering do Exchange é definido para deprovisionamento, você verá o estado de conexão como **PendingRemove**

> [!NOTE]
> Se você executar o cmdlet PowerShell para excluir o peering do Exchange quando o estado de conexão estiver provisionandoIniciado ou ProvisionadoConcluído a operação falhará.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [pré-requisitos para configurar peering com a Microsoft](prerequisites.md).
