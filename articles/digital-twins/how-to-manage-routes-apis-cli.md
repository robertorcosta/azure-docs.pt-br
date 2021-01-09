---
title: Gerenciar pontos de extremidade e rotas (APIs e CLI)
titleSuffix: Azure Digital Twins
description: Consulte como configurar e gerenciar pontos de extremidade e rotas de eventos para dados de gêmeos digital do Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 33b30f29146e446c5525b1bbcfd76af71c557702
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045302"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gerenciar pontos de extremidade e rotas no gêmeos digital do Azure (APIs e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

No Azure digital gêmeos, você pode rotear [notificações de eventos](how-to-interpret-event-data.md) para serviços de downstream ou recursos de computação conectados. Isso é feito primeiro Configurando **pontos de extremidade** que podem receber os eventos. Em seguida, você pode criar  [**rotas de eventos**](concepts-route-events.md) que especificam quais eventos gerados pelo Azure digital gêmeos são entregues a quais pontos de extremidade.

Este artigo orienta você pelo processo de criação de pontos de extremidade e rotas com as [APIs de rotas de eventos](/rest/api/digital-twins/dataplane/eventroutes), o [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)e a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Como alternativa, você também pode gerenciar pontos de extremidade e rotas com o [portal do Azure](https://portal.azure.com). Para obter uma versão deste artigo que usa o portal em vez disso, consulte [*como: gerenciar pontos de extremidade e rotas (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

* Você precisará de uma **conta do Azure** (você pode configurar uma gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Você precisará de uma **instância do gêmeos digital do Azure** em sua assinatura do Azure. Se você ainda não tiver uma instância, poderá criar uma usando as etapas em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-cli.md). Faça com que os seguintes valores da configuração sejam úteis para uso posterior neste artigo:
    - Nome da instância
    - Grupo de recursos
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto de extremidade para o gêmeos digital do Azure

Estes são os tipos de pontos de extremidade com suporte que você pode criar para sua instância:
* [Grade de Eventos](../event-grid/overview.md)
* [Hubs de Evento](../event-hubs/event-hubs-about.md)
* [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de ponto de extremidade, consulte [*escolher entre os serviços de mensagens do Azure*](../event-grid/compare-messaging-services.md).

Para vincular um ponto de extremidade ao Azure digital gêmeos, o tópico da grade de eventos, o Hub de eventos ou o barramento de serviço que você está usando para o ponto de extremidade precisa já existir. 

### <a name="create-an-event-grid-endpoint"></a>Criar um ponto de extremidade de grade de eventos

O exemplo a seguir mostra como criar um ponto de extremidade do tipo grade de evento usando o CLI do Azure. Você pode usar [Azure cloud Shell](https://shell.azure.com)ou [instalar a CLI localmente](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

Primeiro, crie um tópico de grade de eventos. Você pode usar o comando a seguir ou exibir as etapas em mais detalhes visitando [a seção *criar um tópico personalizado*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) do guia de início rápido *eventos personalizados* da grade de eventos.

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Para gerar uma lista de nomes de regiões do Azure que podem ser passados para comandos na CLI do Azure, execute este comando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Depois de criar o tópico, você pode vinculá-lo ao Azure digital gêmeos com o seguinte [comando da CLI do Azure digital gêmeos](how-to-use-cli.md):

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Agora, o tópico da grade de eventos está disponível como um ponto de extremidade dentro do Azure digital gêmeos, sob o nome especificado com o `--endpoint-name` argumento. Normalmente, você usará esse nome como o destino de uma **rota de evento**, que você criará [posteriormente neste artigo](#create-an-event-route) usando a API do serviço de gêmeos digital do Azure.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Criar um hub de eventos ou um ponto de extremidade do barramento de serviço

O processo para a criação de hubs de eventos ou pontos de extremidade do barramento de serviço é semelhante ao processo da grade de eventos mostrado acima.

Primeiro, crie seus recursos que você usará como o ponto de extremidade. Aqui está o que é necessário:
* Barramento de serviço: _namespace do barramento_ de serviço, tópico do barramento de _serviço_, regra de _autorização_
* Hubs de eventos: _namespace de hubs de eventos_, Hub de _eventos_, regra de _autorização_

Em seguida, use os seguintes comandos para criar os pontos de extremidade no Azure digital gêmeos: 

* Adicionar ponto de extremidade de tópico do barramento de serviço (requer um recurso de barramento de serviço criado previamente)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Adicionar ponto de extremidade de hubs de eventos (requer o recurso de hubs de eventos pré-criado)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Criar um ponto de extremidade com mensagens mortas

Quando um ponto de extremidade não pode entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um determinado número de vezes, ele pode enviar o evento não entregue para uma conta de armazenamento. Esse processo é conhecido como **mensagens mortas**.

Para saber mais sobre mensagens mortas, consulte [*conceitos: rotas de eventos*](concepts-route-events.md#dead-letter-events). Para obter instruções sobre como configurar um ponto de extremidade com mensagens mortas, continue no restante desta seção.

#### <a name="set-up-storage-resources"></a>Configurar recursos de armazenamento

Antes de definir o local de mensagens mortas, você deve ter uma [conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal) com um [contêiner](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurado em sua conta do Azure. 

Você fornecerá a URL para esse contêiner ao criar o ponto de extremidade posteriormente. O local de mensagens mortas será fornecido ao ponto de extremidade como uma URL de contêiner com um [token SAS](../storage/common/storage-sas-overview.md). Esse token precisa `write` de permissão para o contêiner de destino dentro da conta de armazenamento. A URL totalmente formada estará no formato: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>` .

Siga as etapas abaixo para configurar esses recursos de armazenamento em sua conta do Azure, para se preparar para configurar a conexão de ponto de extremidade na próxima seção.

1. Siga as etapas em [*criar uma conta de armazenamento*](../storage/common/storage-account-create.md?tabs=azure-portal) para criar uma **conta de armazenamento** em sua assinatura do Azure. Anote o nome da conta de armazenamento para usá-lo mais tarde.
2. Siga as etapas em [*criar um contêiner*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para criar um **contêiner** dentro da nova conta de armazenamento. Anote o nome do contêiner para usá-lo mais tarde.
3. Em seguida, crie um **token SAS** para sua conta de armazenamento que o ponto de extremidade pode usar para acessá-lo. Comece navegando até sua conta de armazenamento na [portal do Azure](https://ms.portal.azure.com/#home) (você pode encontrá-la por nome com a barra de pesquisa do Portal).
4. Na página conta de armazenamento, escolha o link _assinatura de acesso compartilhado_ na barra de navegação à esquerda para iniciar a configuração do token SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Página conta de armazenamento no portal do Azure" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Na *página assinatura de acesso compartilhado*, em *serviços permitidos* e *tipos de recursos permitidos*, selecione quaisquer configurações que desejar. Você precisará selecionar pelo menos uma caixa em cada categoria. Em *permissões permitidas*, escolha **gravar** (você também pode selecionar outras permissões, se desejar).
1. Defina quaisquer valores desejados para as configurações restantes.
1. Quando tiver terminado, selecione o botão _gerar cadeia de conexão e SAS_ para gerar o token SAS. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Página conta de armazenamento no portal do Azure mostrando toda a seleção de configuração para gerar um token SAS e realçar o botão ' gerar cadeia de conexão e SAS '" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Isso irá gerar vários valores de cadeia de conexão e SAS na parte inferior da mesma página, sob as seleções de configuração. Role para baixo para exibir os valores e use o ícone *copiar para área de transferência* para copiar o valor do **token SAS** . Salve-o para uso posterior.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Copie o token SAS para usar no segredo de mensagens mortas." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="configure-the-endpoint"></a>Configurar o ponto de extremidade

Para criar um ponto de extremidade com mensagens mortas habilitadas, você precisará criar o ponto de extremidade usando as APIs de Azure Resource Manager. 

1. Primeiro, use a [documentação de APIs de Azure Resource Manager](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para configurar uma solicitação para criar um ponto de extremidade e preencher os parâmetros de solicitação necessários. 

1. Em seguida, adicione um `deadLetterSecret` campo ao objeto Properties no **corpo** da solicitação. Defina esse valor de acordo com o modelo abaixo, que recolocará uma URL do nome da conta de armazenamento, do nome do contêiner e do valor do token SAS que você coletou na [seção anterior](#set-up-storage-resources).
      
  :::code language="json" source="~/digital-twins-docs-samples/api-requests/deadLetterEndpoint.json":::

1. Envie a solicitação para criar o ponto de extremidade.

Para obter mais informações sobre como estruturar essa solicitação, consulte a documentação da API REST do Azure digital gêmeos: [pontos de extremidade – DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Esquema de armazenamento de mensagens

Depois que o ponto de extremidade com mensagens mortas estiver configurado, mensagens mortas serão armazenadas no seguinte formato em sua conta de armazenamento:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

As mensagens inativas corresponderão ao esquema do evento original que deveria ser entregue ao seu ponto de extremidade original.

Aqui está um exemplo de uma mensagem de mensagens mortas para uma [notificação de criação](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)de papel:

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Criar uma rota de eventos

Para realmente enviar dados do Azure digital gêmeos para um ponto de extremidade, você precisará definir uma **rota de evento**. As APIs do gêmeos **EventRoutes** do Azure digital permitem que os desenvolvedores conectem o fluxo de eventos, em todo o sistema e em serviços downstream. Leia mais sobre as rotas de eventos em [*conceitos: roteamento de eventos do gêmeos digital do Azure*](concepts-route-events.md).

Os exemplos nesta seção usam o [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

**Pré-requisito**: você precisa criar pontos de extremidade conforme descrito anteriormente neste artigo antes de passar para a criação de uma rota. Você pode prosseguir com a criação de uma rota de evento quando seus pontos de extremidade tiverem concluído a configuração.

> [!NOTE]
> Se você tiver implantado seus pontos de extremidade recentemente, valide que eles concluíram a implantação **antes** de tentar usá-los para uma nova rota de evento. Se a implantação de rota falhar porque os pontos de extremidade não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se você estiver criando scripts para esse fluxo, talvez queira considerar isso criando em 2-3 minutos de tempo de espera para o serviço de ponto de extremidade concluir a implantação antes de passar para a instalação de rota.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Código de criação com APIs e o SDK do C#

As rotas de eventos são definidas usando [APIs de plano de dados](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Uma definição de rota pode conter estes elementos:
* O nome da rota que você deseja usar
* O nome do ponto de extremidade que você deseja usar
* Um filtro que define quais eventos são enviados para o ponto de extremidade 

Se não houver nenhum nome de rota, nenhuma mensagem será roteada fora do Azure digital gêmeos. Se houver um nome de rota e o filtro for `true` , todas as mensagens serão roteadas para o ponto de extremidade. Se houver um nome de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir que várias notificações e tipos de eventos sejam selecionados. 

`CreateOrReplaceEventRouteAsync` é a chamada do SDK que é usada para adicionar uma rota de evento. Aqui está um exemplo de uso:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

### <a name="event-route-sample-code"></a>Código de exemplo de rota de evento

O método de exemplo a seguir mostra como criar, listar e excluir uma rota de evento:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

## <a name="filter-events"></a>Filtrar eventos

Sem a filtragem, os pontos de extremidade recebem uma variedade de eventos do Azure digital gêmeos:
* Telemetria acionada por [gêmeos digital](concepts-twins-graph.md) usando a API do serviço gêmeos do Azure digital
* Notificações de alteração de propriedade de cópia, disparadas em alterações de propriedade para qualquer cópia na instância de gêmeos digital do Azure
* Eventos de ciclo de vida, acionados quando gêmeos ou relações são criados ou excluídos

Você pode restringir os eventos que estão sendo enviados adicionando um **filtro** para um ponto de extremidade à sua rota de eventos.

Para adicionar um filtro, você pode usar uma solicitação PUT para *https://{Your-Azure-digital-gêmeos-nome_do_host}/eventRoutes/{Event-rota-Name}? API-Version = 2020-10-31* com o seguinte corpo:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Aqui estão os filtros de rota com suporte. Use os detalhes na coluna *Filtrar esquema de texto* para substituir o `<filter-text>` espaço reservado no corpo da solicitação acima.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Gerenciar pontos de extremidade e rotas com a CLI

Os pontos de extremidade e as rotas também podem ser gerenciados usando a CLI do Azure digital gêmeos. Para obter mais informações sobre como usar a CLI e quais comandos estão disponíveis, consulte [*How-to: Use the Azure digital gêmeos CLI*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Próximas etapas

Leia sobre os diferentes tipos de mensagens de evento que você pode receber:
* [*Como: interpretar dados de evento*](how-to-interpret-event-data.md)
