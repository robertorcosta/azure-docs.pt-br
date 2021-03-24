---
title: Fontes de evento de ingestão de streaming – Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre streaming de dados em Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: ec41f7503ec179cb1fa6172e94e613933f719c93
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953577"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights origens de eventos do Gen2

Seu ambiente de Azure Time Series Insights Gen2 pode ter até duas origens de eventos de streaming. Há suporte para dois tipos de recursos do Azure como entradas:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

Os eventos devem ser enviados como JSON codificado em UTF-8.

## <a name="create-or-edit-event-sources"></a>Criar ou editar origens do evento

A origem do evento é o link entre o Hub e o ambiente do Azure Time Series Insights Gen2, e um recurso separado do tipo `Time Series Insights event source` é criado em seu grupo de recursos. O Hub IoT ou os recursos do hub de eventos podem residir na mesma assinatura do Azure que o ambiente do Azure Time Series Insights Gen2 ou em uma assinatura diferente. No entanto, é uma prática recomendada alojar seu ambiente de Azure Time Series Insights e o Hub IoT ou Hub de eventos na mesma região do Azure.

Você pode usar o [portal do Azure](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [CLI do Azure](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [modelos de Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md)e a [API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) para criar, editar ou remover as fontes de evento do seu ambiente.

## <a name="start-options"></a>Opções de inicialização

Ao criar uma origem de evento, você tem a opção de especificar quais dados preexistentes devem ser coletados. Essa configuração é opcional. As seguintes opções estão disponíveis:

| Nome   |  Descrição  |  Exemplo de modelo de Azure Resource Manager |
|----------|-------------|------|
| EarliestAvailable | Ingerir todos os dados pré-existentes armazenados no IoT ou no Hub de eventos | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Comece ingerir dados que chegam depois que a origem do evento é criada. Todos os dados pré-existentes transmitidos antes da criação da origem do evento serão ignorados. Essa é a configuração padrão no portal do Azure   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Seu ambiente ingerirá dados da sua vida útil personalizada em tempo de enfileiramento (UTC). Todos os eventos que foram enfileirados em seu IoT ou Hub de eventos em ou após seu tempo de enfileiramento personalizado serão ingeridos e armazenados. Todos os eventos que chegaram antes do tempo de enfileiramento personalizado serão ignorados. Observe que "tempo de enfileiramento" refere-se ao tempo (em UTC) que o evento chegou ao seu IoT ou Hub de eventos. Isso é diferente de uma [propriedade de carimbo de data/hora](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) personalizada que está dentro do corpo do seu evento. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Se você selecionar EarliestAvailable e tiver muitos dados preexistentes, poderá experimentar alta latência inicial, pois seu ambiente de Azure Time Series Insights Gen2 processa todos os seus dados.
> - Essa alta latência, eventualmente, deve residir conforme os dados são indexados. Envie um tíquete de suporte pelo portal do Azure se você tiver uma alta latência contínua.

* EarliestAvailable

![Diagrama de EarliestAvailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![Diagrama de EventSourceCreationTime](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![Diagrama de CustomEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Práticas recomendadas de ingestão de streaming

- Sempre crie um grupo de consumidores exclusivo para seu ambiente de Azure Time Series Insights Gen2 para consumir dados da origem do evento. Reutilizar grupos de consumidores pode causar desconexões aleatórias e pode resultar em perda de dados.

- Configure o ambiente do Azure Time Series Insights Gen2 e o Hub IoT e/ou hubs de eventos na mesma região do Azure. Embora seja possível configurar uma origem de evento em uma região separada, esse cenário não tem suporte e não podemos garantir a alta disponibilidade.

- Não ultrapasse o limite de taxa de [transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente ou por limite de partição.

- Configure um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de latência para ser notificado se o seu ambiente estiver enfrentando problemas de processamento de dados. Veja as [cargas de trabalho de produção](./concepts-streaming-ingestion-event-sources.md#production-workloads) abaixo para condições de alerta sugeridas.

- Use a ingestão de streaming somente para dados quase em tempo real e recentes, não há suporte para streaming de dados históricos.

- Entenda como as propriedades serão escapadas e [dados JSON mesclados e armazenados.](./concepts-json-flattening-escaping-rules.md)

- Siga o princípio de privilégios mínimos ao fornecer cadeias de conexão de origem de evento. Para os hubs de eventos, configure uma política de acesso compartilhado somente com a Declaração *Enviar* e para o Hub IOT, use somente a permissão de *conexão de serviço* .

> [!CAUTION] 
> Se você excluir o Hub IoT ou o Hub de eventos e recriar um novo recurso com o mesmo nome, será necessário criar uma nova origem do evento e anexar o novo hub IoT ou Hub de eventos. Os dados não serão ingeridos até que você conclua esta etapa.

## <a name="production-workloads"></a>Cargas de trabalho de produção

Além das práticas recomendadas acima, recomendamos que você implemente o seguinte para cargas de trabalho críticas para os negócios.

- Aumente o tempo de retenção de dados do Hub IoT ou do hub de eventos para o máximo de sete dias.

- Crie alertas de ambiente no portal do Azure. Alertas baseados em [métricas](./how-to-monitor-tsi-reference.md#metrics) de plataforma permitem validar o comportamento de pipeline de ponta a ponta. As instruções para criar e gerenciar alertas estão [aqui](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Condições de alerta sugeridas:

  - IngressReceivedMessagesTimeLag é maior que 5 minutos
  - IngressReceivedBytes é 0
- Mantenha a carga de ingestão equilibrada entre o Hub IoT ou as partições do hub de eventos.

### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

No momento, não há suporte para o uso do pipeline de streaming para importar dados históricos no Azure Time Series Insights Gen2. Se você precisar importar dados passados para o seu ambiente, siga as diretrizes abaixo:

- Não transmita dados dinâmicos e históricos em paralelo. A ingestão de dados fora de ordem resultará em um desempenho de consulta degradado.
- Ingira dados históricos ordenados por tempo para obter um melhor desempenho.
- Fique dentro dos limites da taxa de transferência de ingestão abaixo.
- Desabilite o Armazenamento Warm se os dados forem mais antigos do que o período de retenção do Armazenamento Warm.

## <a name="event-source-timestamp"></a>Carimbo de hora da origem do evento

Ao configurar uma origem de evento, você será solicitado a fornecer uma propriedade de ID de carimbo de data/hora. A propriedade Timestamp é usada para rastrear eventos ao longo do tempo, essa é a hora que será usada como carimbo de data/hora `$ts` nas [APIs de consulta](/rest/api/time-series-insights/dataaccessgen2/query/execute) e na série de plotagem no Azure Time Series insights Explorer. Se nenhuma propriedade for fornecida no momento da criação ou se a propriedade Timestamp estiver ausente de um evento, o Hub IoT do evento ou o tempo enfileirado dos hubs de eventos será usado como o padrão. Os valores de propriedade timestamp são armazenados em UTC.

Em geral, os usuários optarão por personalizar a propriedade Timestamp e usará a hora em que o sensor ou a marca gerou a leitura, em vez de usar o tempo enfileirado do Hub padrão. Isso é particularmente necessário quando os dispositivos têm perda de conectividade intermitente e um lote de mensagens atrasadas é encaminhado para Azure Time Series Insights Gen2.

Se o carimbo de data/hora personalizado estiver dentro de um objeto JSON aninhado ou de uma matriz, você precisará fornecer o nome correto da propriedade seguindo nossas [convenções de nomenclatura de mesclagem e de escape](concepts-json-flattening-escaping-rules.md). Por exemplo, o carimbo de data/hora de origem do evento para a carga JSON mostrado [aqui](concepts-json-flattening-escaping-rules.md#example-a) deve ser inserido como `"values.time"` .

### <a name="time-zone-offsets"></a>Deslocamentos de fuso horário

Os carimbos de data/hora devem ser enviados no formato ISO 8601 e serão armazenados em UTC. Se um deslocamento de fuso horário for fornecido, o deslocamento será aplicado e, em seguida, o horário armazenado e retornado no formato UTC. Se o deslocamento estiver formatado incorretamente, ele será ignorado. Em situações em que sua solução pode não ter o contexto do deslocamento original, você pode enviar os dados de deslocamento em uma propriedade de evento adicional separada para garantir que ele seja preservado e que seu aplicativo possa fazer referência em uma resposta de consulta.

O deslocamento de fuso horário deve ser formatado como um dos seguintes:

± HHMMZ</br>
± HH: MM</br>
± HH: MMZ</br>

## <a name="next-steps"></a>Próximas etapas

- Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

- Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente
