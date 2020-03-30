---
title: Escalabilidade - Hubs de Eventos Azure | Microsoft Docs
description: Este artigo fornece informações sobre como escalar o Azure Event Hubs usando partições e unidades de throughput.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280944"
---
# <a name="scaling-with-event-hubs"></a>Dimensionamento com Hubs de Eventos

Existem dois fatores que influenciam o dimensionamento com os Event Hubs.
*   Unidades de transferência
*   Partições

## <a name="throughput-units"></a>Unidades de transferência

A capacidade de throughput do Event Hubs é controlada por *unidades de throughput*. As unidades de taxa de transferência são unidades de capacidade pré-adquiridas. Um único throughput permite:

* Entrada: até 1 MB por segundo ou 1000 eventos por segundo (o que ocorrer primeiro).
* Entrada: até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade de unidades de transferência adquiridas, a entrada está limitada e uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) é retornada. A saída não gera exceções de limitação, mas ainda é limitada à capacidade das unidades de produtividade adquiridas. Se você receber exceções de taxa de publicação ou estiver esperando ver mais saída, verifique quantas unidades de transferência você comprou para o namespace. Você pode gerenciar unidades de produtividade na folha **Escala** dos namespaces no [portal do Azure](https://portal.azure.com). Você também pode gerenciar unidades de produtividade programaticamente usando as [APIs de Hubs de Eventos](event-hubs-api-overview.md).

As unidades de taxa de transferência são pré-adquiridas e cobradas por hora. Depois de adquiridas, as unidades de taxa de transferência são cobradas por um mínimo de uma hora. Até 20 unidades de produtividade podem ser adquiridas para um namespace de Hubs de Eventos e elas são compartilhadas entre todos os Hubs de Eventos no namespace.

O recurso **inflar automaticamente** dos Hubs de Eventos escala verticalmente automaticamente aumentando o número de unidades de taxa de transferência para atender às necessidades de uso. O aumento de unidades de taxa de transferência evita cenários de limitação, nos quais:

- As taxas de entrada de dados excedem as unidades de taxa de transferência definidas.
- As taxas de solicitação de saída de dados excedem as unidades de taxa de transferência definidas.

O serviço de Hubs de Eventos aumenta a taxa de transferência quando a carga aumentar ultrapassando o limite mínimo, sem quaisquer solicitações com falha com erros de ServerBusy. 

Para obter mais informações sobre o recurso de inflação automática, consulte [Dimensionar automaticamente unidades de throughput](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chave de partição

Use uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de evento de entrada em partições específicas para fins de organização de dados. A chave de partição é um valor fornecido pelo remetente passado para um hub de eventos. Ela é processada por meio de uma função de hash estática, que cria a atribuição de partição. Se você não especificar uma chave de partição ao publicar um evento, uma atribuição de round robin será usada.

O editor de eventos só está ciente da sua chave de partição, não da partição para a qual os eventos são publicados. Essa desassociação de chave e partição isenta o remetente da necessidade de saber muito sobre o processamento de downstream. Uma identidade por dispositivo ou exclusiva do usuário é uma boa chave de partição, mas outros atributos, como geografia, também podem ser usados para agrupar eventos relacionados em uma única partição.


## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

- [Dimensionar automaticamente unidades de transferência](event-hubs-auto-inflate.md)
- [Visão geral do serviço dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
