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
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118261"
---
# <a name="overview-query-data-in-storage"></a>Visão geral: Consultar dados no armazenamento

Esta seção contém consultas de exemplo que você pode usar para experimentar o recurso SQL sob demanda (versão prévia) no Azure Synapse Analytics.
Atualmente, os formatos de arquivo compatíveis são:  
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

Sua primeira etapa é **criar um banco de dados** em que você executará as consultas. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados para ler os dados nestas amostras.

> [!NOTE]
> Os bancos de dados são usados apenas para exibição de metadados, não para dados propriamente ditos.  Anote o nome do banco de dados que você usa, você precisará dele mais tarde.

```sql
CREATE DATABASE mydbname;
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

### <a name="sample-query"></a>Exemplo de consulta

A última etapa da validação é executar a seguinte consulta:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
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
