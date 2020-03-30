---
title: Cenários de fan-out/fan-in nas Funções Duráveis – Azure
description: Saiba como implementar um cenário de fan-out/fan-in na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562183"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in nas Funções Duráveis – Exemplo de backup em nuvem

*Fan-out/fan-in* é o padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação dos resultados. Este artigo descreve um exemplo que usa as [Funções Duráveis](durable-functions-overview.md) para implementar um cenário de fan-out/fan-in. O exemplo é uma função durável que faz o backup de todo ou de parte do conteúdo do site de um aplicativo no Armazenamento do Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

Neste exemplo, as funções carregam todos os arquivos em um diretório especificado, recursivamente, no armazenamento de blobs. Elas também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuida de tudo. O principal problema que você teria seria a **escalabilidade**. Uma única execução de função só pode ser executada em uma única máquina virtual, de modo que o throughput será limitado pelo throughput de que uma única VM. Outro problema é a **confiabilidade**. Se houver uma falha no meio do caminho, ou se todo o processo levar mais de 5 minutos, o backup pode falhar em um estado parcialmente concluído. Em seguida, ele precisaria ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: um enumeraria os arquivos e adicionaria os nomes dos arquivo a uma fila e a outra leria da fila e carregaria os arquivos no armazenamento de blobs. Esta abordagem é melhor em termos de throughput e confiabilidade, mas exige que você prover e gerenciar uma fila. E, mais importante, uma complexidade significativa é introduzida em termos de **gerenciamento de estado** e **coordenação** se você quiser fazer qualquer outra coisa, como relatar o número total de bytes carregados.

Uma abordagem com as Funções Duráveis fornece todos os benefícios mencionados, com pouquíssima sobrecarga.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E2_BackupSiteContent`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E2_GetFileList` para obter uma `E2_CopyFileToBlob` lista de arquivos para fazer backup e, em seguida, chama para fazer backup de cada arquivo.
* `E2_GetFileList`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que retorna uma lista de arquivos em um diretório.
* `E2_CopyFileToBlob`: Uma função de atividade que faça backup de um único arquivo para o Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>função orquestradora E2_BackupSiteContent

Essa função de orquestrador faz, essencialmente, o seguinte:

1. Usa um valor de `rootDirectory` como um parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de arquivos em `rootDirectory`.
3. Faz várias chamadas de função paralelas para carregar cada arquivo no Armazenamento de Blobs do Azure.
4. Aguarda que todos os uploads sejam concluídos.
5. Retorna o total de bytes que foram carregados no Armazenamento de Blobs do Azure.

# <a name="c"></a>[C #](#tab/csharp)

Este é o código que implementa a função de orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Observe a linha `await Task.WhenAll(tasks);`. Todas as chamadas `E2_CopyFileToBlob` individuais para a função *não* eram aguardadas, o que lhes permite executar em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você estiver familiarizado com a TPL (biblioteca de paralelismo de tarefas) no .NET, isso não é novidade para você. A diferença é que essas tarefas podem estar sendo executados em várias máquinas virtuais simultaneamente, e a extensão Funções Duráveis garante que a execução completa seja resiliente à reciclagem de processos.

Depois de aguardar `Task.WhenAll`, sabemos que todas as chamadas de função foram concluídas e retornaram valores para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A função usa a *função padrão.json* para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Observe a linha `yield context.df.Task.all(tasks);`. Todas as chamadas `E2_CopyFileToBlob` individuais para a função *não* foram atendidas, o que lhes permite executar em paralelo. Quando passamos essa matriz de tarefas para `context.df.Task.all`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você está [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) familiarizado com em JavaScript, então isso não é novo para você. A diferença é que essas tarefas podem estar sendo executados em várias máquinas virtuais simultaneamente, e a extensão Funções Duráveis garante que a execução completa seja resiliente à reciclagem de processos.

> [!NOTE]
> Embora as tarefas sejam conceitualmente semelhantes a promessas JavaScript, funções de orquestrador devem usar `context.df.Task.all` e `context.df.Task.any` em vez de `Promise.all` e `Promise.race` para gerenciar a paralelização de tarefa.

