---
title: Verificar os erros de trabalho e tarefas - Azure Batch | Microsoft Docs
description: Erros para verificar e solucionar problemas de trabalhos e tarefas
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087014"
---
# <a name="job-and-task-error-checking"></a>Verificação de erros de trabalho e tarefa

Existem vários erros que podem ocorrer ao adicionar trabalhos e tarefas. Detectar falhas para essas operações é simples porque quaisquer falhas são retornadas imediatamente pela API, CLI ou UI.  No entanto, há falhas que podem acontecer mais tarde quando trabalhos e tarefas são agendados e executados.

Este artigo abrange os erros que podem ocorrer após a apresentação de trabalhos e tarefas. Ele lista e explica os erros que precisam ser verificados e tratados.

## <a name="jobs"></a>Trabalhos

Um trabalho é um agrupamento de uma ou mais tarefas, as tarefas realmente especificando as linhas de comando a serem executadas.

Ao adicionar um trabalho, os seguintes parâmetros podem ser especificados que podem influenciar como o trabalho pode falhar:

- [Restrições de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A `maxWallClockTime` propriedade pode ser especificada opcionalmente para definir o tempo máximo que um trabalho pode estar ativo ou em execução. Se excedido, o trabalho será `terminateReason` encerrado com o imóvel definido na [execuçãoInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de Preparação de Trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificado, uma tarefa de preparação de trabalho é executada na primeira vez que uma tarefa é executada para um trabalho em um nó. A tarefa de preparação do trabalho pode falhar, o que levará à não execução da tarefa e à não conclusão do trabalho.
- [Tarefa de liberação de empregos](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de liberação de trabalho só pode ser especificada se uma tarefa de preparação de trabalho for configurada. Quando um trabalho está sendo encerrado, a tarefa de liberação do trabalho é executada em cada um dos nós de pool onde uma tarefa de preparação de trabalho foi executada. Uma tarefa de liberação de emprego pode `completed` falhar, mas o trabalho ainda vai passar para um estado.

### <a name="job-properties"></a>Propriedades de trabalho

As seguintes propriedades de trabalho devem ser verificadas quanto a erros:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - O `terminateReason` imóvel pode ter valores que indiquem que o `maxWallClockTime`, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Ele também pode ser definido para indicar `onTaskFailure` que uma tarefa falhou se a propriedade de trabalho foi definida adequadamente.
  - A propriedade [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) é definida se houve um erro de agendamento.
 
### <a name="job-preparation-tasks"></a>Tarefas de preparação de trabalho

Se uma tarefa de preparação de trabalho for especificada para um trabalho, então uma instância dessa tarefa será executada na primeira vez que uma tarefa para o trabalho for executada em um nó. A tarefa de preparação do trabalho configurada no trabalho pode ser pensada como um modelo de tarefa, com várias instâncias de tarefade preparação de trabalho sendo executadas, até o número de nomes em um pool.

As instâncias de tarefa de preparação do trabalho devem ser verificadas para determinar se houve erros:
- Quando uma tarefa de preparação de trabalho é executada, então a [state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) tarefa `preparing`que desencadeou a tarefa de preparação do trabalho passará para um estado de ; se a tarefa de preparação do trabalho falhar, `active` a tarefa de acionamento será revertida para o estado e não será executada.  
- Todas as instâncias da tarefa de preparação do trabalho que foram executadas podem ser obtidas a partir do trabalho usando a API [de status de preparação e liberação de](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) preparação de lista. Como em qualquer tarefa, há informações de `failureInfo` `exitCode` [execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como , e `result`.
- Se as tarefas de preparação do trabalho falharem, as tarefas de acionamento do trabalho não serão executadas, o trabalho não será concluído e ficará preso. O pool pode ficar sem uso se não houver outros trabalhos com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de liberação de empregos

Se uma tarefa de liberação de trabalho for especificada para um trabalho, então quando um trabalho está sendo encerrado, uma instância da tarefa de liberação de emprego é executada em cada um dos nós do pool onde uma tarefa de preparação do trabalho foi executada.  As instâncias de tarefa de liberação de trabalho devem ser verificadas para determinar se houve erros:
- Todas as instâncias da tarefa de liberação de trabalho que está sendo executada podem ser obtidas a partir do trabalho usando o [Status de Preparação e Correção da Lista da](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API . Como em qualquer tarefa, há informações de `failureInfo` `exitCode` [execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como , e `result`.
- Se uma ou mais tarefas de liberação de emprego falharem, então o trabalho ainda será encerrado e passará para um `completed` estado.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por várias razões:

- A linha de comando da tarefa falha, retornando com um código de saída não-zero.
- Há `resourceFiles` especificações para uma tarefa, mas houve uma falha que significou que um ou mais arquivos não baixaram.
- Há `outputFiles` especificações para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram carregados.
- O tempo decorrido para a tarefa, `maxWallClockTime` especificado pela propriedade nas [restrições](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)da tarefa, foi excedido.

Em todos os casos, as seguintes propriedades devem ser verificadas quanto a erros e informações sobre os erros:
- A [execução](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) de tarefasPropriedade info contém várias propriedades que fornecem informações sobre um erro. [resultado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa sempre se `completed` moverá para o [Estado,](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)independentemente de ter conseguido ou falhado.

O impacto das falhas de tarefas no trabalho e quaisquer dependências de tarefas deve ser considerado.  A propriedade [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa para configurar uma ação para dependências e para o trabalho.
- Para dependências, [dependencyAction controla](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) se as tarefas dependentes da tarefa falha estão bloqueadas ou são executadas.
- Para o trabalho, o [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controla se a tarefa fracassada leva ao trabalho ser desativado, encerrado ou deixado inalterado.

### <a name="task-command-line-failures"></a>Falhas na linha de comando de tarefas

Quando a linha de comando de `stderr.txt` tarefa `stdout.txt`é executada, a saída é escrita para e . Além disso, o aplicativo pode escrever em arquivos de log específicos do aplicativo.

Se o nó de pool no qual uma tarefa foi executada ainda existir, os arquivos de log podem ser obtidos e visualizados. Por exemplo, o portal Azure lista e pode exibir arquivos de log para uma tarefa ou um nó de pool. Várias APIs também permitem que arquivos de tarefas sejam listados e obtidos, como [Get From Task](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Devido a piscinas e nós de pool freqüentemente sendo efêmeros, com nós sendo continuamente adicionados e excluídos, então recomenda-se que os arquivos de log sejam persistidos. [Os arquivos de saída de](https://docs.microsoft.com/azure/batch/batch-task-output-files) tarefas são uma maneira conveniente de salvar arquivos de log no Azure Storage.

### <a name="output-file-failures"></a>Falhas de arquivo de saída
Em cada upload de arquivo, o lote grava dois arquivos de log para o nó de computação, `fileuploadout.txt` e `fileuploaderr.txt`. Você pode analisar esses arquivos de log para saber mais sobre uma falha específica. Nos casos em que o upload do arquivo nunca foi tentado, por exemplo, porque a tarefa em si não pôde ser executada, então esses arquivos de log não existirão.  

## <a name="next-steps"></a>Próximas etapas

Verifique se seu aplicativo implementa verificação abrangente de erros; pode ser fundamental para detectar e diagnosticar prontamente problemas.
