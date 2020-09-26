---
title: Rotas de eventos
titleSuffix: Azure Digital Twins
description: Entenda como rotear eventos dentro do Azure digital gêmeos e para outros serviços do Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 96da89fa8d7e4783afa11807534bbaeba52b79fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334252"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Rotear eventos dentro e fora do Azure digital gêmeos

O gêmeos digital do Azure usa **rotas de eventos** para enviar dados para consumidores fora do serviço. 

Durante a visualização, há dois casos principais para enviar dados do gêmeos digital do Azure:
* Envio de dados de um gêmeos no grafo digital do Azure para outro. Por exemplo, quando uma propriedade em uma cópia digital é alterada, convém notificar e atualizar outra cópia digital de maneira adequada.
* Envio de dados para serviços de dados downstream para armazenamento ou processamento adicional (também conhecido como *egresso de dados*). Por exemplo,
  - Um hospital pode querer enviar dados de evento de gêmeos digital do Azure para [Time Series insights (TSI)](../time-series-insights/time-series-insights-update-overview.md), para registrar dados de série temporal de eventos relacionados a Handwashing para análise em massa.
  - Uma empresa que já esteja usando [mapas do Azure](../azure-maps/about-azure-maps.md) pode querer usar o gêmeos digital do Azure para aprimorar sua solução. Eles podem habilitar rapidamente um mapa do Azure depois de configurar o Azure digital gêmeos, colocar as entidades do Azure MAP no gêmeos digital do Azure como [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento ou executar consultas poderosas que aproveitam seus mapas do Azure e os dados do Azure digital gêmeos juntos.

As rotas de eventos são usadas para ambos os cenários.

## <a name="about-event-routes"></a>Sobre as rotas de eventos

Uma rota de eventos permite que você envie dados de eventos do digital gêmeos no Azure digital gêmeos para pontos de extremidade definidos personalizados em suas assinaturas. No momento, há suporte para três serviços do Azure para pontos de extremidade: [Hub de eventos](../event-hubs/event-hubs-about.md), [grade de eventos](../event-grid/overview.md)e barramento de [serviço](../service-bus-messaging/service-bus-messaging-overview.md). Cada um desses serviços do Azure pode ser conectado a outros serviços e atua como o middleware, enviando dados para os destinos finais, como TSI ou Azure Maps para qualquer processamento necessário.

O diagrama a seguir ilustra o fluxo de dados de evento por meio de uma solução de IoT maior com um aspecto de gêmeos digital do Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="O Azure digital gêmeos encaminha dados por meio de pontos de extremidade para vários serviços downstream" border="false":::

Os destinos de downstream típicos para rotas de eventos são recursos como o TSI, mapas do Azure, armazenamento e soluções de análise.

### <a name="event-routes-for-internal-digital-twin-events"></a>Rotas de eventos para eventos digitais de entrelaçamento digital

Durante a versão de visualização atual, as rotas de eventos também são usadas para lidar com eventos no grafo de entrelaçamento e enviar dados de uma atualização digital para a imprensa digital. Isso é feito por meio da conexão de rotas de eventos por meio da grade de eventos para computar recursos, como [Azure Functions](../azure-functions/functions-overview.md). Essas funções definem como o gêmeos deve receber e responder a eventos. 

Quando um recurso de computação deseja modificar o gráfico de entrelaçamento com base em um evento recebido por meio da rota de eventos, é útil saber qual é o que precisa ser modificado antes do tempo. 

Como alternativa, a mensagem de evento também contém a ID da fonte de mensagens que enviou a mensagem, de modo que o recurso de computação pode usar consultas ou percorrer relações para localizar um entrelaçamento de destino para a operação desejada. 

O recurso de computação também precisa estabelecer permissões de segurança e de acesso de forma independente.

Para percorrer o processo de configuração de uma função do Azure para processar eventos de alta segurança digital, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Criar um ponto de extremidade

Para definir uma rota de eventos, os desenvolvedores devem primeiro definir pontos de extremidade. Um **ponto de extremidade** é um destino fora do Azure digital gêmeos que dá suporte a uma conexão de rota. Os destinos com suporte na versão de visualização atual são:
* Tópicos personalizados da grade de eventos
* Hub de evento
* Barramento de Serviço

Os pontos de extremidade são configurados usando as APIs do plano de controle (com suporte da [CLI do gêmeos digital do Azure](how-to-use-cli.md)ou por meio do portal do Azure. Uma definição de ponto de extremidade fornece:
* O nome do ponto de extremidade
* O tipo de ponto de extremidade (grade de eventos, Hub de eventos ou barramento de serviço)
* A cadeia de conexão primária e a cadeia de conexão secundária para autenticar 
* O caminho do tópico do ponto de extremidade, como *Your-topic.westus2.eventgrid.Azure.net*

As APIs de ponto de extremidade que estão disponíveis no plano de controle são:
* Criar ponto de extremidade
* Obter lista de pontos de extremidade
* Obter ponto de extremidade por nome
* Excluir ponto de extremidade por nome

## <a name="create-an-event-route"></a>Criar uma rota de eventos
 
As rotas de eventos são criadas em um aplicativo cliente. Uma maneira de fazer isso é com a `CreateEventRoute` chamada do [SDK do .net (C#)](how-to-use-apis-sdks.md) : 

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

1. Primeiro, um `EventRoute` objeto é criado e o construtor usa o nome de um ponto de extremidade. Este `endpointName` campo identifica um ponto de extremidade como um hub de eventos, uma grade de eventos ou um barramento de serviço. Esses pontos de extremidade devem ser criados em sua assinatura e anexados ao Azure digital gêmeos usando as APIs do plano de controle antes de fazer essa chamada de registro.

2. O objeto de rota de evento também tem um campo de [**filtro**](./how-to-manage-routes-apis-cli.md#filter-events) , que pode ser usado para restringir os tipos de eventos que seguem essa rota. Um filtro de `true` habilita a rota sem filtragem adicional (um filtro de `false` desabilita a rota). 

3. Esse objeto de rota de evento é passado para `CreateEventRoute` , junto com um nome para a rota.

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

As rotas também podem ser criadas usando a [CLI do Azure digital gêmeos](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Tipos de mensagens de evento

Tipos diferentes de eventos no Hub IoT e no Azure digital gêmeos produzem diferentes tipos de mensagens de notificação, conforme descrito abaixo.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte como configurar e gerenciar uma rota de eventos:
* [*Como: gerenciar pontos de extremidade e rotas*](how-to-manage-routes-apis-cli.md)

Ou, consulte como usar Azure Functions para rotear eventos no Azure digital gêmeos:
* [*Como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md)