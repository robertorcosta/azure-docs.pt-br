---
title: Visão geral das versões duráveis - Funções do Azure
description: Saiba mais sobre as versões Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152883"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das versões duráveis

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Se você ainda não estiver familiarizado com funções duráveis, consulte a [documentação de visão geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novos recursos em 2.x

Esta seção descreve os recursos de Funções Duráveis que são adicionados na versão 2.x.

### <a name="durable-entities"></a>Entidades duráveis

Em Funções Duráveis 2.x, introduzimos um novo conceito [de funções de entidade.](durable-functions-entities.md)

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções orquestradoras, as funções da entidade não possuem restrições específicas de código. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

Para saber mais, consulte o artigo [das entidades duráveis.](durable-functions-entities.md)

### <a name="durable-http"></a>Durável HTTP

Em Funções Duráveis 2.x, introduzimos um novo recurso [HTTP durável](durable-functions-http-features.md#consuming-http-apis) que permite:

* Ligue para as APIs HTTP diretamente das funções de orquestração (com algumas limitações documentadas).
* Implemente a pesquisa automática de status HTTP 202 do lado do cliente.
* Suporte integrado para [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para saber mais, consulte o artigo [http features.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1.x para 2.x

Esta seção descreve como migrar sua versão existente 1.x Funções Duráveis para a versão 2.x para aproveitar os novos recursos.

### <a name="upgrade-the-extension"></a>Atualize a extensão

Instale a versão 2.x da [extensão de vinculações funções duráveis](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto. Consulte [Registre as extensões de vinculação funções do Azure](../functions-bindings-register.md) para obter mais informações.

### <a name="update-your-code"></a>Atualize seu código

Durable Functions 2.x introduz várias mudanças de ruptura. As funções duráveis 1.x não são compatíveis com funções duráveis 2.x sem alterações de código. Esta seção lista algumas das alterações que você deve fazer ao atualizar as funções da versão 1.x para 2.x.

#### <a name="hostjson-schema"></a>Esquema host.json

Durable Functions 2.x usa um novo esquema host.json. As principais alterações de 1.x incluem:

* `"storageProvider"`(e `"azureStorage"` a subseção) para configuração específica de armazenamento.
* `"tracing"`para rastreamento e configuração de registro.
* `"notifications"`(e `"eventGrid"` a subseção) para a configuração de notificação da grade de eventos.

Consulte a [documentação de referência do host.json de funções duráveis](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter detalhes.

#### <a name="default-taskhub-name-changes"></a>Alterações de nome do taskhub padrão

Na versão 1.x, se um nome de hub de tarefas não fosse especificado no host.json, ele seria padrão para "DurableFunctionsHub". Na versão 2.x, o nome padrão do hub de tarefas agora é derivado do nome do aplicativo de função. Por causa disso, se você não especificou um nome de hub de tarefas ao atualizar para 2.x, seu código estará operando com um novo hub de tarefas, e todas as orquestrações a bordo não terão mais um aplicativo processando-os. Para contornar isso, você pode definir explicitamente o nome do hub de tarefas para o padrão v1.x de "DurableFunctionsHub", ou seguir nossa [orientação de implantação de tempo zero de inatividade](durable-functions-zero-downtime-deployment.md) para obter detalhes sobre como lidar com alterações de quebra para orquestrações a bordo.

#### <a name="public-interface-changes-net-only"></a>Alterações na interface pública (somente.NET)

Na versão 1.x, os vários objetos _de contexto_ suportados por Funções Duráveis têm classes de base abstratas destinadas ao uso em testes unitários. Como parte das Funções Duráveis 2.x, essas classes base abstratas são substituídas por interfaces.

A tabela a seguir representa as principais alterações:

| 1.x | 2. x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>alterações function.json (JavaScript e C# Script)

Em Funções duráveis 1.x, a `type` vinculação do cliente de orquestração usa a de `orchestrationClient`. A versão 2.x usa `durableClient` em seu lugar.
