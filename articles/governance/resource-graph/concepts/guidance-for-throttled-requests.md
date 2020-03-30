---
title: Diretrizes para solicitações limitadas
description: Aprenda a agrupar, cambalear, paginar e consultar em paralelo para evitar que as solicitações sejam estranguladas pelo Gráfico de Recursos do Azure.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259845"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Orientação para solicitações estranguladas no Gráfico de Recursos do Azure

Ao criar o uso programático e frequente dos dados do Gráfico de Recursos do Azure, deve-se considerar como o estrangulamento impacta os resultados das consultas. Alterar a forma como os dados são solicitados pode ajudar você e sua organização a evitar serem estrangulados e manter o fluxo de dados oportunos sobre seus recursos do Azure.

Este artigo abrange quatro áreas e padrões relacionados à criação de consultas no Gráfico de Recursos do Azure:

- Noções básicas sobre os cabeçalhos de limitação
- Consultas de agrupamento
- Consultas de escalonamento
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Noções básicas sobre os cabeçalhos de limitação

O Azure Resource Graph aloca o número da cota para cada usuário com base em uma janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas em cada janela de 5 segundos sem ser estrangulado. O valor da cota é determinado por muitos fatores e está sujeito a alterações.

Em cada resposta de consulta, o Azure Resource Graph adiciona dois cabeçalhos de estrangulamento:

- `x-ms-user-quota-remaining`(int): A cota de recursos restante para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after`(hh:mm:ss): A duração do tempo até que o consumo de cotas do usuário seja redefinido.

Para ilustrar como os cabeçalhos funcionam, vamos olhar para uma `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03`resposta de consulta que tem o cabeçalho e os valores de e .

- Nos próximos 3 segundos, no máximo 10 consultas podem ser submetidas sem serem estranguladas.
- Em 3 segundos, `x-ms-user-quota-remaining` os `x-ms-user-quota-resets-after` valores de `15` `00:00:05` e serão redefinidos para e respectivamente.

Para ver um exemplo de uso dos cabeçalhos para _fazer o backup_ nas solicitações de consulta, consulte a amostra em Consulta em [Paralelo](#query-in-parallel).

## <a name="grouping-queries"></a>Consultas de agrupamento

Agrupar consultas pela assinatura, grupo de recursos ou recurso individual é mais eficiente do que fazer uma paralelização de consultas. O custo de cota de uma consulta maior é muitas vezes menor do que o custo de cota de muitas consultas pequenas e direcionadas. Recomenda-se que o tamanho do grupo seja inferior a _300_.

- Exemplo de uma abordagem mal otimizada

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemplo #1 de uma abordagem de agrupamento otimizada

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemplo #2 de uma abordagem de agrupamento otimizada para obter vários recursos em uma consulta

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Consultas de escalonamento

Devido à forma como o estrangulamento é aplicado, recomendamos que as consultas sejam escalonadas. Ou seja, em vez de enviar 60 consultas ao mesmo tempo, escalonar as consultas em quatro janelas de 5 segundos:

- Cronograma de consulta não escalonado

  | Query Count         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

- Cronograma de consulta escalonado

  | Query Count         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

Abaixo está um exemplo de respeito aos cabeçalhos de estrangulamento ao consultar o Gráfico de Recursos do Azure:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Consulta em Paralelo

Embora o agrupamento seja recomendado sobre a paralelização, há momentos em que as consultas não podem ser facilmente agrupadas. Nesses casos, você pode querer consultar o Gráfico de Recursos do Azure enviando várias consultas de forma paralela. Abaixo está um exemplo de como _fazer o backoff_ com base no estrangulamento de cabeçalhos em tais cenários:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginação

Uma vez que o Azure Resource Graph retorna no máximo 1000 entradas em uma única resposta de consulta, você pode precisar [paginar](./work-with-data.md#paging-results) suas consultas para obter o conjunto de dados completo que você está procurando. No entanto, alguns clientes do Azure Resource Graph lidam com paginação de forma diferente de outros.

- SDK do C#

  Ao usar o ResourceGraph SDK, você precisa lidar com a paginação passando o token de skip sendo devolvido da resposta de consulta anterior para a próxima consulta paginada. Este design significa que você precisa coletar resultados de todas as chamadas paginadas e combiná-las juntas no final. Neste caso, cada consulta paginada enviada leva uma cota de consulta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  Ao usar o Azure CLI ou o Azure PowerShell, as consultas ao Azure Resource Graph são paginadas automaticamente para obter no máximo 5000 entradas. Os resultados da consulta retornam uma lista combinada de entradas de todas as chamadas paginadas. Neste caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais de uma cota de consulta. Por exemplo, no exemplo abaixo, uma única execução da consulta pode consumir até cinco cotas de consulta:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Ainda ser estrangulado?

Se você estiver sendo estrangulado após exercer as recomendações [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)acima, entre em contato com a equipe em .

Forneça estes detalhes:

- Seu caso de uso específico e as necessidades do driver de negócios para um limite de estrangulamento mais alto.
- Quantos recursos você tem acesso? Quantos deles são devolvidos de uma única consulta?
- Em que tipos de recursos você está interessado?
- Qual é o seu padrão de consulta? X consultas por Y segundos etc.

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso em [consultas iniciais](../samples/starter.md).
- Veja usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).