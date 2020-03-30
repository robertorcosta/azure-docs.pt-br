---
title: Use a ingestão de streaming para ingerir dados no Azure Data Explorer
description: Saiba como ingerir dados (carregar) no Azure Data Explorer usando a ingestão de streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297077"
---
# <a name="streaming-ingestion-preview"></a>Ingestão por streaming (Visualização)

Use a ingestão de streaming quando precisar de baixa latência com um tempo de ingestão inferior a 10 segundos para dados de volume variados. É usado para otimizar o processamento operacional de muitas tabelas, em uma ou mais bases de dados, onde o fluxo de dados em cada tabela é relativamente pequeno (poucos registros por segundo), mas o volume total de ingestão de dados é alto (milhares de registros por segundo). 

Use a ingestão em massa em vez de a ingestão de streaming quando a quantidade de dados cresce para mais de 1 MB por segundo por tabela. Leia [a visão geral da ingestão](/azure/data-explorer/ingest-data-overview) de dados para saber mais sobre os diversos métodos de ingestão.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Faça login na [Web UI](https://dataexplorer.azure.com/).
* Crie [um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Habilite a ingestão de streaming em seu cluster

> [!WARNING]
> Por favor, revise as [limitações](#limitations) antes de habilitar a ingestão fumegante.

1. No portal do Azure, acesse o cluster do Azure Data Explorer. Em **Configurações,** selecione **Configurações**. 
1. No painel **Configurações,** selecione **Ativado** para ativar a **ingestão de streaming**.
1. Selecione **Salvar**.
 
    ![ingestão de streaming em](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Na [Web UI,](https://dataexplorer.azure.com/)defina a [política de ingestão de streaming](/azure/kusto/management/streamingingestionpolicy) em tabelas ou bancos de dados que receberão dados de streaming. 

    > [!NOTE]
    > * Se a política for definida no nível do banco de dados, todas as tabelas do banco de dados serão habilitadas para a ingestão de streaming.
    > * A política aplicada pode referenciar apenas dados recém-ingeridos e não outras tabelas no banco de dados.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Use a ingestão de streaming para ingerir dados no seu cluster

Existem dois tipos de ingestão de streaming suportados:


* [**Event Hub**](/azure/data-explorer/ingest-data-event-hub), que é usado como fonte de dados
* **A ingestão personalizada** exige que você escreva um aplicativo que use uma das bibliotecas de clientes do Azure Data Explorer. Consulte [a amostra de ingestão de streaming](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) para uma aplicação de amostra.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Escolha o tipo de ingestão de streaming apropriado

|   |Hub de evento  |Ingestão Personalizada  |
|---------|---------|---------|
|Atraso de dados entre o início da ingestão e os dados disponíveis para consulta   |    Atraso mais longo     |   Atraso mais curto      |
|Sobrecarga de desenvolvimento    |   Configuração rápida e fácil, sem sobrecarga de desenvolvimento    |   Alta sobrecarga de desenvolvimento para aplicação para lidar com erros e garantir a consistência dos dados     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Desativar a ingestão de streaming em seu cluster

> [!WARNING]
> Desativar a ingestão de streaming pode levar algumas horas.

1. Retire a política de [ingestão](/azure/kusto/management/streamingingestionpolicy) de streaming de todas as tabelas e bancos de dados relevantes. A remoção da política de ingestão de streaming desencadeia a movimentação de dados de ingestão de streaming do armazenamento inicial para o armazenamento permanente no armazenamento da coluna (extensões ou fragmentos). O movimento de dados pode durar entre alguns segundos e algumas horas, dependendo da quantidade de dados no armazenamento inicial e de como a CPU e a memória são usadas pelo cluster.
1. No portal do Azure, acesse o cluster do Azure Data Explorer. Em **Configurações,** selecione **Configurações**. 
1. No painel **Configurações,** selecione **Desativar** a **ingestão de streaming**.
1. Selecione **Salvar**.

    ![Ingestão por streaming](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limitações

* A ingestão de streaming não suporta [cursores de banco de dados](/azure/kusto/management/databasecursor) ou mapeamento de [dados](/azure/kusto/management/mappings). Apenas o mapeamento de dados [pré-criado](/azure/kusto/management/create-ingestion-mapping-command) é suportado. 
* O desempenho de ingestão de streaming e as escalas de capacidade com aumento dos tamanhos de VM e cluster. As ingestões simultâneas são limitadas a seis ingestão por núcleo. Por exemplo, para SKUs de 16 núcleos, como D14 e L16, a carga máxima suportada é de 96 ingestões simultâneas. Para duas SKUs principais, como d11, a carga máxima suportada é de 12 ingestões simultâneas.
* A limitação do tamanho dos dados por solicitação de ingestão é de 4 MB.
* Atualizações de esquemas, como criação e modificação de tabelas e mapeamentos de ingestão, podem levar até cinco minutos para o serviço de ingestão de streaming.
* Habilitando a ingestão de streaming em um cluster, mesmo quando os dados não são ingeridos via streaming, usa parte do disco SSD local das máquinas de cluster para transmitir dados de ingestão e reduz o armazenamento disponível para cache quente.
* [As marcas de extensão](/azure/kusto/management/extents-overview#extent-tagging) não podem ser definidas nos dados de ingestão de streaming.

## <a name="next-steps"></a>Próximas etapas

* [Consultar dados no Azure Data Explorer](web-query-data.md)
