---
title: Registro de diagnóstico
titleSuffix: Azure Cognitive Services
description: Este guia fornece instruções passo-a-passo para habilitar o registro de diagnóstico para um Serviço Cognitivo Azure. Esses registros fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são usados para identificação e depuração de problemas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827910"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Habilite o registro de diagnóstico para serviços cognitivos do Azure

Este guia fornece instruções passo-a-passo para habilitar o registro de diagnóstico para um Serviço Cognitivo Azure. Esses registros fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são usados para identificação e depuração de problemas. Antes de continuar, você deve ter uma conta azure com uma assinatura de pelo menos um Serviço Cognitivo, como [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), Speech [Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)ou [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar o registro de diagnóstico, você precisará de um lugar para armazenar seus dados de registro. Este tutorial usa o Azure Storage e o Log Analytics.

* [Armazenamento Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - Retém registros de diagnóstico para auditoria de políticas, análise estática ou backup. A conta de armazenamento não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - Uma ferramenta flexível de pesquisa e análise de logs que permite a análise de logs brutos gerados por um recurso do Azure.

> [!NOTE]
> Opções adicionais de configuração estão disponíveis. Para saber mais, consulte [Coletar e consumir dados de log de seus recursos do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Habilite a coleta de registros de diagnóstico  

Vamos começar ativando o registro de diagnóstico usando o portal Azure.

> [!NOTE]
> Para habilitar esse recurso usando o PowerShell ou o Azure CLI, use as instruções fornecidas no [Coletar e consumir dados de log de seus recursos do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Navegue até o Portal do Azure. Em seguida, localize e selecione um recurso de Serviços Cognitivos. Por exemplo, sua assinatura do Bing Web Search.   
2. Em seguida, no menu de navegação à esquerda, **localize as** **configurações**de Monitoramento e selecione Diagnóstico . Esta tela contém todas as configurações de diagnóstico criadas anteriormente para este recurso.
3. Se houver um recurso criado anteriormente que você gostaria de usar, você pode selecioná-lo agora. Caso contrário, selecione **+ Adicione a configuração de diagnóstico**.
4. Digite um nome para a configuração. Em seguida, **selecione Arquivamento para uma conta de armazenamento** e Envie para registrar o **Analytics**.
5. Quando solicitado a configurar, selecione a conta de armazenamento e o espaço de trabalho DO OMS que você gostaria de usar para armazenar registros de diagnóstico. **Nota**: Se você não tiver uma conta de armazenamento ou espaço de trabalho OMS, siga as instruções para criar uma.
6. Selecione **Auditoria,** **Resposta de Solicitação**e Todas as **Métricas**. Em seguida, defina o período de retenção dos dados do registro de diagnóstico. Se uma política de retenção for definida como zero, os eventos dessa categoria de log serão armazenados indefinidamente.
7. Clique em **Salvar**.

Pode levar até duas horas antes de registrar dados disponíveis para consulta e análise. Então não se preocupe se você não ver nada imediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Exibir e exportar dados de diagnóstico do Azure Storage

O Azure Storage é uma solução robusta de armazenamento de objetos que é otimizada para armazenar grandes quantidades de dados não estruturados. Nesta seção, você aprenderá a consultar sua conta de armazenamento para transações totais durante um prazo de 30 dias e exportar os dados para o excel.

1. A partir do portal Azure, localize o recurso de armazenamento Azure que você criou na última seção.
2. No menu de navegação à esquerda, **localize O Monitoramento** e selecione **Métricas**.
3. Use as as sadas disponíveis para configurar sua consulta. Para este exemplo, vamos definir o intervalo de tempo para **Durar 30 dias** e a métrica para **Transação**.
4. Quando a consulta estiver concluída, você verá uma visualização da transação nos últimos 30 dias. Para exportar esses dados, use o botão **Exportar para Excel** localizado na parte superior da página.

Saiba mais sobre o que você pode fazer com dados de diagnóstico no [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Exibir logs no Log Analytics

Siga estas instruções para explorar dados de análise de log para o seu recurso.

1. No portal Azure, localize e selecione **O Log Analytics** no menu de navegação à esquerda.
2. Localize e selecione o recurso criado ao habilitar diagnósticos.
3. Em **Geral,** localize e selecione **Logs**. A partir desta página, você pode executar consultas contra seus logs.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas básicas do Kusto que você pode usar para explorar seus dados de log.

Execute esta consulta para todos os registros de diagnóstico dos Serviços Cognitivos do Azure por um período de tempo especificado:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Execute esta consulta para ver os 10 registros mais recentes:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Execute esta consulta às operações de grupo por **recurso:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Execute esta consulta para encontrar o tempo médio necessário para executar uma operação:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Execute esta consulta para exibir o volume de operações ao longo do tempo dividido pela OperationName com contagens binadas para cada 10 s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Próximas etapas

* Para entender como ativar o registro, e também as categorias métricas e log que são suportadas pelos vários serviços do Azure, leia tanto a [visão geral das métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) nos artigos do Microsoft Azure quanto a visão geral dos artigos [do Azure Diagnostic Logs.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* Leia estes artigos para saber mais sobre os hubs de eventos:
  * [O que é Hub de Eventos do Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introdução aos Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Leia [Baixar métricas e logs de diagnósticos do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Leia [Entenda as pesquisas de log nos registros do Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
