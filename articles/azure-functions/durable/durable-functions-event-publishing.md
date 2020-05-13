---
title: Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da Grade de Eventos do Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: c0106f3754e0cdcbf1f295fbe3f1b5def8dc3ca1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124209"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)

Este artigo mostra como configurar as Durable Functions para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e com falha) em um [Tópico de Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) personalizado.

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações azuis/verdes**: Talvez você queira saber se alguma tarefa está em execução antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Monitoramento avançado e suporte a diagnósticos**: você pode manter o controle das informações de status de orquestração em um repositório externo otimizado para consultas, como o banco de dados SQL do Azure ou Azure Cosmos DB.

* **Atividade em segundo plano de execução longa**: se você usar Funções Duráveis para uma atividade em segundo plano de execução longa, esse recurso o ajudará a saber o status atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto Durable functions.
* Instale o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) (somente Windows) ou use uma conta de armazenamento do Azure existente.
* Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou usar o [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de Grade de Eventos personalizado

Crie um tópico de Grade de Eventos para enviar eventos a partir de Funções Duráveis. As instruções a seguir mostram como criar um tópico usando a CLI do Azure. Você também pode criar o tópico [usando o PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [usando o portal do Azure](../../event-grid/custom-event-quickstart-portal.md).

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

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Adicione uma `notifications` seção à `durableTask` Propriedade do arquivo, substituindo `<topic_name>` pelo nome escolhido. Se as `durableTask` `extensions` Propriedades ou não existirem, crie-as como este exemplo:

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

As propriedades de configuração da grade de eventos do Azure possíveis podem ser encontradas na [documentação do host. JSON](../functions-host-json.md#durabletask). Depois de configurar o `host.json` arquivo, seu aplicativo de funções envia eventos de ciclo de vida para o tópico da grade de eventos. Essa ação é iniciada quando você executa seu aplicativo de funções localmente e no Azure.

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

Se você estiver usando uma conta de armazenamento do Azure existente, substitua `UseDevelopmentStorage=true` `local.settings.json` por pela sua cadeia de conexão.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Usando o portal do Azure, crie outro aplicativo de funções para escutar eventos publicados pelo seu aplicativo Durable Functions. É melhor localizá-lo na mesma região que o tópico da grade de eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de Grade de Eventos

1. Em seu aplicativo de funções, selecione **funções**e, em seguida, selecione **+ Adicionar** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Adicione uma função no portal do Azure." border="true":::

1. Pesquise a **grade de eventos**e selecione o modelo de **gatilho de grade de eventos do Azure** . 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selecione o modelo de gatilho de grade de eventos na portal do Azure." border="true":::

1. Nomeie o novo gatilho e, em seguida, selecione **criar função**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nomeie o gatilho de grade de eventos na portal do Azure." border="true":::


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

### <a name="add-an-event-grid-subscription"></a>Adicionar uma assinatura da grade de eventos

Agora você pode adicionar uma assinatura de grade de eventos para o tópico da grade de eventos que você criou. Para obter mais informações, consulte [conceitos na grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/concepts).

1. Em sua nova função, selecione **integração** e, em seguida, selecione **gatilho de grade de eventos (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selecione o link do Gatilho de Grade de Eventos." border="true":::

1. Selecione **criar descrição da grade de eventos**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Crie a assinatura da grade de eventos." border="true":::

1. Nomeie sua assinatura de evento e selecione o tipo de tópico **Tópicos da grade de eventos** . 

1. Selecione a assinatura. Em seguida, selecione o grupo de recursos e o recurso que você criou para o tópico da grade de eventos. 

1. Selecione **Criar**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Crie uma assinatura de grade de eventos." border="true":::

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

* **`id`**: Identificador exclusivo para o evento da grade de eventos.
* **`subject`**: Caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`**: Durable Functions parâmetros específicos.
  * **`hubName`**: Nome do [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`**: Nome da função de orquestrador.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Dados adicionais associados ao evento de rastreamento. Para obter mais informações, consulte [Diagnóstico nas Funções Duráveis (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Status do tempo de execução de orquestração. Em Execução, Concluído, Falha, Cancelado.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Hora do evento (UTC).
* **`dataVersion`**: Versão do esquema de eventos do ciclo de vida.
* **`metadataVersion`**: Versão dos metadados.
* **`topic`**: Recurso de tópico da grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia a [depuração local do gatilho de grade de eventos da função do Azure](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conhecer a instância de gerenciamento em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Conhecer controle de versão em Funções Duráveis](durable-functions-versioning.md)
