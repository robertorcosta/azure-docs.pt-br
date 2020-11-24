---
title: 'Tutorial: Introdução à análise de dados com pools de SQL dedicados'
description: Neste tutorial, você usará os dados de exemplo de táxi de NYC para explorar as funcionalidades analíticas do pool de SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: ae7b15bf2e2efd72184110a105f68815516569ab
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843903"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analisar dados com pools de SQL dedicados

O Azure Synapse Analytics fornece a capacidade de analisar dados com o pool de SQL dedicado. Neste tutorial, você usará os dados de táxis de Nova York para explorar as funcionalidades do pool de SQL dedicado.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Carregar os dados de Táxi de Nova York no SQLPOOL1

1. No Synapse Studio, navegue até o hub **Desenvolver** e crie um script de SQL
1. Selecione o pool 'SQLPOOL1' (pool criado na [ETAPA 1](https://docs.microsoft.com/azure/synapse-analytics/get-started-create-workspace#create-a-sql-pool) deste tutorial) na seção 'Conectar-se a' do script.
1. Insira o seguinte código:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. Esse script será concluído em cerca de 60 segundos. Ele carrega 2 milhões linhas de dados de Táxi de Nova York em uma tabela chamada **dbo.Trip**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explorar os dados de táxis de Nova York no pool de SQL dedicado

1. No Synapse Studio, acesse o hub **Dados**.
1. Acesse **SQLPOOL1** > **Tabelas**. Você verá várias tabelas carregadas.
1. Clique com o botão direito do mouse na tabela **dbo.Trip** e selecione **Novo Script de SQL** > **Selecionar as Primeiras 100 Linhas**.
1. Aguarde enquanto um novo script SQL é criado e executado.
1. Observe na parte superior do script de SQL que **Conectar-se ao** é automaticamente definido como o pool de SQL chamado **SQLPOOL1**.
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

