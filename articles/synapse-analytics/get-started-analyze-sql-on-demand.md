---
title: 'Tutorial: Introdução à análise de dados com o SQL sob demanda'
description: Neste tutorial, você aprenderá a analisar dados com o SQL sob demanda usando dados localizados em bancos de dados do Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093165"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analisar dados com o SQL sob demanda

Neste tutorial, você aprenderá a analisar dados com o SQL sob demanda usando dados localizados em bancos de dados do Spark. 

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
