---
title: 'Tutorial: Compilar um modelo de clustering no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na segunda parte desta série de tutoriais de três partes, você criará um modelo K-means para executar o clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia).
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
ms.openlocfilehash: 0a73a2bc3fa76c945cf699133a41b38a9983a234
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345804"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na parte dois desta série de tutoriais de três partes, você criará um modelo K-Means em R para executar o clustering. Na próxima parte desta série, você implantará esse modelo em um banco de dados SQL com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Definir o número de clusters para um algoritmo K-means
> * Executar clustering
> * Analisar os resultados

Na [primeira parte](sql-database-tutorial-clustering-model-prepare-data.md), você aprendeu a preparar os dados de um banco de dados SQL do Azure para executar o clustering.

Na [terceira parte](sql-database-tutorial-clustering-model-deploy.md), você aprenderá a criar um procedimento armazenado em um banco de dados SQL do Azure que pode executar o clustering em R com base em dados novos.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A parte dois deste tutorial presume que você concluiu a [**parte um**](sql-database-tutorial-clustering-model-prepare-data.md) e seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de clusters

Para colocar os dados do cliente em um cluster, você usará o algoritmo de clustering **K-means**, uma das maneiras mais simples e mais conhecidas de agrupar dados.
Você pode ler mais sobre o K-means em [Um guia completo para o algoritmo de clustering K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

O algoritmo aceita duas entradas: Os dados em si e um número predefinido "*k*" que representa o número de clusters a serem gerados.
A saída é *k* clusters com os dados de entrada particionados entre os clusters.

Para determinar o número de clusters para o algoritmo a ser usado, use um gráfico da soma dos quadrados nos grupos, por número de clusters extraídos. O número correto de clusters a ser usado está na curva ou no "cotovelo" do gráfico.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Gráfico de cotovelo](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Com base no gráfico, parece que *k = 4* seria um bom valor para testar. Esse valor *k* agrupará os clientes em quatro clusters.

## <a name="perform-clustering"></a>Executar clustering

No script R a seguir, você usará a função **rxKmeans**, que é a função K-means no pacote RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analisar os resultados

Agora que você já concluiu o clustering usando o K-means, a próxima etapa é analisar o resultado e ver se você pode encontrar todas as informações acionáveis.

O objeto **cluster** contém os resultados do clustering K-means.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

As quatro médias de cluster são fornecidas usando as variáveis definidas na [parte um](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = taxa de devolução de pedidos (número total de pedidos parcialmente ou totalmente retornados em relação o número total de pedidos)
* *itemsRatio* = taxa de devolução de itens (número total de itens retornados em relação ao número de itens comprados)
* *monetaryRatio* = taxa de valores devolvidos (valor monetário total dos itens retornados em relação ao valor comprado)
* *frequência* = frequência de devoluções

A mineração de dados com K-means geralmente requer análise adicional dos resultados e mais etapas para entender melhor cada cluster, mas pode fornecer bons clientes potenciais.
Veja algumas maneiras de interpretar esses resultados:

* O cluster 1 (o maior cluster) parece ser um grupo de clientes que não são ativos (todos os valores são zero).
* O cluster 3 parece ser um grupo que se destaca em termos de comportamento de devolução.

## <a name="clean-up-resources"></a>Limpar os recursos

***Se você não pretende continuar com este tutorial***, exclua o banco de dados tpcxbb_1gb do servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , insira **tpcxbb_1gb** e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb**.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na parte dois desta série de tutoriais, você concluiu estas etapas:

* Definir o número de clusters para um algoritmo K-means
* Executar clustering
* Analisar os resultados

Para implantar o modelo de machine learning que você criou, execute a parte três da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implantar um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-tutorial-clustering-model-deploy.md)