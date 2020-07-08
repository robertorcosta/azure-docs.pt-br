---
title: Fontes de evento de ingestão de streaming – Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre streaming de dados em Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 602f5a0df6cbd7c308d45d02795e7404c46c73a7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049742"
---
# <a name="time-series-insights-event-sources"></a>Fontes de eventos de Time Series Insights

 Seu ambiente de TSI pode ter até duas origens de eventos de streaming. Há suporte para dois tipos de recursos do Azure como entradas:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

Os eventos devem ser enviados como JSON codificado em UTF-8.

## <a name="create-or-edit-event-sources"></a>Criar ou editar origens do evento

Os recursos de origem do evento podem residir na mesma assinatura do Azure que seu ambiente de Time Series Insights ou em uma assinatura diferente. Você pode usar o [portal do Azure](time-series-insights-update-create-environment.md#create-a-preview-payg-environment), [CLI do Azure](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights), [modelos de ARM](time-series-insights-manage-resources-using-azure-resource-manager-template.md)e a [API REST](https://docs.microsoft.com/rest/api/time-series-insights/management/eventsources) para criar, editar ou remover as fontes de evento do seu ambiente.

Quando você se conecta a uma origem de evento, seu ambiente de TSI lerá todos os eventos atualmente armazenados em seu IOT ou hub de eventos, começando com o evento mais antigo.

> [!IMPORTANT]
>
> * Você pode experimentar alta latência inicial ao anexar uma origem de evento ao seu ambiente de versão prévia.
> A latência de origem do evento depende do número de eventos atualmente no Hub IoT ou no Hub de Eventos.
> * A alta latência diminuirá após os dados de origem do evento serem ingeridos pela primeira vez. Envie um tíquete de suporte pelo portal do Azure se você tiver uma alta latência contínua.

## <a name="streaming-ingestion-best-practices"></a>Práticas recomendadas de ingestão de streaming

* Sempre crie um grupo de consumidores exclusivo para seu ambiente de TSI para consumir dados da origem do evento. Reutilizar grupos de consumidores pode causar desconexões aleatórias e pode resultar em perda de dados.

* Configure seu ambiente de TSI e o Hub IoT e/ou hubs de eventos na mesma região do Azure. Embora seja possível configurar uma origem de evento em uma região separada, esse cenário não tem suporte e não podemos garantir a alta disponibilidade.

* Não ultrapasse o limite de taxa de [transferência](concepts-streaming-throughput-limitations.md) do seu ambiente ou por limite de partição.

* Configure um [alerta](https://review.docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency?branch=pr-en-us-117938#monitor-latency-and-throttling-with-alerts) de latência para ser notificado se o seu ambiente estiver enfrentando problemas de processamento de dados.

* Use a ingestão de streaming somente para dados quase em tempo real e recentes, não há suporte para streaming de dados históricos.

* Entenda como as propriedades serão escapadas e [dados JSON mesclados e armazenados.](./concepts-json-flattening-escaping-rules.md)

* Siga o princípio de privilégios mínimos ao fornecer cadeias de conexão de origem de evento. Para os hubs de eventos, configure uma política de acesso compartilhado somente com a Declaração *Enviar* e para o Hub IOT, use somente a permissão de *conexão de serviço* .

### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

No momento, o uso do pipeline de streaming para importar dados históricos não tem suporte na Versão Prévia do Azure Time Series Insights. Se você precisar importar dados passados para o seu ambiente, siga as diretrizes abaixo:

* Não transmita dados dinâmicos e históricos em paralelo. A ingestão de dados fora de ordem resultará em um desempenho de consulta degradado.
* Ingira dados históricos ordenados por tempo para obter um melhor desempenho.
* Fique dentro dos limites da taxa de transferência de ingestão abaixo.
* Desabilite o Armazenamento Warm se os dados forem mais antigos do que o período de retenção do Armazenamento Warm.

## <a name="event-source-timestamp"></a>Carimbo de hora da origem do evento

Ao configurar uma origem de evento, você será solicitado a fornecer uma propriedade de ID de carimbo de data/hora. A propriedade Timestamp é usada para rastrear eventos ao longo do tempo, essa é a hora que será usada como a $event. $ts nas [APIs de consulta de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute) e na série de plotagem no Gerenciador do TSI. Se nenhuma propriedade for fornecida no momento da criação ou se a propriedade Timestamp estiver ausente de um evento, o Hub IoT do evento ou o tempo enfileirado dos hubs de eventos será usado como o padrão. Os valores de propriedade timestamp são armazenados em UTC.

Em geral, os usuários optarão por personalizar a propriedade Timestamp e usará a hora em que o sensor ou a marca gerou a leitura, em vez de usar o tempo enfileirado do Hub padrão. Isso é particularmente necessário quando os dispositivos têm perda de conectividade intermitente e um lote de mensagens atrasadas é encaminhado para TSI.

Se o carimbo de data/hora personalizado estiver dentro de um objeto JSON aninhado ou de uma matriz, você precisará fornecer o nome correto da propriedade seguindo nossas [convenções de nomenclatura de mesclagem e de escape](concepts-json-flattening-escaping-rules.md). Por exemplo, o carimbo de data/hora de origem do evento para a carga JSON mostrado [aqui](concepts-json-flattening-escaping-rules.md#example-a) deve ser inserido como `"values.time"` .

### <a name="time-zone-offsets"></a>Deslocamentos de fuso horário

Os carimbos de data/hora devem ser enviados no formato ISO 8601 e serão armazenados em UTC. Se um deslocamento de fuso horário for fornecido, o deslocamento será aplicado e, em seguida, o horário armazenado e retornado no formato UTC. Se o deslocamento estiver formatado incorretamente, ele será ignorado. Em situações em que sua solução pode não ter o contexto do deslocamento original, você pode enviar os dados de deslocamento em uma propriedade de evento adicional separada para garantir que ele seja preservado e que seu aplicativo possa fazer referência em uma resposta de consulta.

O deslocamento de fuso horário deve ser formatado como um dos seguintes:

± HHMMZ</br>
± HH: MM</br>
± HH: MMZ</br>

## <a name="next-steps"></a>Próximas etapas

* Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados. 

* Entenda as limitações de [taxa de transferência](concepts-streaming-throughput-limitations.md) do seu ambiente




