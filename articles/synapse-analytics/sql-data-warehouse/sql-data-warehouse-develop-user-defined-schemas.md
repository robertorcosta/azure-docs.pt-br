---
title: Usando esquemas definidos pelo usuário
description: Dicas para usar esquemas definidos pelo usuário T-SQL para desenvolver soluções no pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633463"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Esquemas definidos pelo usuário no pool Synapse SQL
Este artigo se concentra em fornecer várias dicas para usar esquemas definidos pelo usuário T-SQL para desenvolver soluções no pool Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

Os data warehouses tradicionais normalmente usam bancos de dados separados para criar limites de aplicativo com base na carga de trabalho, domínio ou segurança. 

Como exemplo, um data warehouse tradicional do SQL Server pode incluir um banco de dados de estágio, um banco de dados e alguns bancos de dados do data mart. Nesta topologia, cada banco de dados funciona como um limite de carga de trabalho e segurança na arquitetura.

Em contraste, o pool SQL executa toda a carga de trabalho do data warehouse dentro de um banco de dados. A adesão ao banco de dados não é permitida. O pool SQL espera que todas as tabelas usadas pelo armazém sejam armazenadas dentro de um banco de dados.

> [!NOTE]
> O pool SQL não suporta consultas de banco de dados cruzados de qualquer tipo. Consequentemente, implementações de data warehouse que utilizam esse padrão precisarão ser revisadas.
> 
> 

## <a name="recommendations"></a>Recomendações
O que se segue são recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário:

- Use um banco de dados de pool SQL para executar toda a sua carga de trabalho do data warehouse.
- Consolide seu ambiente de data warehouse existente para usar um banco de dados sql pool.
- Utilize **esquemas definidos pelo usuário** para fornecer o limite implementado anteriormente usando bancos de dados.

Se os esquemas definidos pelo usuário não tiverem sido usados anteriormente, então você terá uma lousa limpa. Use o nome do banco de dados antigo como base para seus esquemas definidos pelo usuário no banco de dados do pool SQL.

Se os esquemas já foram usados, então você tem algumas opções:

- Remova os nomes do esquema legado e comece de novo.
- Retenha os nomes do esquema legado, pré-pendente do nome do esquema legado para o nome da tabela.
- Manter os nomes de esquema herdados implementando exibições sobre a tabela em um esquema extra para recriar a estrutura do esquema antigo.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a opção mais atraente. No entanto, o detalhe é traiçoeiro. As visualizações são lidas apenas no pool SQL. Qualquer modificação nos dados ou na tabela precisariam ser executados em relação à tabela base. A opção 3 também apresenta uma camada de modos de exibição em seu sistema. Talvez você queira pensar um pouco sobre isso se já estiver usando modos de exibição em sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar esquemas definidos pelo usuário com base em nomes de banco de dados:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha nomes de esquemalegado sumido antes deles para o nome da tabela. Use esquemas para o limite da carga de trabalho:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha nomes de esquemalegado usando visualizações:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
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
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você pode ser capaz de simplificar o modelo de segurança atribuindo permissões no nível do esquema. Se forem necessárias permissões mais granulares, então você pode usar funções de banco de dados.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

