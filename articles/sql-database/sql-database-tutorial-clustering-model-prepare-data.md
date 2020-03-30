---
title: 'Tutorial: Preparar dados para executar o clustering no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série de tutoriais de três partes, você preparará os dados de um banco de dados SQL do Azure para executar o clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia).
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
ms.openlocfilehash: 2241b69e36e3b17475dba115b8d2ae94fe2189a7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345839"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para executar o clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na primeira parte desta série de tutoriais com três partes, você importará e preparará os dados de um Banco de Dados SQL do Azure usando o R. Mais adiante nesta série, você usará esses dados para treinar e implantar um modelo de clustering no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

O *clustering* pode ser explicado como organizador de dados em grupos, nos quais os membros de um grupo são semelhantes de algum modo.
Você usará o algoritmo **K-Means** para executar o clustering de clientes em um conjunto de dados de compras e devoluções de produtos. Ao realizar o clustering de clientes, você pode concentrar seus esforços de marketing com mais eficiência, direcionando-os a grupos específicos.
O clustering de K-Means é um algoritmo de *aprendizado não supervisionado* que procura padrões em dados com base em semelhanças.

Na primeira e na segunda parte desta série, você desenvolverá alguns scripts do R no RStudio para preparar os dados e treinar um modelo de machine learning. Em seguida, na terceira parte, você executará esses scripts do R em um banco de dados SQL usando os procedimentos armazenados.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um banco de dados de exemplo para um Banco de Dados SQL do Azure
> * Clientes separados em dimensões diferentes usando o R
> * Carregar os dados do Banco de Dados SQL do Azure em um dataframe do R

Na [segunda parte](sql-database-tutorial-clustering-model-build.md), você aprenderá a criar e treinar um modelo de clustering K-Means no R.

Na [terceira parte](sql-database-tutorial-clustering-model-deploy.md), você aprenderá a criar um procedimento armazenado em um banco de dados SQL do Azure que pode executar o clustering em R com base em dados novos.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – Caso você não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor do Banco de Dados SQL do Azure com os Serviços do Machine Learning habilitados – Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

* Pacote de RevoScaleR – Confira [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para ver opções de instalação local do pacote.

* IDE do R – Este tutorial usa o [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL – Este tutorial presume que você está usando o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar o banco de dados de exemplo

O conjunto de dados de exemplo usado neste tutorial foi salvo em um arquivo de backup de banco de dados **.bacpac** para você baixar e usar. Esse conjunto de dados é derivado do conjunto de dados [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp), fornecido pela [TPC (Transaction Processing Performance Council)](http://www.tpc.org/default.asp).

1. Baixe o arquivo [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Siga as instruções em [Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import) usando estes detalhes:

   * Importe do arquivo **tpcxbb_1gb.bacpac** que você baixou
   * Durante a versão prévia pública, escolha a configuração **Gen5/vCore** para o novo banco de dados
   * Nomeie o novo banco de dados "tpcxbb_1gb"

## <a name="separate-customers"></a>Separar os clientes

Crie um novo arquivo RScript no RStudio e execute o script a seguir.
Na consulta SQL, você está separando clientes nas seguintes dimensões:

* **orderRatio** = taxa de devolução de pedidos (número total de pedidos parcialmente ou totalmente retornados em relação o número total de pedidos)
* **itemsRatio** = taxa de devolução de itens (número total de itens retornados em relação ao número de itens comprados)
* **monetaryRatio** = taxa de valores devolvidos (valor monetário total dos itens retornados em relação ao valor comprado)
* **frequência** = frequência de devoluções

Na função **paste**, substitua **Servidor**, **UID** e **PWD** por suas próprias informações de conexão.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Carregar os dados em um quadro de dados

Agora, use o script a seguir para retornar os resultados da consulta para um quadro de dados do R usando a função **rxSqlServerData**.
Como parte do processo, você definirá o tipo das colunas selecionadas (usando colClasses) para garantir que os tipos sejam corretamente transferidos para o R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Você deverá ver resultados semelhantes aos seguintes.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Limpar os recursos

***Se você não pretende continuar com este tutorial***, exclua o banco de dados tpcxbb_1gb do servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , insira **tpcxbb_1gb** e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb**.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na parte um desta série de tutoriais, você concluiu estas etapas:

* Importar um banco de dados de exemplo para um Banco de Dados SQL do Azure
* Clientes separados em dimensões diferentes usando o R
* Carregar os dados do Banco de Dados SQL do Azure em um dataframe do R

Para criar um modelo de machine learning que usa esses dados do cliente, siga a segunda parte desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-tutorial-clustering-model-build.md)
