---
title: 'Tutorial: Implantar um modelo de clustering no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte desta série de tutoriais de três partes, você implantará um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ms.openlocfilehash: d67f007ac91d4830557a2cae646698b130b02314
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345797"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implantar um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na terceira parte desta série de tutoriais de três partes, você implantará um modelo de clustering desenvolvido no R em um banco de dados SQL com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Você criará um procedimento armazenado com um script R inserido que executa o clustering. Como seu modelo é executado no Banco de Dados SQL do Azure, ele pode ser facilmente treinado nos dados armazenados no banco de dados.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar o procedimento armazenado que gera o modelo
> * Executar clustering no Banco de Dados SQL
> * Usar as informações de clustering

Na [primeira parte](sql-database-tutorial-clustering-model-prepare-data.md), você aprendeu a preparar os dados de um banco de dados SQL do Azure para executar o clustering.

Na [segunda parte](sql-database-tutorial-clustering-model-build.md), você aprendeu a criar e treinar um modelo de clustering K-Means no R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Na parte 3 deste tutorial, assumimos que você concluiu a [**parte 1**](sql-database-tutorial-clustering-model-prepare-data.md) e a [**parte 2**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar o procedimento armazenado que gera o modelo

Execute o seguinte script T-SQL para criar o procedimento armazenado. O procedimento recria as etapas que você desenvolveu na primeira e na segunda parte desta série de tutoriais:

* classificar clientes com base em seu histórico de compras e devoluções e
* gerar quatro clusters de clientes usando um algoritmo K-means

O procedimento armazena os mapeamentos de cluster do cliente resultantes na tabela de banco de dados **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Executar clustering no Banco de Dados SQL

Agora que você criou o procedimento armazenado, execute o script a seguir para executar o clustering.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Verifique se ele funciona e se, de fato, temos a lista de clientes e seus mapeamentos de cluster.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Usar as informações de clustering

Como você armazenou o procedimento de clustering no banco de dados, ele pode realizar o clustering com eficiência em relação aos dados do cliente armazenados no mesmo banco de dados. Você pode executar o procedimento sempre que os dados do cliente são atualizados e usar as informações de clustering atualizadas.

Suponha que você deseje enviar um email promocional para os clientes no cluster 3, o grupo que tem o comportamento mais ativo de devoluções (veja como os quatro clusters foram descritos na [segunda parte](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). O código a seguir seleciona os endereços de email de clientes no cluster 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Você pode alterar o valor **r.cluster** para retornar os endereços de email dos clientes em outros clusters.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, exclua o banco de dados tpcxbb_1gb do servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , insira **tpcxbb_1gb** e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb**.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na parte três desta série de tutoriais, você concluiu estas etapas:

* Criar o procedimento armazenado que gera o modelo
* Executar clustering no Banco de Dados SQL
* Usar as informações de clustering

Para saber mais sobre como usar o R nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia), confira:

* [Tutorial: Preparar dados para treinar um modelo preditivo no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
* [Trabalhar usando dados do R e do SQL nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)
* [Adicionar um pacote R aos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-add-r-packages.md)
