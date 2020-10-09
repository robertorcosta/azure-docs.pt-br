---
title: Executar tarefas em paralelo para otimizar os recursos de computação
description: Aumente a eficiência e reduza os custos usando menos nós de computação e executando tarefas simultâneas em cada nó em um pool do Lote do Azure
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850992"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Execute tarefas simultaneamente para maximizar o uso dos nós de computação do Lote

Executando simultaneamente mais de uma tarefa em cada nó de computação no pool de Lotes do Azure, você pode maximizar o uso dos recursos em um número menor de nós no pool. Para algumas cargas de trabalho, isso pode resultar em tempos mais curtos de trabalho e reduzir os custos.

Embora alguns cenários se beneficiem de dedicar todos os recursos de um nó para uma única tarefa, algumas situações se beneficiam de permitir que várias tarefas compartilhem esses recursos:

* **Minimização da transferência de dados** quando as tarefas puderem compartilhar dados. Nesse cenário, é possível reduzir drasticamente os encargos de transferência de dados copiando dados compartilhados em uma quantidade menor de nós e executando tarefas em paralelo em cada nó. Isso se aplicará especialmente se os dados que devem ser copiados em cada nó precisarem ser transferidos entre regiões geográficas.
* **Maximização do uso de memória** quando as tarefas exigirem uma grande quantidade de memória, mas somente durante curtos períodos de tempo, e em momentos variáveis durante a execução. Você pode empregar menos nós de computação, porém maiores, com mais memória para lidar de forma eficiente com esses picos. Esses nós teriam várias tarefas executadas em paralelo em cada nó, mas cada tarefa aproveitaria a grande quantidade de memória dos nós em momentos diferentes.
* **Redução dos limites do número de nós** quando a comunicação entre nós for necessária em um pool. Atualmente, os pools configurados para comunicação entre nós estão limitados a 50 nós de computação. Se cada nó desse pool for capaz de executar tarefas em paralelo, será possível executar um número maior de tarefas em paralelo.
* **Replicação de um cluster de computação local**, como na primeira movimentação de um ambiente de computação para o Azure. Se sua solução local atual executar várias tarefas por nó de computação, você poderá aumentar o número máximo de tarefas de nó para espelhar mais de perto a configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Para ilustrar os benefícios de execução das tarefas paralelas, digamos que seu aplicativo de tarefa tenha requisitos de CPU e memória de forma que os nós [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) sejam suficientes. Mas, para concluir o trabalho no tempo necessário, 1.000 nós são necessários.

Em vez de usar os nós Standard\_D1, que têm um núcleo de CPU, você poderia utilizar os nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) com 16 núcleos cada e permitir a execução de tarefas paralelas. Portanto, *16 vezes menos nós* poderiam ser usados; em vez de 1.000 nós, somente 63 seriam necessários. Além disso, se arquivos de aplicativo grandes ou dados de referência forem necessários para cada nó, a eficiência e a duração do trabalho serão novamente aperfeiçoadas, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Habilitar a execução de tarefas paralelas
Você configura os nós de computação para a execução paralela das tarefas no nível do pool. Com a biblioteca .NET do lote, defina a propriedade [CloudPool. TaskSlotsPerNode][maxtasks_net] ao criar um pool. Se você estiver usando a API REST do lote, defina o elemento [taskSlotsPerNode][rest_addpool] no corpo da solicitação durante a criação do pool.

O lote do Azure permite que você defina os slots de tarefa por nó até (4x) o número de núcleos de nó. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), será possível definir `taskSlotsPerNode` como 16. No entanto, independentemente de quantos núcleos o nó tem, você não pode ter mais de 256 slots de tarefa por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, confira [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md). Para saber mais sobre limites de serviço, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md).

> [!TIP]
> Leve em consideração o valor de `taskSlotsPerNode` ao criar uma [fórmula de dimensionamento automático][enable_autoscaling] para o seu pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md) para saber mais.
>
>

> [!NOTE]
> Você pode definir o `taskSlotsPerNode` elemento e a propriedade [TaskSlotsPerNode][maxtasks_net] somente no momento da criação do pool. Eles não poderão ser modificados após a criação do pool.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação em um pool puderem executar as tarefas simultaneamente, será importante especificar como você deseja distribuir suas tarefas nos nós no pool.

Ao usar a propriedade [CloudPool.TaskSchedulingPolicy][task_schedule], você pode especificar que as tarefas devam ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó no pool ("remessa").

Como um exemplo de como esse recurso é valioso, considere o pool de nós [padrão \_ D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um valor de 16 [CloudPool. TaskSlotsPerNode][maxtasks_net] . Se [CloudPool.TaskSchedulingPolicy][task_schedule] for configurada com um [ComputeNodeFillType][fill_type] igual a *Pack*, ela maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento automático](batch-automatic-scaling.md) removesse os nós não utilizados do pool (nós sem nenhuma tarefa atribuída). Isso minimiza o uso de recursos e economizando dinheiro.

