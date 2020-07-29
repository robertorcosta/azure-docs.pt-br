---
title: Consultar o grafo de gêmeos
titleSuffix: Azure Digital Twins
description: Consulte como consultar o grafo gêmeos do Azure digital para obter informações.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 93043874db6076b26d0fefe447db7acd83547442
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725577"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Consultar o grafo gêmeos do Azure digital

Este artigo é aprofundado sobre como usar a [linguagem de repositório de consultas gêmeos do Azure digital](concepts-query-language.md) para consultar o [grafo de entrelaçamento](concepts-twins-graph.md) para obter informações. Você executa consultas no grafo usando as [**APIs de consulta**](how-to-use-apis-sdks.md)do gêmeos digital do Azure.

## <a name="query-syntax"></a>Sintaxe de consulta

Aqui estão algumas consultas de exemplo que ilustram a estrutura da linguagem de consulta e executam possíveis operações de consulta.

Obter [gêmeos digital](concepts-twins-graph.md) por Propriedades (incluindo a ID e os metadados):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

Obter gêmeos digital por [modelo](concepts-models.md)
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> A ID de uma teledigital é consultada usando o campo de metadados `$dtId` .

## <a name="run-queries-with-an-api-call"></a>Executar consultas com uma chamada à API

Depois de decidir sobre uma cadeia de caracteres de consulta, execute-a fazendo uma chamada para a **API de consulta**.
O trecho de código a seguir ilustra essa chamada do aplicativo cliente:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Essa chamada retorna resultados da consulta na forma de um objeto QueryResult. 

As chamadas de consulta dão suporte à paginação. Aqui está um exemplo completo com tratamento de erro e paginação:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-based-on-relationships"></a>Consulta baseada em relações

Ao consultar com base em relações do gêmeos digital, o idioma do Azure digital gêmeos Repositório de Consultas tem uma sintaxe especial.

As relações são retiradas no escopo de consulta na `FROM` cláusula. Uma distinção importante das linguagens do tipo SQL "clássico" é que cada expressão nessa `FROM` cláusula não é uma tabela; em vez disso, a `FROM` cláusula expressa uma passagem de relacionamento entre entidades e é escrita com uma versão do gêmeos digital do Azure do `JOIN` . 

Lembre-se de que com os recursos do [modelo](concepts-models.md) de gêmeos digital do Azure, as relações não existem independentemente do gêmeos. Isso significa que a linguagem de Repositório de Consultas do Azure digital gêmeos `JOIN` é um pouco diferente do SQL geral `JOIN` , pois as relações aqui não podem ser consultadas de forma independente e devem estar vinculadas a um ".".
Para incorporar essa diferença, a palavra-chave `RELATED` é usada na `JOIN` cláusula para fazer referência a um conjunto de relações de entrelaçar. 

A seção a seguir fornece vários exemplos de como isso se parece.

> [!TIP]
> Conceitualmente, esse recurso imita a funcionalidade centrada no documento de CosmosDB, onde `JOIN` pode ser executado em objetos filho dentro de um documento. CosmosDB usa a `IN` palavra-chave para indicar que o deve `JOIN` ser iterado em elementos de matriz dentro do documento de contexto atual.

### <a name="relationship-based-query-examples"></a>Exemplos de consulta baseada em relação

Para obter um conjunto de resultados que inclui relações, use uma única `FROM` instrução seguida por N `JOIN` instruções, em que as `JOIN` instruções expressam relações no resultado de uma `FROM` instrução ou anterior `JOIN` .

Aqui está um exemplo de consulta baseada em relação. Esse trecho de código seleciona todas as gêmeos digitais com uma propriedade de *ID* de ' abc ' e todas as gêmeos digitais relacionadas a essas gêmeos digitais por meio de uma relação *Contains* . 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> O desenvolvedor não precisa correlacionar isso `JOIN` com um valor de chave na `WHERE` cláusula (ou especificar um valor de chave embutido com a `JOIN` definição). Essa correlação é calculada automaticamente pelo sistema, pois as próprias propriedades de relação identificam a entidade de destino.

### <a name="query-the-properties-of-a-relationship"></a>Consultar as propriedades de uma relação

Da mesma forma que o gêmeos digital tem propriedades descritas por meio de DTDL, as relações também podem ter propriedades. A linguagem Repositório de Consultas do Azure digital gêmeos permite a filtragem e a projeção de relações, atribuindo um alias à relação dentro da `JOIN` cláusula. 

Como exemplo, considere uma relação *servicedBy* que tem uma propriedade *reportedCondition* . Na consulta abaixo, essa relação recebe um alias de ' R ' para referenciar sua propriedade.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

No exemplo acima, observe como *reportedCondition* é uma propriedade da relação *servicedBy* em si (não de algumas propriedades digitais que têm uma relação de *servicedBy* ).

### <a name="query-limitations"></a>Limitações de consulta

Pode haver um atraso de até 10 segundos antes que as alterações em sua instância sejam refletidas em consultas. Por exemplo, se você concluir uma operação como criar ou excluir gêmeos com a API DigitalTwins, o resultado poderá não ser refletido imediatamente nas solicitações de API de consulta. Aguardar um curto período de tempo deve ser suficiente para resolver.

Há limitações adicionais no uso `JOIN` durante a visualização.
* Nenhuma subconsulta é suportada na `FROM` instrução.
* `OUTER JOIN`Não há suporte para semântica, o que significa que, se a relação tiver uma classificação igual a zero, a "linha" inteira será eliminada do conjunto de resultados de saída.
* Durante a visualização pública, a profundidade de percurso do grafo é restrita: apenas uma `JOIN` é permitida por consulta.
* A origem das `JOIN` operações é restrita: a consulta deve declarar o gêmeos onde a consulta começa.

## <a name="query-best-practices"></a>Práticas recomendadas de consulta

Abaixo estão algumas dicas para consultar com o Azure digital gêmeos.

* Considere o padrão de consulta durante a fase de design do modelo. Tente garantir que as relações que precisam ser respondidas em uma única consulta sejam modeladas como uma relação de nível único.
* Crie Propriedades de uma maneira que evite grandes conjuntos de resultados da passagem de grafo.
* Você pode reduzir significativamente o número de consultas necessárias criando uma matriz de gêmeos e consultando com o `IN` operador. Por exemplo, considere um cenário no qual os *edifícios* contêm *andares* e *andares* contêm *salas*. Para pesquisar salas em um edifício que estejam quentes, você pode:

    1. Localizar andares no edifício com base na `contains` relação
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Para localizar salas, em vez de considerar os andares um a um e executar uma `JOIN` consulta para localizar os quartos de cada um, você pode consultar com uma coleção dos andares no edifício (chamado *andar* na consulta abaixo).

        No aplicativo cliente:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        Em consulta:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Nomes de propriedade e valores diferenciam maiúsculas de minúsculas, portanto, tome cuidado para usar os nomes exatos definidos nos modelos. Se os nomes de propriedade forem digitados incorretamente ou em maiúsculas e minúsculas, o conjunto de resultados estará vazio sem erros retornados.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [APIs e SDKs do gêmeos digital do Azure](how-to-use-apis-sdks.md), incluindo a API de consulta que é usada para executar as consultas deste artigo.
