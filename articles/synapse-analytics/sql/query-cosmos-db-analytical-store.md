---
title: Consultar dados de Azure Cosmos DB usando o SQL Server sem o link Synapse do Azure (versão prévia)
description: Neste artigo, você aprenderá a consultar Azure Cosmos DB usando o SQL sob demanda no link Synapse do Azure (versão prévia).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9d36cee1ebf9f58991db6fd40fd6aa9422546b61
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710746"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Consultar dados de Azure Cosmos DB usando o SQL Server sem o link Synapse do Azure (versão prévia)

O Synapse SQL Serverless (anteriormente SQL sob demanda) permite que você analise dados em seus contêineres de Azure Cosmos DB que estão habilitados com o [link Synapse do Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) quase em tempo real sem afetar o desempenho de suas cargas de trabalho transacionais. Ele oferece uma sintaxe de T-SQL familiar para consultar dados do [armazenamento analítico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e conectividade integrada a uma ampla gama de ferramentas de consulta de BI e ad hoc por meio da interface T-SQL.

> [!NOTE]
> O suporte para consultar Azure Cosmos DB repositório analítico com o SQL Serverless está atualmente em visualização restrita. 

Para consultar Azure Cosmos DB, a área de superfície de [seleção](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) completa é suportada por meio da função [OPENROWSET](develop-openrowset.md) , incluindo a maioria dos [operadores e funções SQL](overview-features.md). Você também pode armazenar os resultados da consulta que lê dados de Azure Cosmos DB juntamente com os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Storage usando [criar tabela externa como SELECT](develop-tables-cetas.md#cetas-in-sql-on-demand). Atualmente, não é possível armazenar os resultados da consulta sem SQL Server para Azure Cosmos DB usando [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

Neste artigo, você aprenderá a escrever uma consulta usando o SQL Server que irá consultar dados de contêineres de Azure Cosmos DB que estão com o link Synapse habilitado. Em seguida, você pode saber mais sobre como criar exibições sem SQL Server em Azure Cosmos DB contêineres e conectá-las a modelos de Power BI [neste tutorial.](./tutorial-data-analyst.md) 

## <a name="overview"></a>Visão geral

Para dar suporte à consulta e análise de dados no repositório analítico Azure Cosmos DB, o SQL Server usa a seguinte `OPENROWSET` sintaxe:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

A cadeia de conexão Azure Cosmos DB especifica o nome da conta de Azure Cosmos DB, o nome do banco de dados, a chave mestra da conta do banco de dados e um nome de região opcional para `OPENROWSET` funcionar. A cadeia de conexão tem o seguinte formato:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

O nome do contêiner de Azure Cosmos DB é especificado sem aspas na `OPENROWSET` sintaxe. Se o nome do contêiner tiver caracteres especiais (por exemplo, um traço '-'), o nome deverá ser encapsulado dentro do `[]` (colchetes) na `OPENROWSET` sintaxe.

> [!NOTE]
> O SQL Serverless não dá suporte à consulta Azure Cosmos DB armazenamento transacional.

## <a name="sample-data-set"></a>Conjunto de dados de exemplo

Os exemplos neste artigo baseiam-se nos dados do [Centro Europeu para prevenção de doenças e controle (ECDC) COVID-19 casos](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research DataSet (cabo-19), doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Você pode ver a licença e a estrutura de dados nessas páginas e baixar dados de exemplo para conjuntos de dados [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Para acompanhar este artigo mostrando como consultar dados de Cosmos DB com o SQL Server sem nenhum, certifique-se de criar os seguintes recursos:
* Uma conta de banco de dados Azure Cosmos DB que esteja com o [link Synapse habilitado](../../cosmos-db/configure-synapse-link.md)
* Um banco de dados Azure Cosmos DB chamado `covid`
* Dois contêineres Azure Cosmos DB chamados `EcdcCases` e `Cord19` com os conjuntos de dados de exemplo acima carregados.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explorar Azure Cosmos DB dados com inferência de esquema automática

A maneira mais fácil de explorar dados no Azure Cosmos DB é aproveitando a capacidade de inferência automática de esquemas. Ao omitir a `WITH` cláusula da `OPENROWSET` instrução, você pode instruir o SQL Server a detectar automaticamente (inferir) o esquema do repositório analítico do contêiner de Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
No exemplo acima, estamos instruindo o SQL Server a se conectar ao `covid` banco de dados em Azure Cosmos DB conta `MyCosmosDbAccount` autenticada usando a chave de Azure Cosmos dB (fictícia no exemplo acima). Em seguida, estamos acessando o `EcdcCases` repositório analítico do contêiner na `West US 2` região. Como não há projeção de propriedades específicas, a `OPENROWSET` função retornará todas as propriedades dos itens de Azure Cosmos DB.

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

Azure Cosmos DB permite que você represente modelos de dados mais complexos, compondo-os como objetos ou matrizes aninhados. O recurso AutoSync do link do Synapse para Azure Cosmos DB gerencia a representação do esquema no repositório analítico, que inclui o tratamento de tipos de dados aninhados, permitindo uma consulta avançada do SQL Server.

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

Saiba mais sobre a análise [de tipos de dados complexos em Synapse link](../how-to-analyze-complex-schema.md) e [estruturas aninhadas no SQL Server](query-parquet-nested-types.md).

> [!IMPORTANT]
> Se você vir caracteres inesperados em seu texto como `MÃƒÂ©lade` em vez de `Mélade` , o agrupamento do banco de dados não será definido como agrupamento [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Altere o agrupamento do banco de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para algum agrupamento UTF8 usando alguma instrução SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Mesclando matrizes aninhadas

Azure Cosmos DB dados podem ter submatrizes aninhadas, como a matriz do autor do conjunto de dados [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

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

Em alguns casos, talvez seja necessário "unir" as propriedades do item superior (metadados) com todos os elementos da matriz (autores). O SQL Serverless permite mesclar estruturas aninhadas aplicando `OPENJSON` a função na matriz aninhada:

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

É importante primeiro observar que, embora Azure Cosmos DB armazenamento transacional seja independente de esquema, o armazenamento analítico é esquematizados para otimizar o desempenho de consultas analíticas. Com o recurso AutoSync do link do Synapse, o Azure Cosmos DB gerencia a representação do esquema no repositório analítico, que inclui o tratamento de tipos de dados aninhados. Como o SQL Server não consulta o repositório analítico, é importante entender como mapear Azure Cosmos DB tipos de dados de entrada para tipos de dados SQL.

Azure Cosmos DB contas da API do SQL (Core) dão suporte a tipos de propriedade JSON de número, Cadeia de caracteres, booliano, nulo, objeto aninhado ou matriz. Você precisaria escolher tipos SQL que correspondam a esses tipos de JSON se estiver usando a `WITH` cláusula in `OPENROWSET` . Veja abaixo os tipos de coluna SQL que devem ser usados para diferentes tipos de propriedade no Azure Cosmos DB.

| Tipo de propriedade Azure Cosmos DB | Tipo de coluna SQL |
| --- | --- |
| Booliano | bit |
| Integer | BIGINT |
| Decimal | FLOAT |
| String | varchar (agrupamento de banco de dados UTF8) |
| Data e hora (cadeia de caracteres formatada em ISO) | varchar (30) |
| Data e hora (carimbo de hora do UNIX) | BIGINT |
| Nulo | `any SQL type` 
| Objeto ou matriz aninhada | varchar (max) (agrupamento de banco de dados UTF8), serializado como texto JSON |

Para consultar Azure Cosmos DB contas do tipo de API do Mongo DB, você pode saber mais sobre a representação de esquema de fidelidade total no repositório analítico e os nomes de propriedade estendida a serem usados [aqui](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- [Como criar e usar modos de exibição no SQL sob demanda](create-use-views.md) 
- [Tutorial sobre a criação de exibições do SQL sob demanda em Azure Cosmos DB e conectá-las a modelos de Power BI por meio do DirectQuery](./tutorial-data-analyst.md)
