---
title: 'Tutorial: introdução à análise de dados com o SQL sem servidor'
description: Neste tutorial, você aprenderá a analisar dados com o SQL sob demanda usando dados localizados em bancos de dados do Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 8d26a03a8b61850dc17bc4efff5f8ca12dfca191
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300217"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analisar dados com o SQL sob demanda

Neste tutorial, você aprenderá a analisar dados com o SQL sem servidor por meio de um pool de SQL sob demanda usando dados localizados em bancos de dados do Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Analisar dados de táxis de Nova York no armazenamento de blobs usando o pool de SQL sob demanda

1. No hub **Dados** em **Vinculado**, clique com o botão direito do mouse em **Armazenamento de Blobs do Azure > Conjuntos de Dados de Exemplo > nyc_tlc_yellow** e selecione **SELECIONAR AS 100 PRIMEIRAS LINHAS**
1. Isso criará um script SQL com o seguinte código:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Clique em **Executar**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analisar dados de táxi de NYC em bancos de dados do Spark usando SQL sob demanda

As tabelas em bancos de dados do Spark são automaticamente visíveis e podem ser consultadas pelo SQL sob demanda.

1. No Synapse Studio, acesse o hub **Desenvolver** e crie um script de SQL.
1. Defina **Conectar-se a** como **SQL sob demanda**.
1. Cole o texto a seguir no script e execute-o.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Na primeira vez que você executar uma consulta que usa SQL sob demanda, levará cerca de dez segundos para que o SQL sob demanda reúna os recursos de SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar usando o Spark](get-started-analyze-spark.md)
