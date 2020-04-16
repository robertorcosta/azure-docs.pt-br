---
title: Esquemas definidos pelo usuário dentro do Synapse SQL
description: Nas seções abaixo, você encontrará várias dicas para usar esquemas definidos pelo usuário T-SQL para desenvolver soluções com a capacidade Synapse SQL do Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428700"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Esquemas definidos pelo usuário dentro do Synapse SQL

Nas seções abaixo, você encontrará várias dicas para usar esquemas definidos pelo usuário T-SQL para desenvolver soluções dentro do Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

A arquitetura de análise tradicional geralmente usa bancos de dados separados para criar limites de aplicativos com base na carga de trabalho, domínio ou segurança. Por exemplo, uma infra-estrutura tradicional de análise do SQL Server pode incluir um banco de dados de estágio, um banco de dados de análise e bancos de dados do data mart. Nesta topologia, cada banco de dados funciona como um limite de carga de trabalho e segurança na arquitetura.

Em vez disso, o Synapse SQL executa toda a carga de trabalho de análise em um banco de dados. A adesão ao banco de dados não é permitida. O Synapse SQL espera que todas as tabelas usadas pelo armazém sejam armazenadas dentro de um banco de dados.

> [!NOTE]
> Os pools SQL não suportam consultas de banco de dados cruzados de qualquer tipo. Consequentemente, implementações de análise que alavancam esse padrão precisarão ser revistas. SQL on-demand (preview) suporta consultas cruzadas de banco de dados.

## <a name="user-defined-schema-recommendations"></a>Recomendações de esquema sustivas definidas pelo usuário

Incluem-se recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário:

- Use um banco de dados para executar toda a sua carga de trabalho de análise.
- Consolide seu ambiente de análise existente para usar um banco de dados.
- Utilize **esquemas definidos pelo usuário** para fornecer o limite implementado anteriormente usando bancos de dados.

Se os esquemas definidos pelo usuário não tiverem sido usados anteriormente, então você tem uma ficha limpa. Use o nome do banco de dados antigo como base para seus esquemas definidos pelo usuário no banco de dados Synapse SQL.

Se os esquemas já foram usados, então você tem algumas opções:

- Remover os nomes de esquema herdados e começar do zero
- Mantenha os nomes do esquema legado pré-pendente do nome do esquema legado para o nome da tabela
- Mantenha os nomes do esquema legado implementando visualizações sobre a mesa em um esquema extra, que recria a antiga estrutura do esquema.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a escolha mais atraente. As visualizações são lidas apenas no Synapse SQL. Qualquer modificação nos dados ou na tabela precisariam ser executados em relação à tabela base. A opção 3 também apresenta uma camada de modos de exibição em seu sistema. Você pode querer dar um pouco mais de pensamento se você já estiver usando visualizações em sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos

Implementar esquemas definidos pelo usuário com base em nomes de banco de dados.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes do esquema legado pré-pendente-los para o nome da tabela. Usar esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes do esquema legado usando visualizações.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Qualquer mudança na estratégia de esquema requer uma revisão do modelo de segurança do banco de dados. Em muitos casos, você pode ser capaz de simplificar o modelo de segurança atribuindo permissões no nível do esquema.

Se forem necessárias permissões mais granulares, você pode usar funções de banco de dados. Para obter mais informações sobre as funções do banco de dados, consulte o [artigo Gerenciar as funções do banco de dados e os usuários.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte visão geral do [desenvolvimento do Synapse SQL](develop-overview.md).
