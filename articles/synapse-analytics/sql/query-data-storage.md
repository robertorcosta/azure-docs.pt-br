---
title: Visão geral – Consultar dados no armazenamento usando o SQL sob demanda (versão prévia)
description: Esta seção contém consultas de exemplo que você pode usar para experimentar o recurso SQL sob demanda (versão prévia) no Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116240"
---
# <a name="overview-query-data-in-storage"></a>Visão geral: Consultar dados no armazenamento

Esta seção contém consultas de exemplo que você pode usar para experimentar o recurso SQL sob demanda (versão prévia) no Azure Synapse Analytics.
Os arquivos compatíveis no momento são: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas de que você precisa para emitir consultas:

- Cliente SQL de sua escolha:
    - Azure Synapse Studio (versão prévia)
    - Azure Data Studio
    - SQL Server Management Studio

Além disso, os parâmetros são os seguintes:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do SQL sob demanda    | Será usado como o nome do servidor.                                   |
| Região do ponto de extremidade de serviço do SQL sob demanda     | Será usada para determinar o armazenamento usado nos exemplos. |
| Nome de usuário e senha para acesso de ponto de extremidade | Será usado para acessar o ponto de extremidade.                               |
| O banco de dados que você usará para criar exibições     | Esse banco de dados será usado como ponto de partida para os exemplos.       |

## <a name="first-time-setup"></a>Configuração inicial

Antes de usar os exemplos incluídos posteriormente neste artigo, você tem duas etapas:

- Criar um banco de dados para as exibições (caso você queira usar exibições)
- Criar credenciais a serem usadas pelo SQL sob demanda para acessar os arquivos no armazenamento

### <a name="create-database"></a>Criar banco de dados

Você precisa de um banco de dados para criar exibições. Você usará esse banco de dados para algumas das consultas de exemplo nesta documentação.

> [!NOTE]
> Os bancos de dados são usados apenas para exibição de metadados, não para dados propriamente ditos.  Anote o nome do banco de dados que você usa, você precisará dele mais tarde.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Criar credenciais

Você precisa criar credenciais para poder executar consultas. Esta credencial será usada pelo serviço de SQL sob demanda para acessar os arquivos no armazenamento.

> [!NOTE]
> Para executar com êxito as instruções desta seção, você precisa usar o token SAS.
>
> Para começar a usar tokens SAS, você precisa descartar o UserIdentity, que é explicado no [artigo](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through) a seguir.
>
> O SQL sob demanda sempre usa por padrão a passagem do AAD.

Para obter mais informações sobre como gerenciar o controle de acesso de armazenamento, confira este [link](develop-storage-files-storage-access-control.md).

Para criar credenciais para contêineres CSV, JSON e Parquet, execute o código abaixo:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Dados de demonstração fornecidos

Os dados de demonstração contêm os seguintes conjuntos de dados:

- Táxi de Nova York – Registros de viagem de táxi amarelo – Parte do conjunto de dados públicos de Nova York
  - Formato CSV
  - Formato Parquet
- Conjunto de dados de população
  - Formato CSV
- Arquivos Parquet de exemplo com colunas aninhadas
  - Formato Parquet
- JSON de livros
  - Formato JSON

| Caminho da pasta                                                  | Descrição                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Pasta pai para dados no formato CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Pastas com arquivos de dados de população em formatos CSV diferentes. |
| /csv/taxi/                                                   | Pasta com arquivos de dados públicos de Nova York no formato CSV              |
| /parquet/                                                    | Pasta pai para dados no formato Parquet                     |
| /parquet/taxi                                                | Arquivos de dados públicos de Nova York no formato Parquet, particionados por ano e mês, usando o esquema de particionamento do Hive/Hadoop. |
| /parquet/nested/                                             | Arquivos Parquet de exemplo com colunas aninhadas                     |
| /json/                                                       | Pasta pai para dados no formato JSON                        |
| /json/books/                                                 | Arquivos JSON com os dados de livros                                   |

## <a name="validation"></a>Validação

Execute as três consultas a seguir e verifique se as credenciais foram criadas corretamente.

> [!NOTE]
> Todos os URIs nas consultas de exemplo usam uma conta de armazenamento localizada na região do Azure Norte da Europa. Verifique-se você criou a credencial apropriada. Execute a consulta abaixo e verifique se a conta de armazenamento está listada.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Se você não encontrar a credencial apropriada, confira [Configuração inicial](#first-time-setup).

### <a name="sample-query"></a>Exemplo de consulta

A última etapa da validação é executar a seguinte consulta:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

A consulta acima deve retornar este número: **8945574**.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para continuar com os seguintes artigos de instruções:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)

- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)

- [Consultar arquivos específicos](query-specific-files.md)

- [Consultar arquivos Parquet](query-parquet-files.md)

- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)

- [Consultar arquivos JSON](query-json-files.md)

- [Criar e usar exibições](create-use-views.md)
