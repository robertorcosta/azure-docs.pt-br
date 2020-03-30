---
title: Desempenho e escala nas Funções Duráveis – Azure
description: Introdução à extensão de Durable Functions do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277902"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e escala nas Funções Duráveis (Azure Functions)

Para otimizar o desempenho e escalabilidade, é importante entender as características únicas da colocação em escala nas [Funções Duráveis](durable-functions-overview.md).

Para entender o comportamento de escala, você precisa entender alguns dos detalhes do provedor subjacente do fornecedor do Armazenamento do Microsoft Azure.

## <a name="history-table"></a>Tabela de histórico

A tabela de **Histórico** é uma tabela do Armazenamento do Microsoft Azure que contém eventos de histórico de todas as instâncias de orquestração com um hub de tarefas. O nome da tabela está no formato *TaskHubName*histórico. Conforme as instâncias são executadas, novas linhas são adicionadas a essa tabela. A chave de partição da tabela é derivada da ID de instância da orquestração. Uma ID de instância é aleatória na maioria dos casos, o que garante a distribuição ideal das partições internas do Armazenamento do Microsoft Azure.

Quando uma instância de orquestração precisa ser executada, as linhas correspondentes da tabela de histórico são carregadas na memória. Esses *histórico de eventos* são, em seguida, copiados para o código de função do orquestrador para recuperá-lo em seu estado de ponto de verificação anteriormente. O uso do histórico de execução para recriar o estado dessa maneira é influenciado pelo [padrão de Fornecimento de Evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela de instâncias

A tabela **Instances** é outra tabela de armazenamento Azure que contém os status de todas as instâncias de orquestração e entidade dentro de um centro de tarefas. Conforme as instâncias são criadas, novas linhas são adicionadas a essa tabela. A chave de partição desta tabela é o ID de instância de orquestração ou a chave de entidade e a tecla de linha é uma constante fixa. Há uma linha por orquestração ou instância de entidade.

Esta tabela é usada para satisfazer `GetStatusAsync` solicitações de `getStatus` consulta de instância satiscos das APIs (.NET) e (JavaScript), bem como da [api HTTP de consulta de status](durable-functions-http-api.md#get-instance-status). Isso é mantido finalmente consistente com o conteúdo da tabela **Histórico** mencionada anteriormente. O uso de uma tabela separada do Armazenamento do Microsoft Azure para atender com eficiência as operações de consulta de instância dessa maneira é influenciado pelo [padrão de Comando e Segregação de Reponsabilidade (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

As funções de Orchestrator e as funções de atividade são disparadas por filas internas no hub de tarefas do aplicativo. Usar filas dessa maneira fornece garantias de entrega de mensagem “pelo menos uma vez”. Há dois tipos de filas nas Funções Duráveis: a **fila de controle** e a **fila de itens de trabalho**.

### <a name="the-work-item-queue"></a>A fila de item de trabalho

Há uma fila de itens de trabalho por hub de tarefas nas Funções Duráveis. Essa é uma fila básica, que se comporta da mesma forma que qualquer outra fila `queueTrigger` no Azure Functions. Essa fila é usada para disparar *funções de atividade* sem estado para remover da fila uma mensagem única por vez. Cada uma dessas mensagens contém entradas de função de atividades e metadados adicionais, como a função para executar. Quando um aplicativo de Funções Duráveis é expandido para várias VMs, todas essas VMs concorrem para obter trabalho da fila de itens de trabalho.

### <a name="control-queues"></a>Fila(s) de controle

Há várias *filas de controle* por hub de tarefa em Funções Duráveis. Uma *fila de controle* é mais sofisticada do que as filas de itens de trabalho mais simples. Filas de controle são usadas para acionar as funções do orquestrador e da entidade. Como as instâncias de função orquestradora e de entidade são singletons imponentes, não é possível usar um modelo de consumidor concorrente para distribuir carga entre VMs. Em vez disso, as mensagens orquestradoras e de entidade saem equilibradas em todas as filas de controle. Para obter mais detalhes sobre esse comportamento, verifique as seções subsequentes.

Filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Exemplos incluem [mensagens de controle de orquestrador](durable-functions-instance-management.md), mensagens de *resposta* de função de atividade e mensagens de temporizador. Até 32 mensagens serão removidas da fila de uma fila de controle em uma única chamada seletiva. Essas mensagens contêm dados de conteúdo, bem como metadados, incluindo qual instância de orquestração é destinada. Se várias mensagens removidas da fila destinam-se à mesma instância de orquestração, será processado como um lote.

### <a name="queue-polling"></a>Votação na fila

A extensão de tarefa durável implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito da votação em fila ociosa sobre os custos de transação de armazenamento. Quando uma mensagem é encontrada, o tempo de execução verifica imediatamente se há outra mensagem; quando nenhuma mensagem é encontrada, ela espera por um período de tempo antes de tentar novamente. Após tentativas fracassadas subseqüentes de obter uma mensagem de fila, o tempo de espera continua aumentando até atingir o tempo máximo de espera, que é padrão para 30 segundos.

O atraso máximo de votação é configurável através da `maxQueuePollingInterval` propriedade no arquivo [host.json](../functions-host-json.md#durabletask). Definir essa propriedade para um valor mais alto pode resultar em latências de processamento de mensagens mais altas. Latências mais altas só seriam esperadas após períodos de inatividade. Definir essa propriedade para um valor mais baixo pode resultar em custos de armazenamento mais altos devido ao aumento das transações de armazenamento.

> [!NOTE]
> Ao ser executado nos planos Azure Functions Consumption and Premium, o Controlador de Escala de Funções do [Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) fará uma pesquisa sobre cada controle e fila de itens de trabalho uma vez a cada 10 segundos. Essa votação adicional é necessária para determinar quando ativar instâncias do aplicativo de função e tomar decisões em escala. No momento da escrita, este intervalo de 10 segundos é constante e não pode ser configurado.

## <a name="storage-account-selection"></a>Seleção da conta de armazenamento

As filas, tabelas e blobs usadas por Funções Duráveis são criadas em uma conta configurada do Azure Storage. A conta a ser usada `durableTask/storageProvider/connectionStringName` pode ser `durableTask/azureStorageConnectionStringName` especificada usando a configuração (ou configuração em Funções Duráveis 1.x) no arquivo **host.json.**

### <a name="durable-functions-2x"></a>Funções duráveis 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funções duráveis 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se não estiver especificado, a conta de armazenamento `AzureWebJobsStorage` padrão é usada. Para cargas de trabalho afetadas pelo desempenho, no entanto, configurar uma conta de armazenamento padrão não é recomendável. Funções duráveis usam o Armazenamento do Microsoft Azure intensamente e usam uma conta de armazenamento dedicado que isola o uso do armazenamento de funções duráveis com o uso interno pelo host de Funções do Microsoft Azure.

## <a name="orchestrator-scale-out"></a>Expansão do orquestrador

As funções de atividade são sem estado e são expandidas automaticamente adicionando VMs. As funções e entidades do orquestrador, por outro lado, são *divididas* em uma ou mais filas de controle. O número de filas do controle é definido no arquivo **host.json**. O exemplo a seguir, o trecho `durableTask/storageProvider/partitionCount` host.json define a propriedade `durableTask/partitionCount` `3`(ou em Funções Duráveis 1.x) para .

### <a name="durable-functions-2x"></a>Funções duráveis 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funções duráveis 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Um hub de tarefa pode ser configurado com entre 1 e 16 partições. Se não for especificado, a contagem de participação padrão 4** será usada**.

Ao expandir para várias instâncias de host de função (normalmente, em VMs diferentes), cada instância adquire um bloqueio de uma das filas de controle. Esses bloqueios são implementados internamente como locações de armazenamento blob e garantem que uma instância ou entidade de orquestração seja executada apenas em uma única instância de host por vez. Se um hub de tarefas for configurado com três filas de controle, instâncias e entidades de orquestração podem ser equilibradas em até três VMs. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.

O diagrama a seguir ilustra como o host do Azure Functions interage com as entidades de armazenamento em um ambiente expandido.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-diagram.png)

Conforme mostrado no diagrama anterior, todas as máquinas virtuais competem por mensagens na fila do item de trabalho. No entanto, apenas três VMs podem adquirir mensagens das filas de controle e cada VM bloqueia uma única fila de controle.

As instâncias e entidades de orquestração são distribuídas em todas as instâncias da fila de controle. A distribuição é feita por meio do hashing do ID de ocorrência da orquestração ou do nome da entidade e do par de chaves. Os IDs de instância de orquestração por padrão são GUIDs aleatórios, garantindo que as instâncias sejam distribuídas igualmente em todas as filas de controle.

De modo geral, as funções de orquestrador devem ser leves e não devem precisar de muita capacidade de computação. Portanto, não é necessário criar um grande número de partições de fila de controle para obter grande throughput para orquestrações. A maior parte do trabalho pesado será feita em funções de atividade sem monitoração de estado, que podem ser expandidas infinitamente.

## <a name="auto-scale"></a>Dimensionamento automático

Como acontece com todas as funções do Azure em execução nos planos Consumption e Elastic Premium, as Funções Duráveis suportam a escala automática através do [controlador de escala Funções Azure](../functions-scale.md#runtime-scaling). O Controlador de Escala monitora a latência de todas as filas periodicamente emitindo comandos de _inspeção_. Com base nas latências das mensagens inspecionadas, o controlador de escala irá decidir se deseja adicionar ou remover VMs.

Se o controlador de escala determina que as latências de mensagem de fila de controle são muito altas, ele irá adicionar instâncias de VM até que a latência de mensagem diminua em um nível aceitável ou atinja a contagem de partições de fila do controle. Da mesma forma, o controlador de escala continuamente adiciona instâncias de VM se as latências de fila de item de trabalho são alta, independentemente da contagem de partição.

> [!NOTE]
> A partir das Funções Duráveis 2.0, os aplicativos de função podem ser configurados para serem executados dentro de pontos finais de serviço protegidos pela VNET no plano Elastic Premium. Nesta configuração, os gatilhos Funções Duráveis iniciam solicitações de escala em vez do Controlador de Escala.

## <a name="thread-usage"></a>Uso de thread

As funções do Orchestrator são executadas em um único thread para assegurar que a execução pode ser determinística entre muitas repetições. Devido a essa execução de thread único, é importante que os threads de função do orquestrador não execute tarefas de uso intensivo de CPU, faça E/S ou bloqueie por algum motivo. Qualquer trabalho que possa exigir E/S, bloqueio ou vários threads deve ser movido para funções de atividade.

As funções de atividade têm os mesmos comportamentos que as funções disparadas por filas regulares. Elas podem, de maneira segura, fazer E/S, executar operações com uso intensivo de CPU e usar vários threads. Como os gatilhos de atividade são sem monitoração de estado, eles podem ser expandidos livremente para um número ilimitado de VMs.

As funções da entidade também são executadas em um único segmento e as operações são processadas uma por vez. No entanto, as funções da entidade não possuem restrições sobre o tipo de código que pode ser executado.

## <a name="concurrency-throttles"></a>Restrições de simultaneidade

O Azure Functions suporta a execução de várias funções simultaneamente em uma instância de aplicativo único. Essa execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "frios" que um aplicativo típico terá ao longo do tempo. No entanto, a alta concorrência pode esgotar recursos do sistema por VM, tais conexões de rede ou memória disponível. Dependendo das necessidades do aplicativo de função, pode ser necessário aumentar a simultaneidade por instância para evitar a possibilidade de falta de memória em situações de carga alta.

Os limites de concorrência de atividade, orquestrador e função de entidade podem ser configurados no arquivo **host.json.** As configurações `durableTask/maxConcurrentActivityFunctions` relevantes são `durableTask/maxConcurrentOrchestratorFunctions` para funções de atividade e para funções orquestradoras e de entidade.

### <a name="functions-20"></a>Funções 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

No exemplo anterior, um máximo de 10 funções de orquestrador ou entidade e 10 funções de atividade podem ser executadas em um único VM simultaneamente. Se não especificado, o número de execuções simultâneas de atividade simultânea e orquestrador ou função de entidade é limitado a 10X o número de núcleos na VM.

> [!NOTE]
> Essas configurações são úteis para ajudar a gerenciar o uso da memória e CPU em uma única VM. No entanto, quando dimensionado através de várias VMs, cada VM tem seu próprio conjunto de limites. Essas configurações não podem ser usadas para controlar a concorrência em nível global.

## <a name="extended-sessions"></a>Sessões estendidas

Sessões estendidas é um cenário que mantém orquestrações e entidades na memória mesmo depois de terminarem de processar mensagens. O efeito típico de habilitar sessões estendidas é E/S reduzidas em relação à conta de Armazenamento do Microsoft Azure e maior taxa de transferência geral.

Você pode habilitar `durableTask/extendedSessionsEnabled` sessões estendidas configurando para `true` no arquivo **host.json.** A `durableTask/extendedSessionIdleTimeoutInSeconds` configuração pode ser usada para controlar por quanto tempo uma sessão ociosa será mantida na memória:

**Funções 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Funções 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Há duas desvantagens potenciais deste cenário para estar ciente:

1. Há um aumento geral no uso da memória do aplicativo de função.
2. Pode haver uma redução geral no throughput se houver muitas execuções simultâneas, orquestradora de curta duração ou função de entidade.

Como exemplo, `durableTask/extendedSessionIdleTimeoutInSeconds` se for definido para 30 segundos, então um episódio de função de orquestrador ou entidade de curta duração que é executado em menos de 1 segundo ainda ocupa a memória por 30 segundos. Ele também conta `durableTask/maxConcurrentOrchestratorFunctions` contra a cota mencionada anteriormente, potencialmente impedindo que outras funções orquestradoras ou de entidades funcionem.

Os efeitos específicos das sessões estendidas sobre as funções orquestradoras e de entidades são descritos nas próximas seções.

### <a name="orchestrator-function-replay"></a>Reprodução de função do Orchestrator

Conforme mencionado anteriormente, as funções do orquestrador são repetidas usando o conteúdo da tabela **Histórico**. Por padrão, o código de função do orquestrador é repetido sempre que um lote de mensagens for removido da fila de uma fila de controle. Mesmo se você estiver usando o padrão fan-out, fan-in e estiver aguardando que todas as tarefas sejam concluídas (por exemplo, usando `Task.WhenAll` em .NET ou `context.df.Task.all` em JavaScript), haverá replays que ocorrem à medida que lotes de respostas de tarefas são processados ao longo do tempo. Quando as sessões estendidas são ativadas, as instâncias de função orquestradoras são mantidas na memória por mais tempo e novas mensagens podem ser processadas sem um replay completo do histórico.

A melhora de desempenho das sessões estendidas é mais observada nas seguintes situações:

* Quando há um número limitado de instâncias de orquestração em execução simultânea.
* Quando as orquestrações têm um grande número de ações seqüenciais (por exemplo, centenas de chamadas de função de atividade) que se completam rapidamente.
* Quando orquestrações fan-out e fan-in um grande número de ações que completam ao mesmo tempo.
* Quando as funções do orquestrador precisam processar mensagens grandes ou fazer qualquer processamento de dados intensivo na CPU.

Em todas as outras situações, normalmente não há melhoria de desempenho observável para funções orquestradoras.

> [!NOTE]
> Essas configurações só devem ser usadas depois que uma função do orquestrador for totalmente desenvolvida e testada. O comportamento de repetição agressivo padrão pode ser útil para detectar violações de [código de função orquestradora](durable-functions-code-constraints.md) no momento do desenvolvimento e, portanto, é desativado por padrão.

### <a name="entity-function-unloading"></a>Descarga de função da entidade

As funções da entidade processam até 20 operações em um único lote. Assim que uma entidade termina de processar um lote de operações, ela persiste em seu estado e descarrega da memória. Você pode atrasar o descarregamento de entidades da memória usando a configuração de sessões estendidas. As entidades continuam a persistir suas alterações de estado como antes, mas permanecem na memória durante o período de tempo configurado para reduzir o número de cargas do Armazenamento Azure. Essa redução de cargas do Azure Storage pode melhorar o throughput geral de entidades com acesso frequente.

## <a name="performance-targets"></a>Destinos de desempenho

Ao planejar usar funções duráveis para um aplicativo de produção, é importante considerar os requisitos de desempenho no início do processo de planejamento. Esta seção aborda alguns cenários de uso básico e os números de taxa de transferência máxima esperada.

* **Execução de atividades sequenciais**: este cenário descreve uma função do orquestrador que executa uma série de funções de atividade um após o outro. Ele é bastante semelhante ao exemplo de [Encadeamento de função](durable-functions-sequence.md).
* **Execução de atividade paralela**: este cenário descreve uma função do orquestrador que executa muitas funções de atividades em paralelo usando o padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md).
* **Resposta de processamento paralelo**: esse cenário é a segunda metade do padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md). Se concentra no desempenho de fan-in. É importante observar que ao contrário do tipo fan-out, fan-in é feito por uma instância de função do orchestrator único e, portanto, só pode executar em uma única VM.
* **Processamento de eventos externos**: esse cenário representa uma instância de função do orquestrador único que espera [eventos externos](durable-functions-external-events.md), um de cada vez.
* **Processamento de operação de**entidades : Este cenário testa a rapidez com que uma _única_ [entidade contadora](durable-functions-entities.md) pode processar um fluxo constante de operações.

> [!TIP]
> Ao contrário do tipo fan-out, as operações de consolidação são limitadas a uma única VM. Se seu aplicativo usa o padrão fan-out, fan-in e você estiver preocupado sobre o desempenho de fan-in, divida abaixo a divisão da função de atividade em várias [sub-orquestrações](durable-functions-sub-orchestrations.md).

A tabela a seguir mostra os números *máximos* de taxa de transferência para os cenários descritos anteriormente. "Instância" refere-se a uma única instância de uma função do orquestrador em execução em uma única pequena VM ([A1](../../virtual-machines/sizes-previous-gen.md)) no Serviço do Aplicativo do Azure. Todos os casos, presume-se que [sessões estendidas](#orchestrator-function-replay) estão habilitadas. Os resultados reais podem variar dependendo do trabalho de CPU ou E/S executado pelo código da função.

| Cenário | Taxa de transferência máxima |
|-|-|
| Execução da atividade sequencial | 5 atividades por segundo, por instância |
| Execução de atividade paralela (fan-out) | 100 atividades por segundo, por instância |
| Processamento paralelo de resposta (fan-in) | 150 respostas por segunda, por instância |
| Processamento de evento externo | 50 eventos por segunda, por instância |
| Processamento de operação de entidades | 64 operações por segundo |

> [!NOTE]
> Esses números são atuais a partir da versão v1.4.0 (GA) da extensão de Funções Duráveis. Esses números podem alterar ao longo do tempo conforme o recurso amadurece e otimizações que são feitas.

Se você não estiver vendo os números de taxa de transferência esperada e a CPU e uso de memória aparece íntegra, verifique se a causa está relacionada à integridade [ de sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A extensão de Funções Duráveis pode colocar carga significativa em uma conta de Armazenamento do Microsoft Azure e carga suficientemente altas podem resultar na limitação da conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre recuperação de desastres e geodistribuição](durable-functions-disaster-recovery-geo-distribution.md)
