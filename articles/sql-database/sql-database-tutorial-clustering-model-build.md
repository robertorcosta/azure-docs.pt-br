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
ms.openlocfilehash: 9f16ebc5acff7bbccc9de28e2fab0d223c6e244b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640001"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na parte dois desta série de tutoriais de três partes, você criará um modelo K-Means em R para executar o clustering. Na próxima parte desta série, você implantará esse modelo em um banco de dados SQL com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Definir o número de clusters para um algoritmo K-means
> * Executar o clustering
> * Analisar os resultados

Na [primeira parte](sql-database-tutorial-clustering-model-prepare-data.md), você aprendeu a preparar os dados de um banco de dados SQL do Azure para executar o clustering.

Na [terceira parte](sql-database-tutorial-clustering-model-deploy.md), você aprenderá a criar um procedimento armazenado em um banco de dados SQL do Azure que pode executar o clustering em R com base em dados novos.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

* A parte dois deste tutorial presume que você concluiu a [**parte um**](sql-database-tutorial-clustering-model-prepare-data.md) e seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de clusters

Para agrupar os dados do cliente, você usará o algoritmo de clustering **K-means**, uma das maneiras mais simples e mais conhecidas de agrupar dados.
Leia mais sobre o K-means em [Um guia completo sobre o algoritmo de clustering K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

O algoritmo aceita duas entradas: Os próprios dados e um número predefinido "*k*", que representa o número de clusters a ser gerado.
A saída é *k* clusters com os dados de entrada particionados entre os clusters.

Para determinar o número de clusters para o algoritmo a ser usado, use um gráfico da soma dos quadrados dentro dos grupos pelo número de clusters extraídos. O número apropriado de clusters a ser usado está na curva ou no "ângulo" do gráfico.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Ângulo do gráfico](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Com base no gráfico, parece que *k = 4* é um bom valor para experimentar. Esse valor *k* agrupará os clientes em quatro clusters.

## <a name="perform-clustering"></a>Executar o clustering

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

As médias dos quatro clusters são fornecidas usando as variáveis definidas na [primeira parte](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = índice de pedidos devolvidos (o número total de pedidos parcialmente ou totalmente devolvidos versus o número total de pedidos)
* *itemsRatio* = índice de itens devolvidos (o número total de itens devolvidos versus o número de itens comprados)
* *monetaryRatio* = índice de valores devolvidos (o valor monetário total de itens devolvidos versus o valor comprado)
* *frequency* = frequência de devoluções

A mineração de dados usando o K-means geralmente exige uma análise adicional dos resultados e outras etapas para entender melhor cada cluster, mas pode fornecer alguns ótimos clientes potenciais.
Estas são duas maneiras de interpretar esses resultados:

* O cluster 1 (o maior cluster) parece ser um grupo de clientes que não são ativos (todos os valores são zero).
* O cluster 3 parece ser um grupo que se destaca em termos de comportamento de devoluções.

## <a name="clean-up-resources"></a>Limpar recursos

***Se você não pretende continuar com este tutorial***, exclua o banco de dados tpcxbb_1gb do servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , insira **tpcxbb_1gb** e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb**.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na segunda parte desta série de tutoriais, você concluiu estas etapas:

* Definir o número de clusters para um algoritmo K-means
* Executar o clustering
* Analisar os resultados

Para implantar o modelo de machine learning que você criou, execute a parte três da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implantar um modelo de clustering no R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-tutorial-clustering-model-deploy.md)