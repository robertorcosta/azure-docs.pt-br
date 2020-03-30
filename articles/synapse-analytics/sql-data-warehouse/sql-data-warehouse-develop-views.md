---
title: Usando visualizações T-SQL
description: Dicas para usar exibições T-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções.
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
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351529"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Exibições no Azure SQL Data Warehouse
As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução. 

O Azure SQL Data Warehouse suporta visualizações padrão e materializadas. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas. As visualizações encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de computação para que não haja necessidade de reescrever consultas.

## <a name="standard-view"></a>Exibição padrão
Uma exibição padrão calcula seus dados cada vez que a exibição é usada. Não há dados armazenados no disco. As pessoas normalmente usam visualizações padrão como uma ferramenta que ajuda a organizar os objetos e consultas lógicas em um banco de dados. Para usar uma exibição padrão, uma consulta precisa fazer referência direta a ela. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql).

Os modos de exibição no SQL Data Warehouse são armazenados somente como metadados. Consequentemente, as opções a seguir não estão disponíveis:
* Não há opção de associação de esquema
* Tabelas base não podem ser atualizadas por meio da exibição
* Exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* não há exibições indexadas no SQL Data Warehouse

As visualizações padrão podem ser utilizadas para impor o desempenho otimizado entre as tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados. Outro benefício de uma exibição pode ser forçar uma dica de consulta ou junção específica. Usar exibições dessa maneira garante que as junções sempre sejam executadas de forma ideal, evitando a necessidade dos usuários lembrarem a construção correta de suas junções.

## <a name="materialized-view"></a>Exibição materializada
Uma visão materializada pré-computa, armazena e mantém seus dados no Azure SQL Data Warehouse como uma tabela. Não há necessidade de recomputação cada vez que uma visão materializada é usada. À medida que os dados são carregados em tabelas base, o Azure SQL Data Warehouse atualiza sincronizadamente as visualizações materializadas.  O otimizador de consulta usa automaticamente visualizações materializadas implantadas para melhorar o desempenho da consulta, mesmo que as visualizações não sejam referenciadas na consulta.  As consultas que mais se beneficiam de visualizações materializadas são consultas complexas (normalmente consultas com agregações e agregações) em grandes tabelas que produzem pequenos conjuntos de resultados.  

Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [CRIAR EXIBIÇÃO MATERIALIZADA COMO SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Para consultar a orientação de ajuste, verifique [a sintonia de desempenho com visualizações materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Exemplo
Um padrão de aplicativo comum é recriar tabelas usando CREATE TABLE AS SELECT (CTAS) seguido por um objeto de renomeação do padrão durante o carregamento dos dados.  O exemplo a seguir adiciona novos registros de data para uma dimensão de data. Observe como uma nova tabela, DimDate_New, é criada pela primeira vez e renomeada para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
No entanto, essa abordagem pode resultar em tabelas que aparecem e desaparecem da exibição do usuário, bem como nas mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Fornecendo acesso a dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes. Essa camada fornece uma experiência de usuário consistente enquanto garante que os designers do data warehouse podem desenvolver o modelo de dados. Capacidade para desenvolver as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