## <a name="variable-slots-per-task"></a>Slots variáveis por tarefa
A tarefa pode ser definida com a propriedade [CloudTask. RequiredSlots][taskslots_net] para especificar quantos slots ele exige para ser executado em um nó de computação, com o valor padrão como 1. Você pode definir os slots de tarefa variáveis se suas tarefas tiverem diferentes pesos em relação ao uso de recursos no nó de computação, para que cada nó de computação possa ter um número razoável de tarefas simultâneas em execução sem sobrecarregar os recursos do sistema, como CPU ou memória.

Por exemplo, para um pool com propriedade `taskSlotsPerNode = 8` , você pode enviar as tarefas intensivas de CPU exigidas por vários núcleos com o `requiredSlots = 8` , enquanto outras tarefas com o `requiredSlots = 1` . Quando essa carga de trabalho mista estiver agendada para o pool, as tarefas com uso intensivo de CPU serão executadas exclusivamente no nó de computação, enquanto outras tarefas poderão ser executadas simultaneamente (até oito tarefas) em outros nós. Isso o ajudará a balancear sua carga de trabalho entre nós de computação e melhorar a eficiência do uso de recursos.

> [!TIP]
> Ao usar slots de tarefa de variável, é possível que tarefas grandes com slots mais necessários possam falhar temporariamente para serem agendadas por causa de Slots insuficientes disponíveis em qualquer nó de computação, mesmo quando ainda houver Slots ociosos em alguns nós. Você pode aumentar a prioridade do trabalho para essas tarefas para aumentar sua chance de competir por slots disponíveis em nós.
>
> O serviço de lote também emite [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando ele não agenda a execução de uma tarefa, enquanto continua tentando novamente o agendamento até que os slots necessários fiquem disponíveis. Você pode escutar esse evento para detectar um problema de agendamento de tarefas em potencial e fazer sua mitigação de acordo.
>

> [!NOTE]
> Não especifique que a tarefa `requiredSlots` seja maior que o do pool `taskSlotsPerNode` . Isso fará com que a tarefa nunca possa ser executada. Atualmente, o serviço de lote não faz essa validação quando você envia tarefas, pois o trabalho pode não ter um pool associado no tempo de envio ou ser alterado para um pool diferente desabilitando/reabilitando.
>

## <a name="batch-net-example"></a>Exemplo de .NET do Lote
Os seguintes trechos de código de API [.net do lote][api_net] mostram como criar um pool com vários slots de tarefa por nó e enviar tarefa com os slots necessários.

### <a name="create-pool"></a>Criar pool
Este trecho de código mostra uma solicitação para criar um pool que contém quatro nós com quatro slots de tarefa permitidos por nó. Isso especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no pool. Para saber mais sobre como adicionar pools usando a API .NET do Lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>Criar tarefa com slots necessários
Este trecho de código cria uma tarefa com não padrão `requiredSlots` . Esta tarefa só será executada quando houver slots livres suficientes disponíveis no nó de computação.
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Listar nós de computação com contagens para executar tarefas e slots
Esse trecho de código lista todos os nós de computação no pool e imprime as contagens para executar tarefas e slots de tarefa por nó.
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Listar contagens de tarefas para o trabalho
Este trecho de código obtém contagens de tarefas para o trabalho, o que inclui tarefas e tarefas contagem de slots por estado de tarefa.
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Exemplo REST do Lote
Esse snippet da API [REST do Lote][api_rest] mostra uma solicitação para criar um pool com dois nós grandes, com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

Este trecho de código mostra uma solicitação para adicionar uma tarefa com não padrão `requiredSlots` . Esta tarefa só será executada quando houver slots livres suficientes disponíveis no nó de computação.
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra o uso da propriedade [CloudPool. TaskSlotsPerNode][maxtasks_net] .

Este aplicativo de console C# usa a biblioteca [.NET do Lote][api_net] para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular uma carga variável. A saída do aplicativo especifica os nós executados em cada tarefa. O aplicativo também fornece um resumo dos parâmetros do trabalho e a duração. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo é exibida abaixo.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que com um único nó no pool, e a configuração padrão de uma tarefa por nó, a duração do trabalho será superior a 30 minutos.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma redução significativa na duração do trabalho. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, o que permite que a execução paralela de tarefas conclua o trabalho em aproximadamente um quarto do tempo.

> [!NOTE]
> As durações de trabalho nos resumos acima não incluem o tempo de criação do pool. Cada um dos trabalhos acima foi enviado para pools criados anteriormente e cujos nós de computação estavam no estado *Ocioso* na hora do envio.
>
>

## <a name="next-steps"></a>Próximas etapas
### <a name="batch-explorer-heat-map"></a>Mapa de Calor do Explorador do Lote
[O Batch Explorer][batch_labs] é uma ferramenta cliente autônoma, rica e exclusiva para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. O Batch Explorer contém um recurso de *Mapa de Calor* que fornece visualização da execução da tarefa. Ao executar o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample], você pode usar o recurso Mapa de Calor para visualizar facilmente a execução das tarefas paralelas em cada nó.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
