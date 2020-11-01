---
title: Unidades de consulta no gêmeos digital do Azure
titleSuffix: Azure Digital Twins
description: Entender o conceito de cobrança das unidades de consulta no Azure digital gêmeos
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 33c572719d76a2add39aec37329679113fcddb76
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146323"
---
# <a name="query-units-in-azure-digital-twins"></a>Unidades de consulta no gêmeos digital do Azure 

Uma **t (unidade de consulta** gêmeos digital) do Azure é uma unidade de computação sob demanda usada para executar suas [consultas do Azure digital gêmeos](how-to-query-graph.md) usando a [API de consulta](/rest/api/digital-twins/dataplane/query). 

Ele abstrai os recursos do sistema, como CPU, IOPS e memória, necessários para executar operações de consulta com suporte do Azure digital gêmeos, permitindo que você acompanhe o uso em unidades de consulta.

A quantidade de unidades de consulta consumidas para executar uma consulta é afetada por...

* a complexidade da consulta
* o tamanho do conjunto de resultados (portanto, uma consulta que retorna 10 resultados consumirá mais QUs do que uma consulta de complexidade semelhante que retorna apenas um resultado)

Este artigo explica como entender as unidades de consulta e acompanhar o consumo da unidade de consulta.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Localizar o consumo de unidade de consulta no Azure digital gêmeos

Ao executar uma consulta usando a [API de consulta](/rest/api/digital-twins/dataplane/query)do gêmeos digital do Azure, você pode examinar o cabeçalho de resposta para controlar o número de QUs que a consulta consumiu. Procure "Query-encargos" na resposta enviada de volta do Azure digital gêmeos.

Os [SDKs](how-to-use-apis-sdks.md) do gêmeos digital do Azure permitem extrair o cabeçalho de cobrança de consulta da resposta paginável. Esta seção mostra como consultar gêmeos digitais e como iterar sobre a resposta paginável para extrair o cabeçalho de encargo de consulta. 

O trecho de código a seguir demonstra como você pode extrair os encargos de consulta incorridos ao chamar a API de consulta. Ele itera sobre as páginas de resposta primeiro para acessar o cabeçalho de cobrança de consulta e, em seguida, itera nos resultados da pesquisa digital em cada página. 

```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins");
int pageNum = 0;

// The "await" keyword here is required, as a call is made when fetching a new page.

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages())
{
    Console.WriteLine($"Page {++pageNum} results:");

    // Extract the query-charge header from the page

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge))
    {
        Console.WriteLine($"Query charge was: {queryCharge}");
    }

    // Iterate over the twin instances.

    // The "await" keyword is not required here, as the paged response is local.

    foreach (string response in page.Values)
    {
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response);
        Console.WriteLine($"Found digital twin '{twin.Id}'");
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como consultar o Azure digital gêmeos, visite:

* [*Conceitos: linguagem de consulta*](concepts-query-language.md)
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)
* [Documentação de referência da API de consulta](/rest/api/digital-twins/dataplane/query/querytwins)

Você pode encontrar limites relacionados à consulta do Azure digital gêmeos em [*referência: limites de serviço*](reference-service-limits.md).