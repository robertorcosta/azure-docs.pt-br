---
title: Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da Grade de Eventos do Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249753"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)

Este artigo mostra como configurar as Durable Functions para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e com falha) em um [Tópico de Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) personalizado.

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações azuis/verdes**: Talvez você queira saber se alguma tarefa está em execução antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Monitoramento avançado e suporte a diagnósticos**: você pode manter o controle das informações de status de orquestração em um repositório externo otimizado para consultas, como o banco de dados SQL do Azure ou Azure Cosmos DB.

* **Atividade em segundo plano de execução longa**: se você usar Funções Duráveis para uma atividade em segundo plano de execução longa, esse recurso o ajudará a saber o status atual.

## <a name="prerequisites"></a>Prerequisites

* Instale [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto Durable functions.
* Instale o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) (somente Windows) ou use uma conta de armazenamento do Azure existente.
* Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou usar o [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de Grade de Eventos personalizado

Crie um tópico de Grade de Eventos para enviar eventos a partir de Funções Duráveis. As instruções a seguir mostram como criar um tópico usando a CLI do Azure. Você também pode fazer isso [usando o PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [usando o portal do Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, a grade de eventos do Azure não dá suporte a todas as regiões. Para obter informações sobre quais regiões têm suporte, consulte [visão geral da grade de eventos do Azure](../../event-grid/overview.md).

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

## <a name="configure-event-grid-publishing"></a>Configurar publicação da grade de eventos

No projeto de Funções Duráveis, encontre o arquivo `host.json`.

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

As propriedades de configuração da grade de eventos do Azure possíveis podem ser encontradas na [documentação do host. JSON](../functions-host-json.md#durabletask). Depois de configurar o arquivo de `host.json`, seu aplicativo de funções envia eventos de ciclo de vida para o tópico da grade de eventos. Isso funciona quando você executa seu aplicativo de funções localmente e no Azure.

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

Se você estiver usando o [emulador de armazenamento](../../storage/common/storage-use-emulator.md) (somente Windows), verifique se ele está funcionando. É recomendável executar o comando `AzureStorageEmulator.exe clear all` antes da execução.

Se você estiver usando uma conta de armazenamento do Azure existente, substitua `UseDevelopmentStorage=true` em `local.settings.json` com sua cadeia de conexão.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Usando o portal do Azure, crie outro aplicativo de funções para escutar eventos publicados pelo seu aplicativo Durable Functions. É melhor localizá-lo na mesma região que o tópico da grade de eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de Grade de Eventos

Crie uma função para receber os eventos de ciclo de vida. Selecione **Função Personalizada**.

![Selecione Criar uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Escolha gatilho de grade de eventos e selecione um idioma.

![Selecione o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Digite o nome da função e selecione `Create`.

![Crie o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Uma função com o código a seguir é criada:

# <a name="c-script"></a>[C#Prescritiva](#tab/csharp-script)

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

## <a name="run-durable-functions-app-to-send-the-events"></a>Executar Durable Functions aplicativo para enviar os eventos

No projeto de Durable Functions que você configurou anteriormente, inicie a depuração no computador local e inicie uma orquestração. O aplicativo publica Durable Functions eventos de ciclo de vida na grade de eventos. Verifique se a grade de eventos dispara a função de ouvinte que você criou verificando seus logs no portal do Azure.

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

* **`id`** : identificador exclusivo para o evento da grade de eventos.
* **`subject`** : caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`** : Durable Functions parâmetros específicos.
  * **`hubName`** : nome do [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`** : nome da função de orquestrador.
  * **`instanceId`** : Durable Functions InstanceId.
  * **`reason`** : dados adicionais associados ao evento de rastreamento. Para obter mais informações, consulte [Diagnóstico nas Funções Duráveis (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : status do tempo de execução de orquestração. Em Execução, Concluído, Falha, Cancelado.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : hora do evento (UTC).
* **`dataVersion`** : versão do esquema de eventos do ciclo de vida.
* **`metadataVersion`** : versão dos metadados.
* **`topic`** : recurso de tópico da grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia a [depuração local do gatilho de grade de eventos da função do Azure](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conhecer a instância de gerenciamento em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Conhecer controle de versão em Funções Duráveis](durable-functions-versioning.md)
