---
title: Ingerir dados de amostra no Azure Data Explorer
description: Saiba como coletar (carregar) dados de amostra relacionados ao clima no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975169"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Início Rápido: Ingerir dados de amostra no Azure Data Explorer

Este artigo mostra como ingerir (carregar) dados de amostra em um banco de dados do Azure Data Explorer. Existem [várias maneiras de ingerir dados](ingest-data-overview.md); Este artigo enfoca uma abordagem básica adequada para fins de teste.

> [!NOTE]
> Você já terá esses dados se tiver concluído [Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md).

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster de teste e um banco de dados](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingestão de dados

O conjunto de dados de amostra **StormEvents** contém dados relacionados ao clima dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

1. Entre em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. No canto superior esquerdo do aplicativo, selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster**, insira o URL do cluster no formulário e `https://<ClusterName>.<Region>.kusto.windows.net/`, selecione **Adicionar**.

1. Cole o comando a seguir e selecione **Executar** para criar uma tabela StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Cole o comando a seguir e selecione **Executar** para ingerir dados na tabela StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Depois que o processamento for concluído, cole a consulta a seguir, selecione a consulta na janela e selecione **Executar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A consulta retorna os seguintes resultados dos dados de amostra ingeridos.

    ![Resultados da consulta](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Próximas etapas

* [Ingestão de dados do Azure Data Explorer](ingest-data-overview.md) para saber mais sobre os métodos de ingestão.
* [Início Rápido: Consultar dados na interface do usuário Web do Azure Data Explorer](web-query-data.md).
* [Escreva consultas](write-queries.md) com linguagem de consulta Kusto.
