---
title: Verificar erros de trabalho e tarefa-Azure batch | Microsoft Docs
description: Erros para verificar e solucionar problemas de trabalhos e tarefas
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087014"
---
# <a name="job-and-task-error-checking"></a>Verificação de erro de trabalho e tarefa

Há vários erros que podem ocorrer ao adicionar trabalhos e tarefas. A detecção de falhas para essas operações é simples porque todas as falhas são retornadas imediatamente pela API, CLI ou interface do usuário.  No entanto, há falhas que podem acontecer posteriormente quando trabalhos e tarefas são agendados e executados.

Este artigo aborda os erros que podem ocorrer depois que os trabalhos e as tarefas são enviados. Ele lista e explica os erros que precisam ser verificados e manipulados.

## <a name="jobs"></a>Trabalhos

Um trabalho é um agrupamento de uma ou mais tarefas, na verdade, as tarefas que especificam as linhas de comando a serem executadas.

Ao adicionar um trabalho, os seguintes parâmetros podem ser especificados, o que pode influenciar como o trabalho pode falhar:

- [Restrições de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A propriedade `maxWallClockTime` pode, opcionalmente, ser especificada para definir a quantidade máxima de tempo que um trabalho pode estar ativo ou em execução. Se for excedido, o trabalho será encerrado com a propriedade `terminateReason` definida no [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de preparação do trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificado, uma tarefa de preparação de trabalho é executada na primeira vez em que uma tarefa é executada para um trabalho em um nó. A tarefa de preparação do trabalho pode falhar, o que fará com que a tarefa não seja executada e o trabalho não seja concluído.
- [Tarefa de liberação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de liberação de trabalho só poderá ser especificada se uma tarefa de preparação de trabalho estiver configurada. Quando um trabalho está sendo encerrado, a tarefa de liberação de trabalho é executada em cada um dos nós de pool em que uma tarefa de preparação de trabalho foi executada. Uma tarefa de liberação de trabalho pode falhar, mas o trabalho ainda será movido para um estado de `completed`.

### <a name="job-properties"></a>Propriedades de trabalho

As seguintes propriedades de trabalho devem ser verificadas em busca de erros:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - A propriedade `terminateReason` pode ter valores para indicar que o `maxWallClockTime`, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Ele também pode ser definido para indicar uma falha na tarefa se a propriedade de `onTaskFailure` de trabalho tiver sido definida adequadamente.
  - A propriedade [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) será definida se houver um erro de agendamento.
 
### <a name="job-preparation-tasks"></a>Tarefas de preparação do trabalho

Se uma tarefa de preparação de trabalho for especificada para um trabalho, uma instância dessa tarefa será executada na primeira vez em que uma tarefa para o trabalho for executada em um nó. A tarefa de preparação de trabalho configurada no trabalho pode ser considerada como um modelo de tarefa, com várias instâncias de tarefa de preparação de trabalho sendo executadas, até o número de nós em um pool.

As instâncias de tarefa de preparação do trabalho devem ser verificadas para determinar se Houveram erros:
- Quando uma tarefa de preparação de trabalho é executada, a tarefa que disparou a tarefa de preparação do trabalho será movida para um [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) de `preparing`; se a tarefa de preparação do trabalho falhar, a tarefa de disparo será revertida para o estado de `active` e não será executada.  
- Todas as instâncias da tarefa de preparação de trabalho que foram executadas podem ser obtidas do trabalho usando a [preparação da lista e a API de status da tarefa de liberação](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Assim como ocorre com qualquer tarefa, há [informações de execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode`e `result`.
- Se as tarefas de preparação do trabalho falharem, as tarefas de acionamento do trabalho não serão executadas, o trabalho não será concluído e ficará preso. O pool poderá ficar inutilizado se não houver nenhum outro trabalho com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de liberação do trabalho

Se uma tarefa de liberação de trabalho for especificada para um trabalho, quando um trabalho estiver sendo encerrado, uma instância da tarefa de liberação de trabalho será executada em cada um dos nós de pool em que uma tarefa de preparação de trabalho foi executada.  As instâncias de tarefa de liberação de trabalho devem ser verificadas para determinar se Houveram erros:
- Todas as instâncias da tarefa de liberação de trabalho que está sendo executada podem ser obtidas do trabalho usando a preparação da lista de APIs [e o status da tarefa de liberação](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Assim como ocorre com qualquer tarefa, há [informações de execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode`e `result`.
- Se uma ou mais tarefas de liberação de trabalho falharem, o trabalho ainda será encerrado e passará para um estado de `completed`.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por vários motivos:

- A linha de comando da tarefa falha, retornando com um código de saída diferente de zero.
- Há `resourceFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram baixados.
- Há `outputFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram carregados.
- O tempo decorrido da tarefa, especificado pela propriedade `maxWallClockTime` nas [restrições](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)de tarefa, foi excedido.

Em todos os casos, as seguintes propriedades devem ser verificadas quanto a erros e informações sobre os erros:
- A propriedade [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) de tarefas contém várias propriedades que fornecem informações sobre um erro. [resultado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa sempre será movida para o [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)de `completed`, independentemente de ter êxito ou falhado.

O impacto das falhas de tarefas no trabalho e de qualquer dependência de tarefa deve ser considerado.  A propriedade [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa a fim de configurar uma ação para dependências e para o trabalho.
- Para dependências, [dependencyaction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controla se as tarefas dependentes da tarefa com falha são bloqueadas ou são executadas.
- Para o trabalho, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controla se a tarefa com falha leva ao trabalho que está sendo desabilitado, encerrado ou deixado inalterado.

### <a name="task-command-line-failures"></a>Falhas de linha de comando de tarefa

Quando a linha de comando da tarefa é executada, a saída é gravada em `stderr.txt` e `stdout.txt`. Além disso, o aplicativo pode gravar em arquivos de log específicos do aplicativo.

Se o nó do pool no qual uma tarefa foi executada ainda existir, os arquivos de log poderão ser obtidos e exibidos. Por exemplo, o portal do Azure lista e pode exibir arquivos de log para uma tarefa ou um nó de pool. Várias APIs também permitem que os arquivos de tarefa sejam listados e obtidos, como [obter da tarefa](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Devido aos pools e nós de pool frequentemente serem efêmeros, com os nós sendo adicionados e excluídos continuamente, é recomendável que os arquivos de log sejam persistidos. [Os arquivos de saída de tarefas](https://docs.microsoft.com/azure/batch/batch-task-output-files) são uma maneira conveniente de salvar arquivos de log no armazenamento do Azure.

### <a name="output-file-failures"></a>Falhas de arquivo de saída
Em cada upload de arquivo, o lote grava dois arquivos de log para o nó de computação, `fileuploadout.txt` e `fileuploaderr.txt`. Você pode analisar esses arquivos de log para saber mais sobre uma falha específica. Nos casos em que o carregamento do arquivo nunca foi tentado, por exemplo, porque a própria tarefa não pôde ser executada, esses arquivos de log não existirão.  

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Verifique se seu aplicativo implementa a verificação de erros abrangente; pode ser essencial detectar e diagnosticar problemas imediatamente.
