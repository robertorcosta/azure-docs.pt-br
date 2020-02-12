---
title: Visão geral das versões do Durable Functions-Azure Functions
description: Saiba mais sobre as versões do Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152883"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das versões do Durable Functions

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Se você ainda não estiver familiarizado com Durable Functions, consulte a [documentação de visão geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novos recursos no 2. x

Esta seção descreve os recursos de Durable Functions que são adicionados na versão 2. x.

### <a name="durable-entities"></a>Entidades duráveis

No Durable Functions 2. x, introduzimos um novo conceito de [funções de entidade](durable-functions-entities.md) .

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

Para saber mais, confira o artigo sobre [entidades duráveis](durable-functions-entities.md) .

### <a name="durable-http"></a>HTTP durável

No Durable Functions 2. x, apresentamos um novo recurso de [http durável](durable-functions-http-features.md#consuming-http-apis) que permite:

* Chame APIs HTTP diretamente de funções de orquestração (com algumas limitações documentadas).
* Implementar sondagem de status HTTP 202 do lado do cliente automático.
* Suporte interno para [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para saber mais, consulte o artigo [recursos http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1. x para 2. x

Esta seção descreve como migrar suas Durable Functions existentes da versão 1. x para a versão 2. x para aproveitar os novos recursos.

### <a name="upgrade-the-extension"></a>Atualizar a extensão

Instale a versão 2. x da [extensão de associações Durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto. Consulte [registrar Azure Functions extensões de associação](../functions-bindings-register.md) para obter mais informações.

### <a name="update-your-code"></a>Atualizar seu código

O Durable Functions 2. x apresenta várias alterações significativas. Os aplicativos Durable Functions 1. x não são compatíveis com o Durable Functions 2. x sem alterações de código. Esta seção lista algumas das alterações que você deve fazer ao atualizar suas funções da versão 1. x para 2. x.

#### <a name="hostjson-schema"></a>Esquema host. JSON

O Durable Functions 2. x usa um novo esquema host. JSON. As principais alterações de 1. x incluem:

* `"storageProvider"` (e a subseção `"azureStorage"`) para configuração específica do armazenamento.
* `"tracing"` para rastreamento e configuração de log.
* `"notifications"` (e a subseção `"eventGrid"`) para configuração de notificação da grade de eventos.

Consulte a [documentação de referência do Durable Functions host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter detalhes.

#### <a name="default-taskhub-name-changes"></a>Alterações de nome de taskhub padrão

Na versão 1. x, se um nome de Hub de tarefas não foi especificado em host. JSON, o padrão é "DurableFunctionsHub". Na versão 2. x, o nome do hub de tarefas padrão agora é derivado do nome do aplicativo de funções. Por isso, se você não tiver especificado um nome de Hub de tarefas ao atualizar para 2. x, seu código estará operando com o novo hub de tarefas e todas as orquestrações em andamento não terão mais um aplicativo processando-os. Para contornar isso, você pode definir explicitamente o nome do hub de tarefas para o padrão v1. x de "DurableFunctionsHub", ou pode seguir nossas [diretrizes de implantação sem tempo de inatividade](durable-functions-zero-downtime-deployment.md) para obter detalhes sobre como lidar com alterações significativas para orquestrações em andamento.

#### <a name="public-interface-changes-net-only"></a>Alterações de interface pública (somente .NET)

Na versão 1. x, os vários objetos de _contexto_ com suporte pelo Durable Functions têm classes base abstratas destinadas a uso em testes de unidade. Como parte do Durable Functions 2. x, essas classes base abstratas são substituídas por interfaces.

A tabela a seguir representa as principais alterações:

| 1.x | 2. x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>alterações de Function. JSON (JavaScript C# e script)

No Durable Functions 1. x, a associação de cliente de orquestração usa uma `type` de `orchestrationClient`. A versão 2. x usa `durableClient` em vez disso.
