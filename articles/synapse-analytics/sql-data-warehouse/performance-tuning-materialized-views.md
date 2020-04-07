---
title: Ajuste de desempenho com exibições materializadas
description: Recomendações e considerações que você deve saber como você usa visualizações materializadas para melhorar o desempenho de sua consulta.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743177"
---
# <a name="performance-tuning-with-materialized-views"></a>Ajuste de desempenho com exibições materializadas

As visualizações materializadas no pool Synapse SQL fornecem um método de baixa manutenção para consultas analíticas complexas para obter desempenho rápido sem qualquer alteração de consulta. Este artigo discute as orientações gerais sobre o uso de visões materializadas.

As visualizações materializadas no Azure SQL Data Warehouse fornecem um método de baixa manutenção para consultas analíticas complexas para obter desempenho rápido sem qualquer alteração de consulta. Este artigo discute as orientações gerais sobre o uso de visões materializadas.

## <a name="materialized-views-vs-standard-views"></a>Visualizações materializadas versus visualizações padrão

O pool SQL suporta visualizações padrão e materializadas.  Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.  As visualizações encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de computação para que não haja necessidade de reescrever consultas.  

Uma exibição padrão calcula seus dados cada vez que a exibição é usada.  Não há dados armazenados no disco. As pessoas normalmente usam visualizações padrão como uma ferramenta que ajuda a organizar os objetos e consultas lógicas em um banco de dados.  Para usar uma exibição padrão, uma consulta precisa fazer referência direta a ela.

Uma visão materializada pré-computa, armazena e mantém seus dados no pool SQL como uma tabela.  Não há necessidade de recomputação cada vez que uma visão materializada é usada.  É por isso que consultas que usam todos ou subconjuntos dos dados em visualizações materializadas podem obter desempenho mais rápido.  Melhor ainda, as consultas podem usar uma visualização materializada sem fazer referência direta a ela, então não há necessidade de alterar o código do aplicativo.  

