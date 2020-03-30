---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334794"
---
**Volume e retenção de coleta de dados** 

| Camada | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Nível de preços atual por GB<br>(introduzido em abril de 2018) | Sem limite | 30 - 730 dias | A retenção de dados após 31 dias está disponível para taxas adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Níveis legados livres<br>(introduzido em abril de 2016) | 500 MB | 7 dias | Quando seu espaço de trabalho atinge o limite de 500 MB por dia, a ingestão de dados pára e é retomada no início do dia seguinte. Um dia é baseado em UTC. Observe que os dados coletados pelo Azure Security Center não estão incluídos neste limite de 500 MB por dia e continuarão a ser coletados acima desse limite.  |
| Nível autônomo legado por GB<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados após 31 dias está disponível para taxas adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Legado por nó (OMS)<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados após 31 dias está disponível para taxas adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Nível padrão legado | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Nível Premium legado | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de espaços de trabalho por assinatura.**

| Tipo de preço    | Limite de espaço de trabalho | Comentários
|:---|:---|:---|
| Camada gratuita  | 10 | Esse limite não pode ser aumentado. |
| Todos os outros níveis | Sem limite | Você está limitado pelo número de recursos dentro de um grupo de recursos e pelo número de grupos de recursos por assinatura. |

**Portal Azure**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registros máximos retornados por uma consulta de log | 10.000 | Reduza os resultados usando escopo de consulta, intervalo de tempo e filtros na consulta. |


**API de coletor de dados**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Tamanho máximo para um único post | 30 MB | Divida volumes maiores em vários postes. |
| Tamanho máximo para valores de campo  | 32 KB | Campos com mais de 32 KB são truncados. |

**API de Pesquisa**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registros máximos retornados em uma única consulta | 500.000 | |
| Tamanho máximo dos dados retornados | 64.000.000 bytes (~61 MiB)| |
| Tempo máximo de execução da consulta | 10 minutos | Consulte os intervalos para obter [detalhes.](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)  |
| Taxa máxima de solicitação | 200 solicitações por 30 segundos por usuário AAD ou endereço IP do cliente | Consulte [os limites da taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para obter detalhes. |

**Limites gerais do espaço de trabalho**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Colunas máximas em uma tabela         | 500 | |
| Caracteres máximos para nome da coluna | 500 | |
| Exportação de dados | Não disponível no momento | Use a função azure ou o aplicativo lógico para agregar e exportar dados. | 

**Taxa de volume de ingestão de dados**


O Azure Monitor é um serviço de dados de grande escala que atende milhares de clientes que enviam terabytes de dados por mês em um ritmo cada vez maior. O limite de taxa de volume de ingestão padrão para dados enviados dos recursos do Azure usando [configurações de diagnóstico](../articles/azure-monitor/platform/diagnostic-settings.md) é de aproximadamente **6 GB/min** por espaço de trabalho. Este é um valor aproximado, uma vez que o tamanho real pode variar entre os tipos de dados, dependendo do comprimento do registro e sua razão de compressão. Esse limite não se aplica aos dados enviados de agentes ou [a API de coleta](../articles/azure-monitor/platform/data-collector-api.md)de dados .

Se você enviar dados a uma taxa mais alta para um único espaço de trabalho, alguns dados serão descartados e um evento será enviado para a tabela *Operação* em seu espaço de trabalho a cada 6 horas, enquanto o limite continua a ser excedido. Se o volume de ingestão continuar a exceder o limite de taxa ou você estiver esperando alcançá-lo em breve, você pode solicitar um aumento para o seu espaço de trabalho abrindo uma solicitação de suporte.
 
Para ser notificado em tal evento em seu espaço de trabalho, crie uma regra de [alerta de log](../articles/azure-monitor/platform/alerts-log.md) usando a seguinte consulta com base lógica de alerta no número de resultados mais de zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Dependendo de quanto tempo você está usando o Log Analytics, você pode ter acesso a níveis de preços legados. Saiba mais sobre [os níveis de preços legados do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 