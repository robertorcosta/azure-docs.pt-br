---
title: Encadeamento de funções nas Funções Duráveis – Azure
description: Saiba como executar um exemplo de Funções Duráveis que executa uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562047"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadeamento de funções nas Funções Duráveis – Exemplo se sequência Hello

Encadeamento de funções é o padrão de executar uma sequência de funções em uma ordem específica. Frequentemente, a saída de uma função precisa ser aplicada à entrada de outra função. Este artigo descreve a sequência de encadeamento que você cria quando você conclui o guia de início rápido Durable Functions ([C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md)). Para obter mais informações sobre as funções duráveis, consulte [visão geral das funções duráveis](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E1_HelloSequence`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que `E1_SayHello` chama várias vezes em uma sequência. Ela armazena as saídas das chamadas `E1_SayHello` e registra os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que precede uma cadeia de caracteres com "Olá".
* `HttpStart`: Uma função disparada por HTTP que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence função de orquestrador

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas as funções de orquestração em C# devem ter um parâmetro do tipo `DurableOrchestrationContext`, que existe no assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Esse objeto de contexto permite chamar outras funções de *atividade* e passar parâmetros de entrada `CallActivityAsync` usando seu método.

O código chama `E1_SayHello` três vezes seguidas com valores de parâmetros diferentes. O valor retornado de cada chamada é adicionado à lista `outputs`, que é retornada ao final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions de JavaScript estão disponíveis apenas para o Functions 2,0 Runtime.

#### <a name="functionjson"></a>function.json

Se você usa o Visual Studio Code ou o portal do Azure para desenvolvimento, este é o conteúdo do arquivo *function.json* para a função de orquestrador. A maioria dos arquivos *function.json* do orquestrador são quase exatamente iguais a esse.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é o tipo de associação de `orchestrationTrigger`. Todas as funções de orquestrador devem usar esse tipo de gatilho.

> [!WARNING]
> Para obedecer a regra de "não fazer E/S" das funções de orquestrador, não use nenhuma associação de entrada ou saída ao usar a associação de gatilho `orchestrationTrigger`.  Se outras associações de entrada ou de saída forem necessárias, elas deverão ser usadas no contexto das funções `activityTrigger`, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo [restrições de código de função do Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js

Esta é a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração do JavaScript devem incluir o [ `durable-functions` módulo](https://www.npmjs.com/package/durable-functions). É uma biblioteca que permite que você escreva Durable Functions em JavaScript. Há três diferenças significativas entre uma função de orquestração e outras funções de JavaScript:

1. A função é uma [função de gerador.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A função é encapsulada em uma chamada para o método `orchestrator` do módulo `durable-functions` (aqui `df`).
3. A função deve ser síncrona. Como o método "orchestrator" lida com a chamada "context.done", a função deve simplesmente "return".

O `context` objeto contém um `df` objeto de contexto de orquestração durável que permite chamar outras funções de *atividade* e passar parâmetros de `callActivity` entrada usando seu método. O código chama `E1_SayHello` três vezes em sequência com valores de parâmetros diferentes, usando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíncrona serem retornadas. O valor de retorno de cada chamada é adicionado à `outputs` matriz, que é retornada no final da função.

---

### <a name="e1_sayhello-activity-function"></a>Função de atividade de E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

As atividades usam `ActivityTrigger` o atributo. Use o fornecido `IDurableActivityContext` para executar ações relacionadas à atividade, como acessar o valor de entrada `GetInput<T>`usando.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente simples.

Em vez de associar a `IDurableActivityContext`um, você pode associar diretamente ao tipo que é passado para a função de atividade. Por exemplo:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.JSON

O arquivo *function.json* da função de atividade `E1_SayHello` é semelhante ao de `E1_HelloSequence`, exceto por usar um tipo de associação `activityTrigger` em vez de um tipo de associação `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração deve usar a associação `activityTrigger`.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente simples.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração de JavaScript, uma função de atividade não precisa de nenhuma configuração especial. A entrada passada a ela pela função do orquestrador está localizada no objeto `context.bindings` sob o nome da ligação `activityTrigger`; nesse caso, `context.bindings.name`. O nome da associação pode ser definido como parâmetro da função exportada e acessado diretamente, que é o que o código de exemplo faz.

---

### <a name="httpstart-client-function"></a>Função de cliente HttpStart

Você pode iniciar uma instância da função de orquestrador usando uma função de cliente. Você usará a `HttpStart` função http disparada para iniciar `E1_HelloSequence`instâncias do.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Para interagir com orquestradores, a função deve incluir uma `DurableClient` Associação de entrada. Você usa o cliente para iniciar uma orquestração. Ele também pode ajudá-lo a retornar uma resposta HTTP contendo URLs para verificar o status da nova orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function. JSON

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Para interagir com orquestradores, a função deve incluir uma `durableClient` Associação de entrada.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Use `df.getClient` para obter um `DurableOrchestrationClient` objeto. Você usa o cliente para iniciar uma orquestração. Ele também pode ajudá-lo a retornar uma resposta HTTP contendo URLs para verificar o status da nova orquestração.

---

## <a name="run-the-sample"></a>Execute o exemplo

Para executar a `E1_HelloSequence` orquestração, envie a seguinte solicitação HTTP post para a `HttpStart` função.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O snippet de código HTTP anterior pressupõe que exista uma entrada no arquivo `host.json` que remove o prefixo `api/` padrão de todas as URLs de funções de gatilho HTTP. Você pode encontrar a marcação para essa configuração no arquivo `host.json` nos exemplos.

Por exemplo, se você estiver executando a amostra em um aplicativo da função chamado "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (resumido para fins de brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração é enfileirada e começa a ser executada imediatamente. A URL no cabeçalho `Location` pode ser usada para verificar o status da execução.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o status da orquestração. Ela é executada e concluída rapidamente e você a verá com estado *Concluído*, com uma resposta semelhante esta (resumida para fins de brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como você pode ver, o `runtimeStatus` da instância é *Concluído* e o `output` contém o resultado serializado em JSON da execução da função de orquestrador.

> [!NOTE]
> Você pode implementar uma lógica inicial semelhante a outros tipos de gatilho, como `queueTrigger`, `eventHubTrigger` ou `timerTrigger`.

Examine os logs de execução da função. A `E1_HelloSequence` função foi iniciada e concluída várias vezes devido ao comportamento de reprodução descrito no tópico de [confiabilidade da orquestração](durable-functions-orchestrations.md#reliability) . Por outro lado, houve apenas três execuções de `E1_SayHello`, uma vez que as execuções dessas funções não são repetidas.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou uma orquestração de encadeamento de função simples. O próximo exemplo mostra como implementar o padrão de fan-out/fan-in.

> [!div class="nextstepaction"]
> [Executar o exemplo de fan-out/fan-in](durable-functions-cloud-backup.md)
