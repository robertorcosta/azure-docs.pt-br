---
title: Como usar o SQL sob demanda (versão prévia)
description: Neste início rápido, você verá e aprenderá como é fácil consultar vários tipos de arquivos usando o SQL sob demanda (versão prévia).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457375"
---
# <a name="quickstart-using-sql-on-demand"></a>Início Rápido: Como usar o SQL sob demanda

O Synapse SQL sob demanda (versão prévia) é um serviço de consulta sem servidor que permite executar as consultas SQL nos seus arquivos colocados no Armazenamento do Azure. Neste início rápido, você aprenderá a consultar vários tipos de arquivos usando o SQL sob demanda.

Há suporte para os seguintes tipos de arquivo: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Pré-requisitos

Escolha um cliente SQL para emitir consultas:

- O [Azure Synapse Studio](quickstart-synapse-studio.md) é uma ferramenta da Web que pode ser usada para procurar arquivos no armazenamento e criar uma consulta SQL.
- O [Azure Data Studio](sql/get-started-azure-data-studio.md) é uma ferramenta de cliente que permite executar consultas SQL e notebooks no banco de dados sob demanda.
- O [SQL Server Management Studio](sql/get-started-ssms.md) é uma ferramenta de cliente que permite executar consultas SQL no banco de dados sob demanda.

Parâmetros para o início rápido:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do SQL sob demanda    | Usado como o nome do servidor                                   |
| Região do ponto de extremidade de serviço do SQL sob demanda     | Usada para determinar qual armazenamento será usado nas amostras |
| Nome de usuário e senha para acesso de ponto de extremidade | Usado para acessar o ponto de extremidade                               |
| O banco de dados usado para criar exibições         | Banco de dados usado como ponto de partida nas amostras       |

## <a name="first-time-setup"></a>Configuração inicial

Antes de usar as amostras:

- Criar um banco de dados para as exibições (caso você queira usar exibições)
- Criar credenciais a serem usadas pelo SQL sob demanda para acessar arquivos no armazenamento

### <a name="create-database"></a>Criar banco de dados

Crie um banco de dados próprio para fins de demonstração. Esse é o banco de dados no qual você criará as exibições. Use-o nas consultas de exemplo deste artigo.

> [!NOTE]
> Os bancos de dados são usados apenas para os metadados de exibição, não para os dados reais.
>
> Anote o nome do banco de dados usado para uso posterior no Início Rápido.

Use a seguinte consulta, alterando `mydbname` para um nome de sua escolha:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Criar credenciais

Para executar consultas usando o SQL sob demanda, crie credenciais para uso pelo SQL sob demanda para acessar arquivos no armazenamento.

> [!NOTE]
> Observe que você precisará criar credenciais para acesso à conta de armazenamento. Embora o SQL sob demanda possa acessar armazenamentos de diferentes regiões, ter o armazenamento e o workspace do Azure Synapse na mesma região proporcionará uma experiência com melhor desempenho.

Modifique o seguinte snippet de código para criar credenciais para contêineres CSV, JSON e Parquet:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Como consultar arquivos CSV

A seguinte imagem é uma visualização do arquivo a ser consultado:

![Dez primeiras linhas do arquivo CSV sem cabeçalho e uma nova linha no estilo do Windows.](./sql/media/query-single-csv-file/population.png)

A seguinte consulta mostra como ler um arquivo CSV que não contém uma linha de cabeçalho, com uma nova linha no estilo do Windows e colunas delimitadas por vírgula:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Você pode especificar o esquema no momento da compilação da consulta.
Para obter mais exemplos, confira como [consultar um arquivo CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Como consultar arquivos Parquet

A amostra a seguir apresenta as funcionalidades de inferência automática de esquemas para consulta de arquivos Parquet. Ela retorna o número de linhas em setembro de 2017 sem especificar o esquema.

> [!NOTE]
> Você não precisa especificar colunas na cláusula `OPENROWSET WITH` ao ler arquivos Parquet. Nesse caso, o SQL sob demanda utiliza os metadados do arquivo Parquet e associa as colunas por nome.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Encontre mais informações sobre [como consultar arquivos Parquet](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>Como consultar arquivos JSON

### <a name="json-sample-file"></a>Arquivo de exemplo JSON

Os arquivos são armazenados no contêiner *json* e na pasta *books* e contêm uma só entrada de livro com a seguinte estrutura:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="querying-json-files"></a>Como consultar arquivos JSON

A seguinte consulta mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalares (título, editor) de um livro com o título *Probabilística e métodos estatísticos em criptologia, uma introdução com artigos selecionados*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Estamos lendo o arquivo JSON inteiro como uma só linha/coluna, de modo que FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR sejam definidos como 0x0b, porque não esperamos encontrá-lo no arquivo.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para começar com os seguintes artigos de Início Rápido:

- [Consultar um arquivo CSV individual](sql/query-single-csv-file.md)
- [Consultar pastas e vários arquivos CSV](sql/query-folders-multiple-csv-files.md)
- [Consultar arquivos específicos](sql/query-specific-files.md)
- [Consultar arquivos Parquet](sql/query-parquet-files.md)
- [Consultar tipos aninhados Parquet](sql/query-parquet-nested-types.md)
- [Consultar arquivos JSON](sql/query-json-files.md)
- [Como criar e usar exibições](sql/create-use-views.md)
- [Como criar e usar tabelas externas](sql/create-use-external-tables.md)
- [Persistir o resultado da consulta no Armazenamento do Azure](sql/create-external-table-as-select.md)

Avance para o próximo artigo para saber como consultar um arquivo CSV individual.
> [!div class="nextstepaction"]
> [Consultar um arquivo CSV individual](sql/query-single-csv-file.md)
