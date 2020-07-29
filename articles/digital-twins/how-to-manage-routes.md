---
title: Gerenciar pontos de extremidade e rotas
titleSuffix: Azure Digital Twins
description: Consulte como configurar e gerenciar pontos de extremidade e rotas de eventos para dados de gêmeos digital do Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8c9fbf7bc45ed2070570faf0d1dfdb15b5fd98ee
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373259"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Gerenciar pontos de extremidade e rotas no gêmeos digital do Azure

No Azure digital gêmeos, você pode rotear [notificações de eventos](how-to-interpret-event-data.md) para serviços de downstream ou para se conectar aos recursos de computação. Isso é feito primeiro Configurando os **pontos de extremidade** que podem receber os eventos, seguidos pelas rotas de [**eventos**](concepts-route-events.md) que especificam quais eventos gerados pelo Azure digital gêmeos são entregues a quais pontos de extremidade.

Os tipos de ponto de extremidade com suporte incluem:
* [Hub de Evento](../event-hubs/event-hubs-about.md)
* [Grade de Eventos](../event-grid/overview.md)
* [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes pontos de extremidade, consulte [*escolher entre os serviços de mensagens do Azure*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Os pontos de extremidade e as rotas são gerenciados com as [**APIs do EventRoutes**](how-to-use-apis-sdks.md), o [SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md). Eles também podem ser gerenciados por meio do [portal do Azure](https://portal.azure.com).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto de extremidade para o gêmeos digital do Azure

Para vincular um ponto de extremidade ao Azure digital gêmeos, o Hub de eventos, o tópico da grade de eventos ou o barramento de serviço que você está usando para o ponto de extremidade precisa já existir. 

O exemplo a seguir mostra como criar um tópico de grade de eventos usando o CLI do Azure:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Para gerar uma lista de nomes de regiões do Azure que podem ser passados para comandos na CLI do Azure, execute este comando:
> ```azurecli
> az account list-locations -o table
> ```

Depois de criar o tópico, você pode vinculá-lo ao Azure digital gêmeos com o seguinte comando:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Isso torna o tópico da grade de eventos disponível dentro do Azure digital gêmeos, sob o nome especificado com o `--endpoint-name` argumento. Normalmente, você usará esse nome como o destino de uma **rota de evento**, que será criada na próxima seção usando a API do serviço de gêmeos digital do Azure.

Existem comandos equivalentes para os pontos de extremidade do hub de eventos e do barramento de serviço:

* Adicionar ponto de extremidade de tópico do barramento de serviço (requer um recurso de barramento de serviço criado previamente)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Adicionar ponto de extremidade do hub de eventos (requer o recurso de Hub de eventos pré-criado)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Gerenciar rotas de eventos com APIs

Para realmente enviar dados do Azure digital gêmeos para um ponto de extremidade, você precisa definir uma rota de evento. As APIs do gêmeos **EventRoutes** do Azure digital permitem que os desenvolvedores conectem o fluxo de eventos, em todo o sistema e em serviços downstream. Leia mais sobre as rotas de eventos em [*conceitos: roteamento de eventos do gêmeos digital do Azure*](concepts-route-events.md).

Você pode prosseguir com a criação de uma rota de evento quando seus pontos de extremidade tiverem concluído a configuração.

>[!NOTE]
>Se você tiver implantado seus pontos de extremidade recentemente, valide que eles concluíram a implantação **antes** de tentar usá-los para uma nova rota de evento. Se a implantação de rota falhar porque os pontos de extremidade não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se você estiver criando scripts para esse fluxo, talvez queira considerar isso criando em 2-3 minutos de tempo de espera para o serviço de ponto de extremidade concluir a implantação antes de passar para a instalação de rota.

Os exemplos neste artigo usam o SDK do C#.

As rotas de eventos são definidas usando APIs de plano de dados. Uma definição de rota pode conter estes elementos:
* A ID de rota que você deseja usar
* O nome do ponto de extremidade que você deseja usar
* Um filtro que define quais eventos são enviados para o ponto de extremidade 

Se não houver nenhuma ID de rota, nenhuma mensagem será roteada fora do Azure digital gêmeos. Se houver uma ID de rota e o filtro for `true` , todas as mensagens serão roteadas para o ponto de extremidade. Se houver uma ID de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir que várias notificações e tipos de eventos sejam selecionados. 

`CreateEventRoute`é a chamada do SDK que é usada para adicionar uma rota de evento. Aqui está um exemplo de uso:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

### <a name="event-route-sample-code"></a>Código de exemplo de rota de evento

O exemplo de código a seguir mostra como criar, listar e excluir uma rota de evento:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrar eventos

Sem a filtragem, os pontos de extremidade recebem uma variedade de eventos do Azure digital gêmeos:
* Telemetria acionada por [gêmeos digital](concepts-twins-graph.md) usando a API do serviço gêmeos do Azure digital
* Notificações de alteração de propriedade de cópia, disparadas em alterações de propriedade para qualquer cópia na instância de gêmeos digital do Azure
* Eventos de ciclo de vida, acionados quando gêmeos ou relações são criados ou excluídos
* Eventos de alteração de modelo, acionados quando [modelos](concepts-models.md) configurados em uma instância de gêmeos digital do Azure são adicionados ou excluídos

Você pode restringir os eventos que estão sendo enviados adicionando um filtro a um ponto de extremidade.

Para adicionar um filtro, você pode usar uma solicitação PUT para *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* com o seguinte corpo:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Aqui estão os filtros de rota com suporte.

| Nome do filtro | Descrição | Filtrar esquema | Valores com suporte | 
| --- | --- | --- | --- |
| Type | O [tipo de evento](./concepts-route-events.md#types-of-event-messages) que flui pela sua instância de cópia digital | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Fonte | Nome da instância do gêmeos digital do Azure | `"filter" : "source = '<hostname>'"`|  **Para notificações**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Para telemetria**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Assunto | Uma descrição do evento no contexto da origem do evento acima | `"filter": " subject = '<subject>'"` | **Para notificações**: o assunto é`<twinid>` <br> ou um formato de URI para entidades, que são identificadas exclusivamente por várias partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para telemetria**: o assunto é o caminho do componente (se a telemetria for emitida de um componente de entrelaçamento), como `comp1.comp2` . Se a telemetria não for emitida de um componente, o campo assunto estará vazio. |
| Esquema de dados | ID do modelo DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Para telemetria**: o esquema de dados é a ID do modelo de a/ou o componente que emite a telemetria <br>**Para notificações**: não há suporte para o esquema de dados|
| Tipo de conteúdo | Tipo de conteúdo do valor de dados | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Versão de especificação | A versão do esquema de evento que você está usando | `"filter": "specversion = '<version>'"` | Deve ser `1.0`. Isso indica o esquema CloudEvents versão 1,0 |
| Verdadeiro/falso | Permite criar uma rota sem filtragem ou desabilitar uma rota | `"filter" : "<true/false>"` | `true`= a rota está habilitada sem filtragem <br> `false`= a rota está desabilitada |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Também é possível combinar filtros usando as seguintes operações:

| Nome do filtro | Filtrar esquema | Exemplo | 
| --- | --- | --- |
| E/OU | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| E/OU | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Operações aninhadas | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Durante a visualização, há suporte apenas para igualdade de cadeia de caracteres (=,! =).

Quando você implementa ou atualiza um filtro, a alteração pode levar alguns minutos para ser refletida no pipeline de dados.

## <a name="manage-endpoints-and-routes-with-cli"></a>Gerenciar pontos de extremidade e rotas com a CLI

Os pontos de extremidade e as rotas também podem ser gerenciados usando a CLI do Azure digital gêmeos. Os comandos podem ser encontrados em [*How-to: Use the Azure digital gêmeos CLI*](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Monitorar rotas de eventos

As métricas de roteamento, como contagem, latência e taxa de falha podem ser exibidas no [portal do Azure](https://portal.azure.com/). 

Na página inicial do portal, pesquise sua instância do gêmeos digital do Azure para obter seus detalhes. Selecione a opção **métricas** no menu da instância do gêmeos digital do Azure para abrir a página de *métricas* .

:::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Captura de tela mostrando a página de métricas do Azure digital gêmeos":::

A partir daqui, você pode exibir as métricas para sua instância e criar exibições personalizadas.

## <a name="next-steps"></a>Próximas etapas

Leia sobre os diferentes tipos de mensagens de evento que você pode receber:
* [*Como: interpretar dados de evento*](how-to-interpret-event-data.md)
