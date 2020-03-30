---
title: Restrições de código orquestrador duráveis - Funções Azure
description: Replay da função de orquestração e restrições de código para funções duráveis do Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562132"
---
# <a name="orchestrator-function-code-constraints"></a>Restrições de código de função orquestradora

Durable Functions é uma extensão das [funções do Azure](../functions-overview.md) que permite construir aplicativos imponentes. Você pode usar uma [função orquestradora](durable-functions-orchestrations.md) para orquestrar a execução de outras funções duráveis dentro de um aplicativo de função. As funções orquestradoras são imponentes, confiáveis e potencialmente de longa duração.

## <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

As funções do orquestrador usam [o sourcing de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) para garantir uma execução confiável e manter o estado variável local. O comportamento de [repetição](durable-functions-orchestrations.md#reliability) do código orquestrador cria restrições sobre o tipo de código que você pode escrever em uma função orquestradora. Por exemplo, as funções orquestradoras devem ser *determinísticas:* uma função orquestradora será repetida várias vezes, e deve produzir o mesmo resultado todas as vezes.

### <a name="using-deterministic-apis"></a>Usando APIs deterministas

Esta seção fornece algumas diretrizes simples que ajudam a garantir que seu código seja determinístico.

As funções do orquestrador podem chamar qualquer API em seus idiomas-alvo. No entanto, é importante que as funções orquestradoras chamem apenas APIs deterministas. Uma *API determinista* é uma API que sempre retorna o mesmo valor dada a mesma entrada, não importa quando ou com que frequência é chamada.

A tabela a seguir mostra exemplos de APIs que você deve evitar porque elas *não* são deterministas. Essas restrições se aplicam apenas às funções orquestradoras. Outros tipos de funções não têm tais restrições.

| Categoria API | Motivo | Solução alternativa |
| ------------ | ------ | ---------- |
| Datas e horas  | As APIs que retornam a data ou hora atuais não são deterministas porque o valor retornado é diferente para cada replay. | Use`CurrentUtcDateTime` a API em `currentUtcDateTime` .NET ou a API em JavaScript, que são seguras para replay. |
| GUIDs e UUIDs  | As APIs que retornam um GUID aleatório ou UUID não são deterministas porque o valor gerado é diferente para cada replay. | Use `NewGuid` em .NET ou `newGuid` em JavaScript para gerar GUIDs aleatórios com segurança. |
| Números aleatórios | ApIs que retornam números aleatórios não são deterministas porque o valor gerado é diferente para cada replay. | Use uma função de atividade para retornar números aleatórios a uma orquestração. Os valores de retorno das funções de atividade são sempre seguros para replay. |
| Associações | As ligações de entrada e saída normalmente fazem I/O e são não deterministas. Uma função orquestradora não deve usar diretamente mesmo as vinculações do [cliente de orquestração](durable-functions-bindings.md#orchestration-client) e do cliente da [entidade.](durable-functions-bindings.md#entity-client) | Use ligações de entrada e saída dentro das funções de cliente ou atividade. |
| Rede | As chamadas de rede envolvem sistemas externos e são não deterministas. | Use funções de atividade para fazer chamadas de rede. Se você precisar fazer uma chamada HTTP a partir de sua função orquestradora, você também pode usar as [APIs HTTP duráveis](durable-functions-http-features.md#consuming-http-apis). |
| Bloqueio de APIs | O bloqueio `Thread.Sleep` de APIs como em .NET e APIs similares pode causar problemas de desempenho e escala para funções orquestradoras e deve ser evitado. No plano de consumo de funções do Azure, eles podem até resultar em cobranças desnecessárias de tempo de execução. | Use alternativas para bloquear APIs quando estiverem disponíveis. Por exemplo, `CreateTimer` use para introduzir atrasos na execução de orquestração. [Atrasos duráveis do temporizador](durable-functions-timers.md) não contam para o tempo de execução de uma função orquestradora. |
| APIs assíncronas | O código orquestrador nunca deve iniciar qualquer `IDurableOrchestrationContext` operação de `context.df` assincronia, exceto usando a API ou a API do objeto. Por exemplo, você não `Task.Run` `Task.Delay`pode `HttpClient.SendAsync` usar , `setTimeout` e `setInterval` em .NET ou e em JavaScript. O Durable Task Framework executa o código orquestrador em um único segmento. Ele não pode interagir com outros segmentos que possam ser chamados por outras APIs assíncronas. | Uma função orquestradora deve fazer apenas chamadas assíncronas duráveis. As funções de atividade devem fazer quaisquer outras chamadas async da API. |
| Funções JavaScript async | Você não pode declarar funções orquestradoras JavaScript como `async` porque o tempo de execução do nó.js não garante que as funções assíncronas sejam determinísticas. | Declare funções do orquestrador JavaScript como funções síncronas do gerador. |
| APIs de rosca | O Durable Task Framework executa o código orquestrador em um único segmento e não pode interagir com outros segmentos. Introduzir novos tópicos na execução de uma orquestração pode resultar em execução não determinista ou impasses. | As funções do orquestrador quase nunca devem usar APIs de rosca. Por exemplo, em .NET, evite usar; `ConfigureAwait(continueOnCapturedContext: false)` isso garante que as continuações da tarefa `SynchronizationContext`são executadas no original da função orquestradora . Se essas APIs forem necessárias, limite seu uso apenas para funções de atividade. |
| Variáveis estáticas | Evite usar variáveis estáticas não constantes em funções orquestrais porque seus valores podem mudar com o tempo, resultando em comportamento não determinístico de tempo de execução. | Use constantes ou limite o uso de variáveis estáticas para funções de atividade. |
| Variáveis de ambiente | Não use variáveis de ambiente em funções orquestrais. Seus valores podem mudar com o tempo, resultando em comportamento não determinístico de tempo de execução. | As variáveis de ambiente devem ser referenciadas apenas a partir de funções do cliente ou funções de atividade. |
| Loops infinitos | Evite loops infinitos em funções de orquestrador. Como o Quadro de Tarefas Duráveis salva o histórico de execução à medida que a função de orquestração progride, um loop infinito pode fazer com que uma instância orquestradora se esime da memória. | Para cenários de loop `ContinueAsNew` infinito, use `continueAsNew` APIs como em .NET ou em JavaScript para reiniciar a execução da função e descartar o histórico de execução anterior. |

Embora aplicar essas restrições possa parecer difícil no início, na prática eles são fáceis de seguir.

O Quadro de Tarefas Duráveis tenta detectar violações das regras anteriores. Se encontrar uma violação, a estrutura lançará uma exceção **NonDeterministicOrchestrationException.** No entanto, esse comportamento de detecção não pegará todas as violações, e você não deve depender disso.

## <a name="versioning"></a>Controle de versão

Uma orquestração duradoura pode funcionar continuamente por dias, meses, anos ou até mesmo [eternamente.](durable-functions-eternal-orchestrations.md) Quaisquer atualizações de código feitas em aplicativos de Funções Duráveis que afetem orquestrações inacabadas podem quebrar o comportamento de repetição das orquestrações. É por isso que é importante planejar cuidadosamente ao fazer atualizações de código. Para obter uma descrição mais detalhada de como versão do seu código, consulte o [artigo de versão](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tarefas duráveis

> [!NOTE]
> Esta seção descreve os detalhes de implementação interna do Framework de Tarefa Durável. Você pode usar funções duráveis sem saber essas informações. Elas destinam-se somente a ajudá-lo a entender o comportamento de reprodução.

Tarefas que podem esperar com segurança em funções orquestradoras são ocasionalmente referidas como *tarefas duráveis*. O Quadro de Tarefas Duráveis cria e gerencia essas tarefas. Exemplos são as tarefas retornadas por **CallActivityAsync,** **WaitForExternalEvent**e **CreateTimer** em funções orquestradoras .NET.

Essas tarefas duráveis são gerenciadas `TaskCompletionSource` internamente por uma lista de objetos em .NET. Durante o replay, essas tarefas são criadas como parte da execução de código orquestrador. Eles terminaram quando o despachante enumera os eventos históricos correspondentes.

As tarefas são executadas ssincronizadamente usando um único segmento até que toda a história tenha sido reproduzida. Tarefas duráveis que não são concluídas até o final do replay da história têm ações apropriadas realizadas. Por exemplo, uma mensagem pode ser enfileirada para chamar uma função de atividade.

A descrição desta seção do comportamento em tempo de execução `await` deve `yield` ajudá-lo a entender por que uma função orquestradora não pode usar ou em uma tarefa não durável. Há duas razões: o segmento de despachante não pode esperar que a tarefa seja concluída, e qualquer retorno por essa tarefa pode potencialmente corromper o estado de rastreamento da função orquestradora. Algumas verificações de tempo de execução estão em vigor para ajudar a detectar essas violações.

Para saber mais sobre como o Durable Task Framework executa funções orquestradoras, consulte o [código-fonte de tarefa durável no GitHub](https://github.com/Azure/durabletask). Em particular, veja [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda a invocar suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)
