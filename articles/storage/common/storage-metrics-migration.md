---
title: Migração de métricas de Armazenamento do Azure | Microsoft Docs
description: Saiba como migrar métricas antigas para novas métricas gerenciadas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 10768ca4c6fbe4afc322fa9a7045c7cc4fe6f175
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681313"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas de Armazenamento do Azure

Alinhado com a estratégia de unificar a experiência do Azure Monitor, o Armazenamento do Microsoft Azure integra métricas à plataforma do Azure Monitor. No futuro, o serviço das métricas antigas terminará com uma notificação antecipada com base no Azure Policy. Se você depender de métricas de armazenamento antigas, precisará migrar antes da data de término do serviço para manter as informações da métrica.

Este artigo mostra como migrar das métricas antigas para as novas métricas.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Reconhecer as métricas antigas gerenciadas pelo Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure coleta valores de métrica antigas, agrega e armazena-os nas tabelas $Metric dentro da mesma conta de armazenamento. Você pode usar o portal do Azure para configurar um gráfico de monitoramento. Também é possível usar os SDKs de Armazenamento do Microsoft Azure para ler os dados das tabelas $Metric com base no esquema. Para obter mais informações, consulte [Análise de Armazenamento](./storage-analytics.md).

As métricas antigas fornecem métricas de capacidade apenas no Azure Storage Blob. Métricas antigas fornecem métricas de transação em armazenamento de Blobs, armazenamento de Tabelas, Arquivos do Azure e armazenamento de Filas.

Métricas antigas são projetadas em um esquema simples. O design resulta em valor de métrica zero quando você não tem os padrões de tráfego que ativam a métrica. Por exemplo, o valor **ServerTimeoutError** é definido como 0 em tabelas $Metric, mesmo quando você não recebe nenhum erro de tempo limite do servidor do tráfego dinâmico para uma conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Entender as nova métricas gerenciadas pelo Azure Monitor

Para novas métricas de armazenamento, o Armazenamento do Microsoft Azure emite os dados de métrica para o back-end do Azure Monitor. O Azure Monitor fornece uma experiência de monitoramento unificada, incluindo dados do portal, bem como ingestão de dados. Para obter mais detalhes, você pode consultar este [artigo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As novas métricas fornecem as métricas de capacidade e métricas de transação no Blob, Tabela, Arquivo e Fila.

Multidimensão é um dos recursos que o Azure Monitor fornece. O Armazenamento do Azure adota o design ao definir o novo esquema de métrica. Para dimensões com suporte em métricas, você pode localizar os detalhes em [Métricas de Armazenamento do Microsoft Azure no Azure Monitor](./storage-metrics-in-azure-monitor.md). O design multidimensional fornece economia em largura de banda de ingestão e a capacidade de armazenar métricas. Consequentemente, se o tráfego não ativou métricas relacionadas, os dados relacionados de métrica não serão gerados. Por exemplo, se o tráfego não disparou nenhum erro de tempo limite do servidor, o Azure Monitor não retornará nenhum dado quando você consultar o valor da métrica **Transações** com dimensão **ResponseType** igual a **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapeamento de métricas entre métricas antigas e novas métricas

Se você lê dados da métrica programaticamente, você precisa adotar o novo esquema de métricas em seus programas. Para reconhecer melhor as alterações, você pode consultar o mapeamento listado na tabela a seguir:

**Métricas de capacidade**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Capacidade**            | **BlobCapacity** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ObjectCount**        | **BlobCount** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

As métricas a seguir são novas ofertas que as métricas antigas não dão suporte:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Métricas de transação**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** e dimensão de **Autenticação** igual a **Anônimo** |
| **AnonymousClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousClientTimeoutError** | Transações com dimensão **ResponseType** igual a **ClientTimeoutError** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousNetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousServerTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousSuccess** | Transações com dimensão **ResponseType** igual a **Sucesso** e dimensões de **Autenticação** igual a **Anônimo** |
| **AnonymousThrottlingError** | Transações com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e dimensão de**Autenticação** igual a **Anônimo** |
| **AuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **Disponibilidade** | **Disponibilidade** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **ClientTimeoutError** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** |
| **NetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **PercentClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **PercentNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **PercentSuccess** | Transações com a dimensão **ResponseType** igual a **Success** |
| **PercentThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** |
| **PercentTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** ou **ResponseType** igual a **ClientTimeoutError** |
| **SASAuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** e dimensão de **Autenticação** igual a **SAS** |
| **SASClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** e dimensões de **Autenticação** igual a **SAS** |
| **SASClientTimeoutError** | Transações com dimensão **ResponseType** igual a **ClientTimeoutError** e dimensões de **Autenticação** igual a **SAS** |
| **SASNetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** e dimensões de **Autenticação** igual a **SAS** |
| **SASServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** e dimensões de **Autenticação** igual a **SAS** |
| **SASServerTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** e dimensões de **Autenticação** igual a **SAS** |
| **SASSuccess** | Transações com dimensão **ResponseType** igual a **Sucesso** e dimensões de **Autenticação** igual a **SAS** |
| **SASThrottlingError** | Transações com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e dimensão de**Autenticação** igual a **SAS** |
| **ServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **ServerTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** |
| **Êxito** | Transações com a dimensão **ResponseType** igual a **Success** |
| **ThrottlingError** | **Transações** com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError**|
| **TotalBillableRequests** | **Transações** |
| **TotalEgress** | **Saída** |
| **TotalIngress** | **Entrada** |
| **TotalRequests** | **Transações** |

## <a name="faq"></a>Perguntas frequentes

### <a name="how-should-i-migrate-existing-alert-rules"></a>Como posso migrar o regras de alerta existentes?

Se você criou regras de alerta clássicas com base em métricas de armazenamento antigas, será necessário criar novas regras de alerta com base no novo esquema de métrica.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Os novos dados de métrica são armazenados na mesma conta de armazenamento por padrão?

Não. Para arquivar os dados de métrica em uma conta de armazenamento, use a [API de configuração de diagnóstico do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Próximas etapas

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de Armazenamento do Microsoft Azure no Azure Monitor](./storage-metrics-in-azure-monitor.md)
