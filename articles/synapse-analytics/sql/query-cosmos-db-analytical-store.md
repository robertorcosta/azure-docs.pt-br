---
title: Consultar dados de Azure Cosmos DB usando o pool SQL sem servidor no link Synapse do Azure (versão prévia)
description: Neste artigo, você aprenderá a consultar Azure Cosmos DB usando o pool SQL sem servidor no link Synapse do Azure (versão prévia).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9f57d435134bffbb8e7576adffeacb92bf687124
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310302"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Consultar dados de Azure Cosmos DB com o pool SQL sem servidor no link Synapse do Azure (versão prévia)

O pool SQL sem servidor Synapse permite analisar dados em seus contêineres de Azure Cosmos DB que são habilitados com o [link Synapse do Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) quase em tempo real sem afetar o desempenho de suas cargas de trabalho transacionais. Ele oferece uma sintaxe de T-SQL familiar para consultar dados do [armazenamento analítico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e conectividade integrada a uma ampla gama de ferramentas de consulta de BI e ad hoc por meio da interface T-SQL.

Para consultar Azure Cosmos DB, a área de superfície de [seleção](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) completa é suportada por meio da função [OPENROWSET](develop-openrowset.md) , incluindo a maioria dos [operadores e funções SQL](overview-features.md). Você também pode armazenar os resultados da consulta que lê dados de Azure Cosmos DB juntamente com os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Storage usando [criar tabela externa como SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool). Atualmente, não é possível armazenar os resultados da consulta do pool SQL sem servidor para Azure Cosmos DB usando CETAS. 

Neste artigo, você aprenderá a escrever uma consulta com o pool SQL sem servidor que consultará dados de contêineres de Azure Cosmos DB que estão com o link Synapse habilitado. Em seguida, você pode saber mais sobre como criar exibições de pool de SQL sem servidor em Azure Cosmos DB contêineres e conectá-las a modelos de Power BI [neste tutorial.](./tutorial-data-analyst.md) 

> [!IMPORTANT]
> Este tutorial usa um contêiner com [Azure Cosmos DB esquema bem definido](../../cosmos-db/analytical-store-introduction.md#schema-representation). A experiência de consulta que o pool SQL sem servidor fornece para [Azure Cosmos DB esquema de fidelidade total](#full-fidelity-schema) é um comportamento temporário que será alterado com base nos comentários de visualização. Não confie no esquema do conjunto de resultados da `OPENROWSET` função sem `WITH` a cláusula que lê dados de um contêiner com o esquema de fidelidade total, pois a experiência de consulta pode ser alterada e alinhada com um esquema bem definido. Poste seus comentários no [Fórum de comentários do Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) ou entre em contato com a [equipe do produto Synapse link](mailto:cosmosdbsynapselink@microsoft.com) para fornecer comentários.

## <a name="overview"></a>Visão geral

Para dar suporte à consulta e análise de dados no repositório analítico Azure Cosmos DB, o pool SQL sem servidor usa a seguinte `OPENROWSET` sintaxe:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A cadeia de conexão Azure Cosmos DB especifica o nome da conta de Azure Cosmos DB, o nome do banco de dados, a chave mestra da conta do banco de dados e um nome de região opcional para `OPENROWSET` funcionar. 

> [!IMPORTANT]
> Certifique-se de usar alias após `OPENROWSET` . Há um [problema conhecido](#known-issues) que causa o problema de conexão com o ponto de extremidade SQL sem servidor Synapse se você não especificar o alias após a `OPENROWSET` função.

A cadeia de conexão tem o seguinte formato:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

O nome do contêiner de Azure Cosmos DB é especificado sem aspas na `OPENROWSET` sintaxe. Se o nome do contêiner tiver caracteres especiais (por exemplo, um traço '-'), o nome deverá ser encapsulado dentro do `[]` (colchetes) na `OPENROWSET` sintaxe.

> [!NOTE]
> O pool SQL sem servidor não dá suporte à consulta Azure Cosmos DB repositório transacional.

## <a name="sample-data-set"></a>Conjunto de dados de exemplo

Os exemplos neste artigo baseiam-se nos dados do [Centro Europeu para prevenção de doenças e controle (ECDC) COVID-19 casos](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research DataSet (cabo-19), doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Você pode ver a licença e a estrutura de dados nessas páginas e baixar dados de exemplo para conjuntos de dados [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Para acompanhar este artigo mostrando como consultar dados de Cosmos DB com o pool SQL sem servidor, certifique-se de criar os seguintes recursos:
* Uma conta de banco de dados Azure Cosmos DB que esteja com o [link Synapse habilitado](../../cosmos-db/configure-synapse-link.md)
* Um banco de dados Azure Cosmos DB chamado `covid`
* Dois contêineres Azure Cosmos DB chamados `EcdcCases` e `Cord19` com os conjuntos de dados de exemplo acima carregados.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explorar Azure Cosmos DB dados com inferência de esquema automática

A maneira mais fácil de explorar dados no Azure Cosmos DB é aproveitando a capacidade de inferência automática de esquemas. Ao omitir a `WITH` cláusula da `OPENROWSET` instrução, você pode instruir o pool SQL sem servidor a detectar automaticamente (inferir) o esquema do repositório analítico do Azure Cosmos DB contêiner.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
No exemplo acima, estamos instruindo o pool SQL sem servidor a se conectar ao `covid` banco de dados em Azure Cosmos DB conta `MyCosmosDbAccount` autenticada usando a chave Azure Cosmos dB (fictícia no exemplo acima). Em seguida, estamos acessando o `EcdcCases` repositório analítico do contêiner na `West US 2` região. Como não há projeção de propriedades específicas, a `OPENROWSET` função retornará todas as propriedades dos itens de Azure Cosmos DB. 

Supondo que os itens no contêiner Cosmos DB têm `date_rep` `cases` Propriedades, e, `geo_id` os resultados dessa consulta são mostrados na tabela a seguir:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Se você precisar explorar dados do outro contêiner no mesmo banco de Azure Cosmos DB, poderá usar a mesma cadeia de conexão e o contêiner de referência necessário como terceiro parâmetro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

Embora a capacidade de inferência de esquema automática no `OPENROWSET` forneça um querience simples e fácil de usar, seus cenários de negócios podem exigir que você especifique explicitamente o esquema para propriedades relevantes somente leitura dos dados de Azure Cosmos DB.

`OPENROWSET` permite especificar explicitamente quais propriedades você deseja ler dos dados no contêiner e especificar seus tipos de dados. Vamos imaginar que importamos alguns dados do [conjunto de dados ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) com a seguinte estrutura para Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Esses documentos JSON simples no Azure Cosmos DB podem ser representados como um conjunto de linhas e colunas no SQL Synapse. `OPENROWSET` a função permite especificar um subconjunto de propriedades que você deseja ler e os tipos de coluna exatos na `WITH` cláusula:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

O resultado dessa consulta pode ser semelhante a:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Examine as [regras para mapeamentos de tipo SQL](#azure-cosmos-db-to-sql-type-mappings) no final do artigo para obter mais informações sobre os tipos SQL que devem ser usados para Azure Cosmos DB valor.

## <a name="querying-nested-objects-and-arrays"></a>Consultando objetos aninhados e matrizes

Azure Cosmos DB permite que você represente modelos de dados mais complexos, compondo-os como objetos ou matrizes aninhados. O recurso AutoSync do link do Synapse para Azure Cosmos DB gerencia a representação do esquema no repositório analítico, que inclui o tratamento de tipos de dados aninhados, permitindo uma consulta avançada de um pool SQL sem servidor.

Por exemplo, o conjunto de dados de [cabo-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) tem documentos JSON seguindo a seguinte estrutura:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Os objetos aninhados e as matrizes no Azure Cosmos DB são representados como cadeias de caracteres JSON no resultado da consulta quando a `OPENROWSET` função os lê. Uma opção para ler os valores desses tipos complexos como colunas SQL é usar funções SQL JSON:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

O resultado dessa consulta pode ser semelhante a:

| título | authors | first_autor_name |
| --- | --- | --- |
| Informações complementares de um epidemi de eco... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Como opção alternativa, você também pode especificar os caminhos para valores aninhados nos objetos ao usar a `WITH` cláusula:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Saiba mais sobre a análise [de tipos de dados complexos no link do Synapse](../how-to-analyze-complex-schema.md) e [estruturas aninhadas no pool SQL sem servidor](query-parquet-nested-types.md).

> [!IMPORTANT]
> Se você vir caracteres inesperados em seu texto como `MÃƒÂ©lade` em vez de `Mélade` , o agrupamento do banco de dados não será definido como agrupamento [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Altere o agrupamento do banco de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para algum agrupamento UTF8 usando alguma instrução SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flattening-nested-arrays"></a>Mesclando matrizes aninhadas

Azure Cosmos DB dados podem ter submatrizes aninhadas como a matriz do autor do conjunto de dados [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Em alguns casos, talvez seja necessário "unir" as propriedades do item superior (metadados) com todos os elementos da matriz (autores). O pool SQL sem servidor permite mesclar estruturas aninhadas aplicando a `OPENJSON` função na matriz aninhada:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

O resultado dessa consulta pode ser semelhante a:

| título | authors | first | last | afilia |
| --- | --- | --- | --- | --- |
| Informações complementares de um epidemi de eco... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informações complementares de um epidemi de eco... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | quatro # |`{"laboratory":"","institution":"U…` | 
| Informações complementares de um epidemi de eco... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informações complementares de um epidemi de eco... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se você vir caracteres inesperados em seu texto como `MÃƒÂ©lade` em vez de `Mélade` , o agrupamento do banco de dados não será definido como agrupamento [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Altere o agrupamento do banco de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para algum agrupamento UTF8 usando alguma instrução SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB para mapeamentos de tipo SQL

É importante primeiro observar que, embora Azure Cosmos DB armazenamento transacional seja independente de esquema, o armazenamento analítico é esquematizados para otimizar o desempenho de consultas analíticas. Com o recurso AutoSync do link do Synapse, o Azure Cosmos DB gerencia a representação do esquema no repositório analítico, que inclui o tratamento de tipos de dados aninhados. Como o pool SQL sem servidor consulta o repositório analítico, é importante entender como mapear Azure Cosmos DB tipos de dados de entrada para tipos de dados SQL.

Azure Cosmos DB contas da API do SQL (Core) dão suporte a tipos de propriedade JSON de número, Cadeia de caracteres, booliano, nulo, objeto aninhado ou matriz. Você precisaria escolher tipos SQL que correspondam a esses tipos de JSON se estiver usando a `WITH` cláusula in `OPENROWSET` . Veja abaixo os tipos de coluna SQL que devem ser usados para diferentes tipos de propriedade no Azure Cosmos DB.

| Tipo de propriedade Azure Cosmos DB | Tipo de coluna SQL |
| --- | --- |
| Booliano | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (agrupamento de banco de dados UTF8) |
| Data e hora (cadeia de caracteres formatada em ISO) | varchar (30) |
| Data e hora (carimbo de hora do UNIX) | bigint |
| Nulo | `any SQL type` 
| Objeto ou matriz aninhada | varchar (max) (agrupamento de banco de dados UTF8), serializado como texto JSON |

## <a name="full-fidelity-schema"></a>Esquema de fidelidade total

Azure Cosmos DB esquema de fidelidade total registra os valores e seus melhores tipos de correspondência para cada propriedade em um contêiner.
`OPENROWSET` a função em um contêiner com esquema de fidelidade total fornece o tipo e o valor real em cada célula. Vamos supor que a consulta a seguir leia os itens de um contêiner com esquema de fidelidade total:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

O resultado dessa consulta retornará tipos e valores formatados como texto JSON: 

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"data": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"data": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"data": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"data": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"data": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"data": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

Para cada valor, você pode ver o tipo identificado em Cosmos DB item de contêiner. A maioria dos valores da `date_rep` propriedade contém `date` valores, mas alguns deles são armazenados incorretamente como cadeias de caracteres no cosmos DB. O esquema de fidelidade completa retornará valores digitados corretamente `date` e valores formatados incorretamente `string` .
O número de casos é uma informação armazenada como `int32` valor, mas há um valor que é inserido como número decimal. Este valor tem `float64` tipo. Se houver alguns valores que excedam o maior `int32` número, eles serão armazenados como `int64` tipo. Todos os `geo_id` valores neste exemplo são armazenados como `string` tipos.

> [!IMPORTANT]
> `OPENROWSET` function sem `WITH` cláusula expõe os valores com os tipos esperados e os valores com tipos inseridos incorretamente. Essa funcidade foi projetada para exploração de dados e não para relatórios. Não analise valores JSON retornados dessa função para criar relatórios e use a [cláusula WITH](#querying-items-with-full-fidelity-schema) explícita para criar seus relatórios.
> Você deve limpar os valores que têm tipos incorretos no contêiner Azure Cosmos DB para aplicar a corect em um repositório analítico de fidelidade total. 

Para consultar Azure Cosmos DB contas do tipo de API do Mongo DB, você pode saber mais sobre a representação de esquema de fidelidade total no repositório analítico e os nomes de propriedade estendida a serem usados [aqui](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="querying-items-with-full-fidelity-schema"></a>Consultando itens com o esquema de fidelidade total

Ao consultar o esquema de fidelidade completa, você precisa especificar explicitamente o tipo SQL e o tipo de propriedade esperado Cosmos DB na `WITH` cláusula in. Não use `OPENROWSET` `WITH` a cláusula WITH nos relatórios porque o formato do conjunto de resultados pode ser alterado na visualização com base nos comentários.

No exemplo a seguir, vamos pressupor que `string` é o tipo correto para a `geo_id` propriedade e o `int32` tipo correto para a `cases` Propriedade:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Os valores de `geo_id` e `cases` que têm outros tipos serão retornados como `NULL` valores. Essa consulta fará referência somente ao `cases` com o tipo especificado na expressão ( `cases.int32` ).

Se você tiver valores com outros tipos ( `cases.int64` , `cases.float64` ) que não podem ser limpos no contêiner Cosmos DB, precisará referenciá-los explicitamente na `WITH` cláusula e combinar os resultados. A consulta a seguir agrega ambos `int32` , `int64` e `float64` armazenados na `cases` coluna:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Neste exemplo, o número de casos é armazenado como `int32` , `int64` ou `float64` valores e todos os valores devem ser extraídos para calcular o número de casos por país. 

## <a name="known-issues"></a>Problemas conhecidos

- O alias **deve** ser especificado após `OPENROWSET` a função (por exemplo, `OPENROWSET (...) AS function_alias` ). A omissão do alias pode causar problemas de conexão e o ponto de extremidade SQL sem servidor Synapse pode estar temporariamente indisponível. Esse problema será resolvido em 2020 de novembro.
- A experiência de consulta que o pool SQL sem servidor fornece para [Azure Cosmos DB esquema de fidelidade total](#full-fidelity-schema) é um comportamento temporário que será alterado com base nos comentários de visualização. Não confie no esquema que `OPENROWSET` a função sem a `WITH` cláusula fornece durante a visualização pública, pois a experiência de consulta pode estar alinhada ao esquema bem definido com base nos comentários dos clientes. Contate a [equipe do produto Synapse link](mailto:cosmosdbsynapselink@microsoft.com) para fornecer comentários.

Possíveis erros e ações de solução de problemas estão listados na tabela a seguir:

| Erro | Causa raiz |
| --- | --- |
| Erros de sintaxe:<br/> -Sintaxe incorreta próxima a ' OPENROWSET '<br/> - `...` Não é uma opção de provedor de OPENROWSET em massa reconhecida.<br/> -Sintaxe incorreta próxima a `...` | Possíveis causas raiz<br/> -Não está usando ' CosmosDB ' como primeiro parâmetro,<br/> -Usando literal de cadeia de caracteres em vez de identificador no terceiro parâmetro,<br/> -Não especificando o terceiro parâmetro (nome do contêiner) |
| Ocorreu um erro na cadeia de conexão CosmosDB | -A conta, o banco de dados e a chave não foram especificados <br/> -Há alguma opção na cadeia de conexão que não é reconhecida.<br/> -Ponto e vírgula `;` é colocado no final da cadeia de conexão |
| Falha ao resolver o caminho CosmosDB com o erro ' nome da conta incorreto ' ou ' nome do banco de dados incorreto ' | O nome da conta, o nome do banco de dados ou o contêiner especificado não pode ser encontrado ou o armazenamento analítico não foi habilitado para a coleção especificada|
| Falha ao resolver o caminho CosmosDB com o erro ' valor de segredo incorreto ' ou ' segredo é nulo ou vazio ' | A chave de conta não é válida ou está ausente. |
| `column name`A coluna do tipo `type name` não é compatível com o tipo de dados externo`type name` | O tipo de coluna especificado na `WITH` cláusula in não corresponde ao tipo no contêiner Cosmos DB. Tente alterar o tipo de coluna conforme descrito na seção [Azure Cosmos DB para mapeamentos de tipo SQL](#azure-cosmos-db-to-sql-type-mappings) ou tipo de uso `VARCHAR` . |
| A coluna contém `NULL` valores em todas as células. | Nome de coluna ou expressão de caminho possivelmente incorreto na `WITH` cláusula. O nome da coluna (ou expressão de caminho após o tipo de coluna) na `WITH` cláusula deve corresponder a um nome de propriedade na coleção de Cosmos DB. A comparação diferencia **maiúsculas de minúsculas**  (por exemplo, `productCode` e `ProductCode` são propriedades diferentes). |

Você pode relatar sugestões e problemas na [página de comentários do Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- [Usar o Power BI e o pool de SQL sem servidor com o link Synapse do Azure](../../cosmos-db/synapse-link-power-bi.md)
- [Como criar e usar modos de exibição no pool SQL sem servidor](create-use-views.md) 
- [Tutorial sobre como criar exibições de pool de SQL sem servidor em Azure Cosmos DB e conectá-las a modelos de Power BI por meio do DirectQuery](./tutorial-data-analyst.md)