Depois de `context.df.Task.all`ceder, sabemos que todas as chamadas de função foram concluídas e devolveram os valores de volta para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

---

### <a name="helper-activity-functions"></a>Funções de atividade auxiliares

Funções de atividade auxiliares, assim como ocorre com os outros exemplos, são apenas funções regulares que usam a associação de gatilho `activityTrigger`.

#### <a name="e2_getfilelist-activity-function"></a>função de atividade E2_GetFileList

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

O arquivo *function.json* para `E2_GetFileList` se parece com o seguinte:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

E esta é a implementação:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A função `readdirp` usa o módulo (versão 2.x) para ler recursivamente a estrutura do diretório.

---

> [!NOTE]
> Você deve estar se perguntando por que você não poderia simplesmente colocar esse código diretamente na função de orquestrador. Você poderia, mas isso violaria uma das regras fundamentais das funções de orquestrador, de que elas nunca devem executar E/S, incluindo no caso de acesso ao sistema de arquivos local. Para obter mais informações, consulte [restrições de código de função do Orquestrador](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob função de atividade

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Você precisará instalar `Microsoft.Azure.WebJobs.Extensions.Storage` o pacote NuGet para executar o código de amostra.

A função usa alguns recursos avançados das ligações Funções Azure (ou seja, o uso do [ `Binder` parâmetro),](../functions-dotnet-class-library.md#binding-at-runtime)mas você não precisa se preocupar com esses detalhes para o propósito deste passo a passo.

# <a name="javascript"></a>[Javascript](#tab/javascript)

O arquivo *function.json* para `E2_CopyFileToBlob` também é simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A implementação JavaScript usa o [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) para carregar os arquivos no Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

A implementação carrega o arquivo no disco e transmite de forma assíncrona o conteúdo para um blob de mesmo nome no contêiner "backups". O valor retornado é o número de bytes copiados para o armazenamento, que é usado pela função de orquestrador para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de movimentação de operações de E/S para uma função `activityTrigger`. Não só o trabalho pode ser distribuído em muitas máquinas diferentes, mas também você tem os benefícios de controlar o progresso. Se o processo de host for encerrado por algum motivo, você saberá quais carregamentos já foram concluídos.

## <a name="run-the-sample"></a>Execute o exemplo

Você pode iniciar a orquestração enviando a solicitação HTTP POST a seguir.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A função `HttpStart` que você está invocando funciona somente com conteúdo formatado em JSON. Por esse motivo, o cabeçalho `Content-Type: application/json` é obrigatório e o caminho do diretório é codificado como uma cadeia de caracteres JSON. Além disso, o fragmento de HTTP assume que existe uma entrada no arquivo `host.json`que remove o prefixo padrão`api/` de todas as URLs de funções do acionador HTTP. Você pode encontrar a marcação para essa configuração no arquivo `host.json` nos exemplos.

Esta solicitação HTTP dispara o orquestrador `E2_BackupSiteContent` e passa a cadeia de caracteres `D:\home\LogFiles` como um parâmetro. A resposta fornece um link para obter o status da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos arquivos de log você tiver em seu aplicativo de funções, essa operação pode levar vários minutos para ser concluída. Você pode obter o status mais recente consultando a URL no cabeçalho `Location` da resposta HTTP 202 anterior.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Nesse caso, a função ainda está sendo executada. É possível ver a entrada que foi salva no estado do orquestrador e a hora da última atualização. Você pode continuar usando os valores de cabeçalho `Location` para sondar a conclusão. Quando o status for "Concluído", você verá um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Agora, você pode ver que a orquestração foi concluída e aproximadamente quanto tempo ela levou para ser concluída. Você também verá um valor para o campo `output`, o que indica que cerca de 450 KB de logs foram carregados.

## <a name="next-steps"></a>Próximas etapas

Este exemplo mostra como implementar o padrão de fan-out/fan-in. O próximo exemplo mostra como implementar o padrão de monitor usando [temporizadores variáveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de monitor](durable-functions-monitor.md)