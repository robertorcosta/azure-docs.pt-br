---
title: Atrações de saída HTTP funções do Azure
description: Saiba como retornar respostas HTTP em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277616"
---
# <a name="azure-functions-http-output-bindings"></a>Atrações de saída HTTP funções do Azure

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho.

O valor de retorno padrão para uma função acionada pelo HTTP é:

- `HTTP 204 No Content`com um corpo vazio em Funções 2.x e superior
- `HTTP 200 OK`com um corpo vazio em Funções 1.x

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*. Para as bibliotecas de classes C#, não há propriedades de atributo que correspondem a essas propriedades do *function.json*.

|Propriedade  |Descrição  |
|---------|---------|
| **type** |Deve ser definido como `http`. |
| **direction** | Deve ser definido como `out`. |
| **name** | O nome da variável usada no código de função para a resposta, ou `$return` para usar o valor de retorno. |

## <a name="usage"></a>Uso

Para enviar uma resposta HTTP, use os padrões de resposta padrão do idioma. Em c# ou script c#, faça o tipo de retorno da função retorno `IActionResult` ou `Task<IActionResult>`. Em c#, um atributo de valor de retorno não é necessário.

Por ver exemplos de respostas, confira o [exemplo de gatilho](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as configurações globais disponíveis para essa vinculação nas versões 2.x ou superior. O arquivo host.json de exemplo abaixo contém apenas as configurações da versão 2.x+ para esta vinculação. Para obter mais informações sobre configurações globais nas versões 2.x e além, consulte [a referência host.json para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
| customHeaders|none|Permite definir cabeçalhos personalizados na resposta HTTP. O exemplo anterior `X-Content-Type-Options` adiciona o cabeçalho à resposta para evitar o cheiro do tipo de conteúdo. |
|dynamicThrottlesEnabled|Verdade<sup>\*</sup>|Quando ativada, essa configuração faz com que o pipeline `connections/threads/processes/memory/cpu/etc` de processamento de solicitações verifique periodicamente os contadores de desempenho do sistema `429 "Too Busy"` como e se algum desses contadores estiver acima de um limite superior incorporado (80%), as solicitações serão rejeitadas com uma resposta até que o contador(s) retorne aos níveis normais.<br/><sup>\*</sup>A inadimplência em um `true`plano de consumo é . O padrão em um `false`plano dedicado é .|
|hsts|não habilitado|Quando `isEnabled` é `true`definido para , o [comportamento HTTP Strict Transport Security (HSTS) do .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) é aplicado, conforme definido na [ `HstsOptions` classe](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). O exemplo acima [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) também define a propriedade para 10 dias. As propriedades `hsts` suportadas são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>excluídohosts</td><td>Uma matriz de strings de nomes de host para os quais o cabeçalho HSTS não é adicionado.</td></tr><tr><td>incluiSubDomínios</td><td>O valor booleano que indica se o parâmetro SubDomain do cabeçalho De segurança de transporte rigoroso está ativado.</td></tr><tr><td>Maxage</td><td>String que define o parâmetro de idade máxima do cabeçalho De Segurança de Transporte Rigoroso.</td></tr><tr><td>pré-carregar</td><td>Booleano que indica se o parâmetro de pré-carga do cabeçalho de segurança de transporte rigoroso está habilitado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Esse valor permite controlar a concorrência, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, você pode ter uma função HTTP que usa um grande número de recursos do sistema (memória/cpu/soquetes) de modo que causa problemas quando a concorrência é muito alta. Ou você pode ter uma função que faz solicitações de saída para um serviço de terceiros, e essas chamadas precisam ser limitadas. Nesses casos, aplicar uma limitação aqui pode ajudar. <br/><sup>*</sup>O padrão para um plano de consumo é 100. O padrão para um plano dedicado`-1`é ilimitado ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de solicitações pendentes mantidas em um dado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Quaisquer solicitações recebidas acima desse limite são rejeitadas com uma resposta "Muito ocupado" 429. Isso permite que os chamadores empreguem estratégias de repetição com base em tempo e também ajuda você a controlar as latências máximas de solicitação. Isso controla apenas o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitação ASP.NET, ainda estarão vigor e não serão afetadas por essa configuração. <br/><sup>\*</sup>A inadimplência para um plano de consumo é 200. O padrão para um plano dedicado`-1`é ilimitado ( ).|
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função a partir de uma solicitação HTTP](./functions-bindings-http-webhook-trigger.md)