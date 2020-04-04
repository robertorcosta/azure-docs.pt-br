---
title: Construir soluções integradas
description: Ferramentas de solução e parceiros que se integram com um pool Synapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633138"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integre outros serviços com um data warehouse sql analytics

O recurso SQL Analytics no Azure Synapse Analytics permite que os usuários se integrem com muitos dos outros serviços no Azure. Usando o SQL Analytics, você pode criar um data warehouse através de seu recurso SQL Pool, que pode então utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Fábrica de dados do Azure
* Azure Machine Learning
* Stream Analytics do Azure

Para obter mais informações sobre os serviços de integração em todo o Azure, consulte o artigo [dos parceiros de Integração.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

A integração do Power BI permite combinar o poder de computação de um data warehouse com a emissão dinâmica de relatórios e visualização do Power BI. Atualmente, a integração do Power BI inclui:

* **Direct Connect**: Uma conexão mais avançada com pushdown lógico contra um data warehouse provisionado usando pool SQL. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abra no Power BI**: O botão 'Abrir em Poder BI' passa as informações de instância para o Power BI para uma maneira simplificada de se conectar.

Para obter mais informações, consulte [Integrar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou a [Documentação do Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory oferece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração do pool SQL com a Fábrica de Dados do Azure inclui:

* **Procedimentos Armazenados**: Orquestrar a execução dos procedimentos armazenados.
* **Cópia**: Use o ADF para mover dados para o pool SQL. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou PolyBase nos bastidores.

Para obter mais informações, consulte [Integrar com o Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerenciado que permite criar modelos complexos utilizando um grande conjunto de ferramentas de previsão. O pool SQL é suportado como fonte e destino para esses modelos e tem a seguinte funcionalidade:

* **Ler dados:** Modelos de acionamento em escala usando T-SQL contra pool SQL.
* **Gravar dados:** Comprometa alterações de qualquer modelo de volta ao pool SQL.

Para obter mais informações, consulte [Integrar com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

O Stream Analytics do Azure é uma infraestrutura complexa, totalmente gerenciada para processamento e consumo de dados de eventos gerados por meio do hub de eventos do Azure.  A integração com o pool SQL permite que os dados de streaming sejam efetivamente processados e armazenados juntamente com dados relacionais, permitindo análises mais profundas e avançadas.  

* **Saída de emprego:** Envie a saída dos trabalhos do Stream Analytics diretamente para o pool SQL.

Para obter mais informações, consulte [Integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