A maioria dos requisitos em uma exibição padrão ainda se aplicam a uma exibição materializada. Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [CRIAR EXIBIÇÃO MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Comparação                     | Exibir                                         | Exibição Materializada
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Exibir definição                 | Armazenado em pool SQL.              | Armazenado em pool SQL.
|Exibir conteúdo                    | Gerado cada vez que a exibição é usada.   | Pré-processado e armazenado no pool SQL durante a criação da exibição. Atualizado à medida que os dados são adicionados às tabelas subjacentes.
|Atualização dedados                    | Sempre atualizado                               | Sempre atualizado
|Velocidade para recuperar dados de exibição de consultas complexas     | Lenta                                         | Rápido  
|Armazenamento extra                   | Não                                           | Sim
|Sintaxe                          | CREATE VIEW                                  | CRIAR VISUALIZAÇÃO MATERIALIZADA COMO SELEÇÃO

## <a name="benefits-of-using-materialized-views"></a>Benefícios do uso de visões materializadas

Uma visão materializada adequadamente projetada pode fornecer os seguintes benefícios:

- Reduza o tempo de execução para consultas complexas com JOINs e funções agregadas. Quanto mais complexa a consulta, maior o potencial de economia de tempo de execução. O maior benefício é obtido quando o custo de computação de uma consulta é alto e o conjunto de dados resultante é pequeno.  
- O otimizador no pool SQL pode usar automaticamente visualizações materializadas implantadas para melhorar os planos de execução de consultas.  Esse processo é transparente para os usuários que fornecem desempenho de consulta mais rápido e não requer consultas para fazer referência direta às visualizações materializadas.
- Requerem baixa manutenção nas vistas.  Todas as alterações incrementais de dados das tabelas base são automaticamente adicionadas às exibições materializadas de forma síncrona.  Este design permite que a consulta de visualizações materializadas retorne os mesmos dados que consultar diretamente as tabelas base.
- Os dados em uma exibição materializada podem ser distribuídos de forma diferente das tabelas base.  
- Os dados em visualizações materializadas recebem os mesmos benefícios de alta disponibilidade e resiliência que os dados em tabelas regulares.  

As visualizações materializadas implementadas no pool SQL também fornecem os seguintes benefícios adicionais:

Comparando com outros provedores de data warehouse, as visualizações materializadas implementadas no Azure SQL Data Warehouse também fornecem os seguintes benefícios adicionais:

- Atualização automática e síncrona de dados com alterações de dados em tabelas base. Não é necessária nenhuma ação do usuário.
- Suporte amplo de função agregada. Consulte [CRIAR VISUALIZAÇÃO MATERIALIZADA COMO SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- O suporte para recomendação de exibição materializada específica de consulta.  Consulte [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Cenários comuns  

As visualizações materializadas são normalmente usadas nos seguintes cenários:

**Necessidade de melhorar o desempenho de consultas analíticas complexas contra grandes dados em tamanho**

Consultas analíticas complexas normalmente usam mais funções de agregação e adesões de tabela, causando operações mais pesadas de computação, como embaralhos e se junta à execução de consultas.  É por isso que essas consultas demoram mais para serem concluídas, especialmente em mesas grandes.  

Os usuários podem criar visualizações materializadas para os dados retornados dos cálculos comuns das consultas, de modo que não há necessidade de recomputação quando esses dados são necessários por consultas, permitindo menor custo de computação e resposta de consulta mais rápida.

**Precisa de desempenho mais rápido sem alterações de consulta ou mínimo**

As alterações de esquema e consulta nos pools SQL são normalmente mantidas ao mínimo para suportar operações e relatórios regulares de ETL.  As pessoas podem usar visualizações materializadas para afinação de desempenho de consulta, se o custo incorrido pelas visualizações pode ser compensado pelo ganho no desempenho da consulta.

Em comparação com outras opções de ajuste, como o dimensionamento e o gerenciamento de estatísticas, é uma mudança de produção muito menos impactante para criar e manter uma visão materializada e seu potencial ganho de desempenho também é maior.

- Criar ou manter visualizações materializadas não afeta as consultas que correm contra as tabelas base.
- O otimizador de consulta pode usar automaticamente as visualizações materializadas implantadas sem referência de exibição direta em uma consulta. Esse recurso reduz a necessidade de alteração de consulta na sintonia de desempenho.

**Precisa de diferentes estratégias de distribuição de dados para um desempenho mais rápido de consulta**

O pool SQL é um sistema de processamento massivamente paralelo distribuído (MPP).   Os dados em uma tabela de bilhar SQL são distribuídos em 60 nós usando uma das três [estratégias de distribuição](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin ou replicada).  

A distribuição de dados é especificada no tempo de criação da tabela e permanece inalterada até que a tabela seja retirada. A exibição materializada sendo uma tabela virtual em disco suporta distribuições de hash e round_robin dados.  Os usuários podem escolher uma distribuição de dados diferente das tabelas base, mas ideal para o desempenho das consultas que mais usam as visualizações.  

## <a name="design-guidance"></a>Diretrizes de design

Aqui está a orientação geral sobre o uso de visualizações materializadas para melhorar o desempenho da consulta:

**Design para sua carga de trabalho**

Antes de começar a criar visualizações materializadas, é importante ter uma compreensão profunda da sua carga de trabalho em termos de padrões de consulta, importância, frequência e o tamanho dos dados resultantes.  

Os usuários podem executar o EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> para as visualizações materializadas recomendadas pelo otimizador de consulta.  Uma vez que essas recomendações são específicas de consulta, uma exibição materializada que beneficia uma única consulta pode não ser ideal para outras consultas na mesma carga de trabalho.  

Avalie essas recomendações com suas necessidades de carga de trabalho em mente.  As visões materializadas ideais são aquelas que beneficiam o desempenho da carga horária.  

**Esteja atento à troca entre consultas mais rápidas e o custo**

Para cada visualização materializada, há um custo de armazenamento de dados e um custo para manter a visualização.  À medida que os dados mudam nas tabelas base, o tamanho da visão materializada aumenta e sua estrutura física também muda.  Para evitar a degradação do desempenho da consulta, cada visualização materializada é mantida separadamente pelo mecanismo de pool SQL.  

A carga de trabalho de manutenção aumenta quando o número de visualizações materializadas e as alterações na tabela de base aumentam.   Os usuários devem verificar se o custo incorrido de todas as visualizações materializadas pode ser compensado pelo ganho de desempenho da consulta.  

Você pode executar esta consulta para a lista de exibição materializada em um banco de dados:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opções para reduzir o número de visualizações materializadas:

- Identifique conjuntos de dados comuns frequentemente usados pelas consultas complexas em sua carga de trabalho.  Crie visualizações materializadas para armazenar esses conjuntos de dados para que o otimizador possa usá-los como blocos de construção ao criar planos de execução.  

- Solte as vistas materializadas que têm baixo uso ou não são mais necessárias.  Uma visualização materializada desativada não é mantida, mas ainda incorre no custo de armazenamento.  

- Combine visualizações materializadas criadas nas mesmas tabelas base ou similares, mesmo que seus dados não se sobreponham.  O penteamento de visualizações materializadas pode resultar em uma visão maior em tamanho do que a soma das vistas separadas, no entanto, o custo de manutenção da visualização deve reduzir.  Por exemplo:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nem toda sintonia de desempenho requer alteração de consulta**

O otimizador de pool SQL pode usar automaticamente visualizações materializadas implantadas para melhorar o desempenho da consulta.  Esse suporte é aplicado de forma transparente a consultas que não fazem referência às visualizações e consultas que usam agregados não suportados na criação de visualizações materializadas.  Nenhuma alteração de consulta é necessária. Você pode verificar o plano de execução estimado de uma consulta para confirmar se uma exibição materializada é usada.  

**Monitorar visualizações materializadas**

Uma exibição materializada é armazenada na piscina SQL como uma tabela com índice de columnstore clustered (CCI).  A leitura de dados de uma exibição materializada inclui a varredura dos segmentos de índice cci e a aplicação de quaisquer alterações incrementais das tabelas base. Quando o número de alterações incrementais é muito alto, resolver uma consulta de uma exibição materializada pode levar mais tempo do que consultar diretamente as tabelas base.  

Para evitar a degradação do desempenho da consulta, é uma boa prática executar [o DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para monitorar o overhead_ratio da vista (total_rows / máx(1, base_view_row)).  Os usuários devem reconstruir a visão materializada se sua overhead_ratio for muito alta.

**Visualização materializada e cache conjunto de resultados**

Esses dois recursos são introduzidos no pool SQL ao mesmo tempo para afinação do desempenho da consulta.  O cache conjunto de resultados é usado para obter alta concorrência e resposta rápida de consultas repetitivas contra dados estáticos.  

Para usar o resultado armazenado em cache, a forma da consulta de solicitação de cache deve coincidir com a consulta que produziu o cache.  Além disso, o resultado em cache deve ser aplicado a toda a consulta.  

As visualizações materializadas permitem alterações de dados nas tabelas base.  Os dados em visualizações materializadas podem ser aplicados a um pedaço de uma consulta.  Esse suporte permite que as mesmas visualizações materializadas sejam usadas por diferentes consultas que compartilham alguma computação para um desempenho mais rápido.

## <a name="example"></a>Exemplo

Este exemplo usa uma consulta semelhante ao TPCDS que encontra clientes que gastam mais dinheiro via catálogo do que em lojas, identificam os clientes preferidos e seu país de origem.   A consulta envolve selecionar os registros TOP 100 do SINDICATO de três declarações sub-SELECT envolvendo Soma() e GRUPO BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Verifique o plano de execução estimado da consulta.  São 18 embaralhados e 17 aderes às operações, que levam mais tempo para serem executadas. Agora vamos criar uma exibição materializada para cada uma das três instruções sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Verifique novamente o plano de execução da consulta original.  Agora, o número de adesões muda de 17 para 5 e não há mais embaralhar.  Clique no ícone de operação Filtro no plano, sua lista de saída mostra que os dados são lidos a partir das exibições materializadas em vez de tabelas base.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Com visualizações materializadas, a mesma consulta é executada muito mais rápido sem qualquer alteração de código.  

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte visão geral do [desenvolvimento do pool SqL da Synapse](sql-data-warehouse-overview-develop.md).
