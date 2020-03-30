---
title: Monitorar operações de ingestão do Azure Data Explorer usando logs de diagnóstico
description: Saiba como configurar registros de diagnóstico do Azure Data Explorer para monitorar as operações de ingestão.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277420"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitore as operações de ingestão do Azure Data Explorer usando registros de diagnóstico (Preview)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, você ingere (carregar) dados em uma tabela em um banco de dados para que você possa executar consultas contra ele. Os registros de diagnóstico do [Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) fornecem dados sobre a operação dos recursos do Azure. O Azure Data Explorer usa registros de diagnóstico para obter informações sobre sucessos e falhas de ingestão. Você pode exportar logs de operação para o Azure Storage, Event Hub ou Log Analytics para monitorar o status de ingestão. Os logs do Azure Storage e do Azure Event Hub podem ser encaminhados para uma tabela no cluster Azure Data Explorer para análise suplementar.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* Crie um [cluster e um banco de dados](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configure registros de diagnóstico para um cluster Azure Data Explorer

Os registros de diagnóstico podem ser usados para configurar a coleta dos seguintes dados de log:
* Operações de ingestão bem-sucedidas: Esses registros têm informações sobre operações de ingestão concluídas com sucesso.
* Operações de ingestão com falhas: Esses registros têm informações detalhadas sobre operações de ingestão com falha, incluindo detalhes de erro. 

Os dados são então arquivados em uma conta de armazenamento, transmitidos para um Event Hub ou enviados para o Log Analytics, de acordo com suas especificações.

### <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico

Os logs de diagnóstico estão desabilitados por padrão. Para habilitar registros de diagnóstico, faça as seguintes etapas:

1. No [portal Azure,](https://portal.azure.com)selecione o recurso de cluster Azure Data Explorer que deseja monitorar.
1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
  
    ![Adicionar registros de diagnóstico](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selecione **Adicionar configuração de diagnóstico**.
1. Na janela **configurações diagnósticos:**
 
    ![Configuração das configurações de diagnóstico](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selecione **Nome** para sua configuração de diagnóstico.
    1. Selecione um ou mais alvos: uma conta de armazenamento, o Event Hub ou o Log Analytics.
    1. Selecione logs a `SucceededIngestion` `FailedIngestion`serem coletados: ou .
    1. Selecione [as métricas](using-metrics.md#supported-azure-data-explorer-metrics) a serem coletadas (opcional).  
    1. Selecione **Salvar** para salvar as novas configurações e métricas de registros de diagnóstico.
    1. Crie uma **nova solicitação de suporte** no portal Azure para solicitar a ativação de registros de diagnóstico.

Novas configurações serão definidas em poucos minutos. Os logs aparecem no destino de arquivamento configurado (conta de armazenamento, hub de eventos ou análise de log). 

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os [registros de diagnóstico do Azure Monitor compartilham um esquema comum de alto nível](/azure/azure-monitor/platform/diagnostic-logs-schema). O Azure Data Explorer tem propriedades exclusivas para seus próprios eventos. Todos os logs são armazenados em um formato JSON.

### <a name="ingestion-logs-schema"></a>Esquema de torções de troncos

As seqüências De log JSON incluem elementos listados na tabela a seguir:

|Nome               |Descrição
|---                |---
|time               |Horário do relatório
|resourceId         |ID de recurso do Azure Resource Manager
|operationName      |Nome da operação: 'MICROSOFT. KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Versão do esquema: '1.0' 
|category           |Categoria da operação. `SucceededIngestion` ou `FailedIngestion`. As propriedades diferem para [uma operação bem sucedida](#successful-ingestion-operation-log) ou uma operação com [falha.](#failed-ingestion-operation-log)
|properties         |Informações detalhadas da operação.

#### <a name="successful-ingestion-operation-log"></a>Registro de operação de ingestão bem-sucedido

**Exemplo:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Propriedades de um registro de diagnóstico de operação bem-sucedido**

|Nome               |Descrição
|---                |---
|sucedeuOn        |Tempo de conclusão da ingestão
|operationId        |ID da operação de ingestão do Azure Data Explorer
|Banco de Dados           |Nome do banco de dados de destino
|tabela              |Nome da tabela de destino
|ingestionSourceId  |ID da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou uri bolha
|rootActivityId     |ID da atividade

#### <a name="failed-ingestion-operation-log"></a>Registro de operação de ingestão com falha

**Exemplo:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Propriedades de um registro de diagnóstico de operação com falha**

|Nome               |Descrição
|---                |---
|falhou           |Tempo de conclusão da ingestão
|operationId        |ID da operação de ingestão do Azure Data Explorer
|Banco de Dados           |Nome do banco de dados de destino
|tabela              |Nome da tabela de destino
|ingestionSourceId  |ID da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou uri bolha
|rootActivityId     |ID da atividade
|detalhes            |Descrição detalhada da mensagem de falha e erro
|errorCode          |Código do erro 
|falhaStatus      |`Permanent` ou `Transient`. A repetição de um fracasso transitório pode ter sucesso.
|origina-seFromUpdatePolicy|Verdade se falha se origina ruma de uma política de atualização
|deveRetry        |Verdade se a tentativa pode ter sucesso

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Inserie e consulte dados de monitoramento no Azure Data Explorer](ingest-data-no-code.md)
* [Usar métricas para monitorar a integridade do cluster](using-metrics.md)

