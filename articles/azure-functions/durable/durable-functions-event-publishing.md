---
title: Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da Grade de Eventos do Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535478"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)

Este artigo mostra como configurar as Durable Functions para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e com falha) em um [Tópico de Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) personalizado.

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações azul/verde**: Você pode querer saber se alguma tarefa está sendo executado antes de implementar a [estratégia de implantação lado](durable-functions-versioning.md#side-by-side-deployments)a lado .

* **Suporte avançado de monitoramento e diagnóstico**: Você pode acompanhar as informações de status de orquestração em uma loja externa otimizada para consultas, como O Banco de Dados SQL do Azure ou o Azure Cosmos DB.

* **Atividade em segundo plano de execução longa**: se você usar Funções Duráveis para uma atividade em segundo plano de execução longa, esse recurso o ajudará a saber o status atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto Funções Duráveis.
* Instale [o Azure Storage Emulator](../../storage/common/storage-use-emulator.md) (somente windows) ou use uma conta de armazenamento Azure existente.
* Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou usar o [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de Grade de Eventos personalizado

Crie um tópico de Grade de Eventos para enviar eventos a partir de Funções Duráveis. As instruções a seguir mostram como criar um tópico usando a CLI do Azure. Você também pode fazer isso [usando o PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [usando o portal Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, o Azure Event Grid não suporta todas as regiões. Para obter informações sobre quais regiões são suportadas, consulte a visão geral da Grade de Eventos do [Azure](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de Grade de Eventos fornece um ponto de extremidade definido pelo usuário no qual você posta seu evento. Substitua `<topic_name>` por um nome exclusivo para o tópico. O nome do tópico deve ser exclusivo, pois se torna uma entrada DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obter o ponto de extremidade e a chave

Obter o ponto de extremidade do tópico. Substitua `<topic_name>` pelo nome escolhido.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenha a chave do tópico. Substitua `<topic_name>` pelo nome escolhido.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora, você pode enviar eventos para o tópico.

## <a name="configure-event-grid-publishing"></a>Configurar a publicação da Event Grid

No projeto de Funções Duráveis, encontre o arquivo `host.json`.

### <a name="durable-functions-1x"></a>Funções duráveis 1.x

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Funções duráveis 2.x

Adicione `notifications` uma seção à `durableTask` propriedade do `<topic_name>` arquivo, substituindo pelo nome escolhido. Se `durableTask` as `extensions` propriedades não existirem, crie-as como este exemplo:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

As possíveis propriedades de configuração do Azure Event Grid podem ser encontradas na [documentação host.json](../functions-host-json.md#durabletask). Depois de configurar `host.json` o arquivo, seu aplicativo de função envia eventos do ciclo de vida para o tópico Event Grid. Isso funciona quando você executa seu aplicativo de função tanto localmente quanto no Azure.

Defina a configuração do aplicativo para a chave do tópico no Aplicativo de Funções e `local.settings.json`. O JSON a seguir é um exemplo do `local.settings.json` para depuração local. Substitua `<topic_key>` pela chave do tópico.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Se você estiver usando o [Emulato de Armazenamento](../../storage/common/storage-use-emulator.md) (somente windows), certifique-se de que ele está funcionando. É recomendável executar o comando `AzureStorageEmulator.exe clear all` antes da execução.

Se você estiver usando uma conta azure `local.settings.json` Storage existente, substitua-a por `UseDevelopmentStorage=true` sua seqüência de conexões.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Usando o portal Azure, crie outro aplicativo de função para ouvir os eventos publicados pelo seu aplicativo Durable Functions. É melhor localizá-lo na mesma região que o tópico Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de Grade de Eventos

Crie uma função para receber os eventos de ciclo de vida. Selecione **Função Personalizada**.

![Selecione Criar uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Escolha o Gatilho da grade de eventos e selecione um idioma.

![Selecione o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Digite o nome da função e selecione `Create`.

![Crie o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Uma função com o código a seguir é criada:

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Selecione `Add Event Grid Subscription`. Essa operação adiciona uma assinatura de Grade de Eventos para o tópico de Grade de Eventos que você criou. Para obter mais informações, consulte [Conceitos na Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/concepts)

![Selecione o link do Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecione `Event Grid Topics` para **Tipo de Tópico**. Selecione o grupo de recursos que você criou para o tópico de Grade de Eventos. Em seguida, selecione a instância do tópico de Grade de Eventos. Pressione `Create`.

![Criar uma assinatura na Grade de Eventos.](./media/durable-functions-event-publishing/eventsubscription.png)

Agora, você está pronto para receber eventos de ciclo de vida.

## <a name="run-durable-functions-app-to-send-the-events"></a>Execute o aplicativo Funções Duráveis para enviar os eventos

No projeto Funções Duráveis que você configurou anteriormente, comece a depurar em sua máquina local e inicie uma orquestração. O aplicativo publica eventos do ciclo de vida de Funções Duráveis para a Event Grid. Verifique se a Grade de Eventos aciona a função de ouvinte que você criou verificando seus logs no portal Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema do evento

A lista a seguir explica o esquema de eventos de ciclo de vida:

* **`id`**: Identificador exclusivo para o evento Event Grid.
* **`subject`**: Caminho para o tema do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`**: Parâmetros específicos de funções duráveis.
  * **`hubName`**: [Nome do TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Nome da função do orquestrador.
  * **`instanceId`**: Funções duráveis instanceId.
  * **`reason`**: Dados adicionais associados ao evento de rastreamento. Para obter mais informações, consulte [Diagnóstico nas Funções Duráveis (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Status de Execução de Orquestração. Em Execução, Concluído, Falha, Cancelado.
* **`eventType`**: "Evento Orquestrador"
* **`eventTime`**: Horário do evento (UTC).
* **`dataVersion`**: Versão do esquema de eventos do ciclo de vida.
* **`metadataVersion`**: Versão dos metadados.
* **`topic`**: Recurso tópico da grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia [Azure Function Event Grid Trigger Local Debugging](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conhecer a instância de gerenciamento em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Conhecer controle de versão em Funções Duráveis](durable-functions-versioning.md)
