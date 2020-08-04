---
title: 'Tutorial: Introdução à análise de dados com o pool de SQL'
description: Neste tutorial, você usará os dados de exemplo de táxi de NYC para explorar as funcionalidades analíticas do pool de SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325212"
---
# <a name="analyze-data-with-sql-pools"></a>Analisar dados com pools de SQL

O Azure Synapse Analytics fornece a capacidade de analisar dados com o pool de SQL. Neste tutorial, você usará os dados de exemplo de táxi de NYC para explorar as funcionalidades analíticas do pool de SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregar os dados de exemplo de táxi de Nova York no banco de dados SQLDB1

1. No Synapse Studio, no menu azul superior, selecione o ícone de interrogação ( **?** ).
1. Selecione **Introdução** > **Hub de introdução**.
1. No cartão com rótulo **Dados de consulta de exemplo**, selecione o pool de SQL chamado **SQLDB1**.
1. Selecione **Dados de consulta**. Uma notificação "Carregando dados de exemplo" é exibida brevemente. Uma barra de notificação azul-clara exibida perto da parte superior do Synapse Studio, indica que os dados estão sendo carregados no SQLDB1.
1. Depois que a barra de status ficar verde, descarte-a.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorar os dados de táxi de Nova York no pool de SQL

1. No Synapse Studio, acesse o hub **Dados**.
1. Acesse **SQLDB1** > **Tabelas**. Você verá várias tabelas carregadas.
1. Clique com o botão direito do mouse na tabela **dbo.Trip** e selecione **Novo Script de SQL** > **Selecionar as Primeiras 100 Linhas**.
1. Aguarde enquanto um novo script SQL é criado e executado.
1. Observe na parte superior do script de SQL que **Conectar ao** é automaticamente definido como o pool de SQL chamado **SQLDB1**.
1. Substitua o texto do script de SQL por esse código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Essa consulta mostra como as distâncias totais de viagem e a distância média da viagem estão relacionadas ao número de passageiros.
1. Na janela de resultados do script de SQL, altere a opção **Exibição** para **Gráfico** para uma visualização dos resultados como um gráfico de linhas.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar usando o Spark](get-started-analyze-spark.md)

