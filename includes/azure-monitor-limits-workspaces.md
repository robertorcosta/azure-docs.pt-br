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
ms.openlocfilehash: 6979ce65022f350a93f533951d634b8e436283bc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186320"
---
**Volume e retenção da coleta de dados** 

| Camada | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Tipo de preço atual por GB<br>(introdução em abril de 2018) | Sem limite | De 30 a 730 dias | A retenção de dados por mais de 31 dias está disponível por preços adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Camadas gratuitas herdadas<br>(introdução em abril de 2016) | 500 MB | 7 dias | Quando o workspace atingir o limite de 500 MB por dia, a ingestão de dados será interrompida e reiniciada no início do dia seguinte. Um dia é baseado em UTC. Observe que os dados coletados pela Central de Segurança do Azure não estão incluídos nesse limite de 500 MB por dia e continuarão sendo coletados acima desse limite.  |
| Camada herdada autônoma por GB<br>(introdução em abril de 2016) | Sem limite | De 30 a 730 dias | A retenção de dados por mais de 31 dias está disponível por preços adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Herdado por nó (OMS)<br>(introdução em abril de 2016) | Sem limite | De 30 a 730 dias | A retenção de dados por mais de 31 dias está disponível por preços adicionais. Saiba mais sobre o preço do Azure Monitor. |
| Camada Standard herdada | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Camada Premium herdada | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de workspaces por assinatura.**

| Tipo de preço    | Limite do workspace | Comentários
|:---|:---|:---|
| Camada gratuita  | 10 | Esse limite não pode ser aumentado. |
| Todas as outras camadas | Sem limite | Você está limitado pelo número de recursos dentro de um grupo de recursos e pelo número de grupos de recursos por assinatura. |

**Portal do Azure**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Máximo de registros retornados por uma consulta de log | 10.000 | Reduza os resultados usando o escopo da consulta, o intervalo de tempo e os filtros na consulta. |


**API do Coletor de dados**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Tamanho máximo para uma postagem única | 30 MB | Dividir volumes maiores em várias postagens. |
| Tamanho máximo para valores de campo  | 32 KB | Campos com mais de 32 KB são truncados. |

**API de Pesquisa**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Máximo de registros retornados em uma consulta única | 500.000 | |
| Tamanho máximo dos dados retornados | 64.000.000 bytes (aproximadamente 61 MiB)| |
| Tempo máximo de execução da consulta | 10 minutos | Confira os [tempos limites](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para obter detalhes.  |
| Taxa máxima de solicitação | 200 solicitações por 30 segundos por usuário do Azure AD ou por endereço IP do cliente | Consulte os [Limites de taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para obter detalhes. |

**Limites gerais do workspace**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Máximo de colunas em uma tabela         | 500 | |
| Máximo de caracteres para o nome da coluna | 500 | |
| Exportação de dados | Não disponível no momento | Use a Função do Azure ou o Aplicativo Lógico para agregar e exportar dados. | 

**Taxa do volume de ingestão de dados**

O Azure Monitor é um serviço de dados de grande escala que atende milhares de clientes que enviam terabytes de dados por mês em um ritmo cada vez maior. O limite da taxa de volume pretende proteger os clientes do Azure Monitor de picos repentinos de ingestão no ambiente de multilocação. Um limite da taxa do volume de ingestão padrão de 500 MB (compactados) se aplica aos workspaces, que é de aproximadamente **6 GB/min** descompactados. O tamanho real pode variar entre os tipos de dados, dependendo do comprimento do log e da taxa de compactação. Esse limite se aplica a todos os dados ingeridos, sejam eles enviados de recursos do Azure usando as [configurações de diagnóstico](../articles/azure-monitor/platform/diagnostic-settings.md), a [API do Data Collector](../articles/azure-monitor/platform/data-collector-api.md) ou os agentes.

Ao enviar dados para um workspace a uma taxa de volume superior a 80% do limite configurado no workspace, um evento será enviado para a tabela de *operações* no workspace a cada seis horas, enquanto o limite continua sendo excedido. Quando a taxa do volume ingerido for maior do que o limite, alguns dados serão descartados e um evento será enviado para a tabela de *operações* no workspace a cada seis horas, enquanto o limite continua sendo excedido. Se a taxa do volume de ingestão continuar excedendo o limite ou se você estiver esperando alcançá-la em breve, poderá solicitar o aumento no workspace abrindo uma solicitação de suporte. 

Para receber uma notificação sobre esse evento no workspace, crie uma [regra de alerta de log](../articles/azure-monitor/platform/alerts-log.md) usando a consulta a seguir com base lógica de alerta no número de resultados superior a zero, com período de avaliação de cinco minutos e frequência de cinco minutos.

A taxa do volume de ingestão atingiu 80% do limite:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"
```

Limite atingido na taxa do volume de ingestão:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed the threshold"
```

>[!NOTE]
>Dependendo de quanto tempo está usando o Log Analytics, você poderá ter acesso a tipos de preço herdados. Saiba mais sobre os [tipos de preço herdados do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
