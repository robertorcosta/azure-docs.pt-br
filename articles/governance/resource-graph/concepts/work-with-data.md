---
title: Trabalhar com grandes conjuntos de dados
description: Entenda como obter, formatar, página e pular registros em grandes conjuntos de dados enquanto trabalha com o Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064737"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhando com grandes conjuntos de dados de recurso do Azure

O Azure Resource Graph foi projetado para trabalhar com informações sobre os recursos em seu ambiente do Azure e obtê-las. O Resource Graph facilita a obtenção esses dados rapidamente, mesmo ao consultar milhares de registros. O Resource Graph tem várias opções para trabalhar com esses grandes conjuntos de dados.

Para obter orientações sobre como trabalhar com consultas em alta frequência, consulte [Orientação para solicitações estranguladas](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por padrão, o Resource Graph limita qualquer consulta a retornar apenas **100** registros. Esse controle protege o usuário e o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Esse evento geralmente ocorre quando um cliente está experimentando consultas para localizar e filtrar recursos da maneira que atende às suas necessidades específicas. Esse controle é diferente de usar os operadores de linguagem de programação [top](/azure/kusto/query/topoperator) ou [limit](/azure/kusto/query/limitoperator) do Azure Data Explorer para limitar os resultados.

> [!NOTE]
> Ao usar **o First,** recomenda-se encomendar os resultados `asc` por `desc`pelo menos uma coluna com ou . Sem classificação, os resultados devolvidos são aleatórios e não repetíveis.

O limite padrão pode ser substituído por meio de todos os métodos de interação com o Resource Graph. Os exemplos a seguir mostram como alterar o limite de tamanho do conjunto de dados para _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), o controle é **$top** e faz parte de **QueryRequestOptions**.

O controle que for _mais restritivo_ prevalecerá. Por exemplo, se sua consulta usa os operadores **top** ou **limit** e resultaria em mais registros do que **First**, o máximo de registros retornado seria igual a **First**. Da mesma forma, se **top** ou **limit** for menor do que **First**, o conjunto de registros retornado será o menor valor configurado por **top** ou **limit**.

**First** atualmente tem um valor máximo permitido de _5000_.

## <a name="skipping-records"></a>Ignorando os registros

A próxima opção para trabalhar com grandes conjuntos de dados é o controle **Skip**. Esse controle permite que sua consulta pule ou ignore o número definido de registros antes de retornar os resultados. **Skip** é útil para consultas que classificam os resultados de uma maneira significativa, em que a intenção é chegar a registros em algum lugar no meio do conjunto de resultados. Se os resultados necessários estão no final do conjunto de dados retornado, é mais eficiente usar uma configuração de classificação diferente e, em vez disso, recuperar os resultados da parte superior do conjunto de dados.

> [!NOTE]
> Ao usar **Skip,** recomenda-se encomendar os resultados por `asc` pelo `desc`menos uma coluna com ou . Sem classificação, os resultados devolvidos são aleatórios e não repetíveis.

Os exemplos a seguir mostram como ignorar os primeiros _10_ registros em que uma consulta resultaria, começando em vez disso o conjunto de resultados pelo 11º registro:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), o controle é **$skip** e faz parte de **QueryRequestOptions**.

## <a name="paging-results"></a>Resultados da paginação

Quando for necessário quebrar um resultado definido em conjuntos menores de registros para processamento ou porque um conjunto de resultados excederia o valor máximo permitido de _1000_ registros retornados, use paginação. A **Resposta de Consulta de** [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) fornece valores para indicar que um conjunto de resultados foi quebrado: **resultTruncated** e **$skipToken**.
**resultTruncated** é um valor booliano que informa ao consumidor se existem registros adicionais não retornados na resposta. Essa condição também pode ser identificada quando a propriedade **count** é menor do que a propriedade **totalRecords**. **totalRecords** define quantos registros correspondem à consulta.

 **resultTruncated** é **verdadeiro** quando a paginação é `id` desativada ou não é possível devido a nenhuma coluna ou quando há menos recursos disponíveis do que uma consulta está solicitando. Quando **o resultadoTruncated** é **verdadeiro,** a **propriedade $skipToken** não está definida.

Os exemplos a seguir mostram como **pular** os primeiros 3000 registros e retornar os **primeiros** 1000 registros depois que esses registros pularam com o Azure CLI e o Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A consulta precisa **projetar** o campo **id** para que a paginação funcione. Se estiver faltando na consulta, a resposta não incluirá o **$skipToken**.

Para ver um exemplo, confira a [Consulta de próxima página](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) na documentação da API REST.

## <a name="formatting-results"></a>Resultados de formatação

Os resultados de uma consulta do Gráfico de Recursos são fornecidos em dois formatos, _Table_ e _ObjectArray_. O formato é configurado com o parâmetro **resultFormat** como parte das opções de solicitação. O formato _Tabela_ é o valor padrão para **resultFormat**.

Os resultados do Azure CLI são fornecidos em JSON por padrão. Os resultados no Azure PowerShell são um **PSCustomObject** por padrão, mas `ConvertTo-Json` eles podem ser rapidamente convertidos para JSON usando o cmdlet. Para outros SDKs, os resultados da consulta podem ser configurados para produzir o formato _ObjectArray._

### <a name="format---table"></a>Formato - Tabela

O formato padrão, _Table_, retorna resulta em um formato JSON projetado para destacar o desenho da coluna e os valores de linha das propriedades retornadas pela consulta. Esse formato se assemelha muito aos dados definidos em uma tabela ou planilha estruturada com as colunas identificadas primeiro e, em seguida, cada linha representando dados alinhados a essas colunas.

Aqui está uma amostra de um resultado de consulta com a formatação _da tabela:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Formato - ObjectArray

O formato _ObjectArray_ também retorna os resultados em um formato JSON. No entanto, esse design se alinha à relação de par de chaves/valor comum no JSON, onde os dados da coluna e da linha são combinados em grupos de matriz.

Aqui está uma amostra de um resultado de consulta com a formatação _ObjectArray:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Aqui estão alguns exemplos de configuração de formato de **resultado** para usar o formato _ObjectArray:_

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso em [consultas iniciais](../samples/starter.md).
- Veja usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).