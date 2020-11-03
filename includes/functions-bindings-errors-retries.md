---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284381"
---
Os erros gerados em um Azure Functions podem vir de qualquer uma das seguintes origens:

- Uso de [gatilhos e associações](..\articles\azure-functions\functions-triggers-bindings.md) de Azure Functions internos
- Chamadas para APIs de serviços subjacentes do Azure
- Chamadas para pontos de extremidade REST
- Chamadas para bibliotecas de cliente, pacotes ou APIs de terceiros

Seguir as boas práticas de tratamento de erro é importante para evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de tratamento de erros incluem as seguintes ações:

- [Habilitar o Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usar tratamento de erro estruturado](#use-structured-error-handling)
- [Design para Idempotência](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de repetição](#retry-policies) (quando apropriado)

### <a name="use-structured-error-handling"></a>Usar tratamento de erro estruturado

A captura e o registro em log de erros são essenciais para o monitoramento da integridade do seu aplicativo. O nível mais alto de qualquer código de função deve incluir um bloco try/catch. No bloco catch, você pode capturar e registrar erros.

## <a name="retry-policies"></a>Políticas de repetição

Uma política de repetição pode ser definida em qualquer função para qualquer tipo de gatilho em seu aplicativo de funções.  A política de repetição executa novamente uma função até a execução bem-sucedida ou até que ocorra o número máximo de repetições.  As políticas de repetição podem ser definidas para todas as funções em um aplicativo ou para funções individuais.  Por padrão, um aplicativo de funções não repetirá mensagens (além dos [gatilhos específicos que têm uma política de repetição na origem do gatilho](#trigger-specific-retry-support)).  Uma política de repetição é avaliada sempre que uma execução resulta em uma exceção não capturada.  Como prática recomendada, você deve capturar todas as exceções em seu código e lançar novamente os erros que devem resultar em uma nova tentativa.  Os hubs de eventos e os pontos de verificação de Azure Cosmos DB não serão gravados até que a política de repetição para a execução seja concluída, o que significa que o andamento dessa partição será pausado até que o lote atual seja concluído.

### <a name="retry-policy-options"></a>Opções de política de repetição

As opções a seguir estão disponíveis para definir uma política de repetição.

**Contagem máxima de repetições** é o número máximo de vezes que uma execução é repetida antes da falha eventual. Um valor de `-1` significa tentar indefinidamente. A contagem de repetições atual é armazenada na memória da instância. É possível que uma instância tenha uma falha entre as tentativas de repetição.  Quando uma instância falha durante uma política de repetição, a contagem de repetição é perdida.  Quando há falhas de instância, os gatilhos como hubs de eventos, Azure Cosmos DB e armazenamento de filas são capazes de retomar o processamento e repetir o lote em uma nova instância, com a contagem de repetição redefinida para zero.  Outros gatilhos, como HTTP e Timer, não reiniciam em uma nova instância.  Isso significa que a contagem máxima de tentativas é um melhor esforço e, em alguns casos raros, uma execução pode ser repetida mais do que o máximo, ou para que gatilhos como HTTP e temporizador sejam repetidos menos do que o máximo.

A **estratégia de repetição** controla como as tentativas se comportam.  A seguir estão duas opções de repetição com suporte:

| Opção | Descrição|
|---|---|
|**`fixedDelay`**| Um período de tempo especificado pode decorrer entre cada repetição,|
| **`exponentialBackoff`**| A primeira tentativa aguarda o atraso mínimo. Em novas tentativas subsequentes, o tempo é adicionado exponencialmente à duração inicial para cada repetição, até que o atraso máximo seja atingido.  Retirada exponencial adiciona alguma pequena aleatoriedade a atrasos para escalonar repetições em cenários de alta taxa de transferência.|

#### <a name="app-level-configuration"></a>Configuração de nível de aplicativo

Uma política de repetição pode ser definida para todas as funções em um aplicativo [usando o `host.json` arquivo](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configuração de nível de função

Uma política de repetição pode ser definida para uma função específica.  A configuração específica da função tem prioridade sobre a configuração no nível do aplicativo.

#### <a name="fixed-delay-retry"></a>Repetição de atraso fixa

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição na *function.jsno* arquivo:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição na *function.jsno* arquivo:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Repetição de retirada exponencial

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|Propriedade function.json  |Propriedade de atributo | Descrição |
|---------|---------|---------| 
|usada|N/D|Obrigatórios. Usar qual estratégia de repetição. Os valores válidos são `fixedDelay` ou `exponentialBackoff`.|
|maxRetryCount|N/D|Obrigatórios. O número máximo de repetições permitidas por execução de função. `-1` significa repetir indefinidamente.|
|delayInterval|N/D|O atraso que será usado entre as repetições ao usar a `fixedDelay` estratégia.|
|minimumInterval|N/D|O atraso mínimo de repetição ao usar a `exponentialBackoff` estratégia.|
|maximumInterval|N/D|O atraso máximo de repetição ao usar a `exponentialBackoff` estratégia.| 

## <a name="trigger-specific-retry-support"></a>Gatilho-suporte de repetição específico

Alguns gatilhos fornecem novas tentativas na origem do gatilho.  Essas repetições de gatilho podem ser usadas além de ou como uma substituição para a política de repetição do host do aplicativo de funções.  Se um número fixo de novas tentativas for desejado, você deverá usar a política de repetição específica do gatilho na política de repetição de host genérico.  Os seguintes gatilhos dão suporte a repetições na origem do gatilho:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de Filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Barramento de Serviço do Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, esses gatilhos retentam solicitações até cinco vezes. Após a quinta repetição, o armazenamento de fila do Azure e o gatilho do barramento de serviço do Azure gravam uma mensagem em uma [fila suspeita](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
