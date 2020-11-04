---
title: Crie soluções integradas
description: Ferramentas de solução e parceiros que se integram a um pool SQL dedicado no Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9f92128266c41912868f6ab74abaa2d374c6d236
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324491"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integre outros serviços com um pool SQL dedicado no Azure Synapse Analytics.

O recurso de pool dedicado do SQL dentro do Azure Synapse Analytics permite que os usuários se integrem a muitos dos outros serviços no Azure. Usando o Synapse SQL, você pode criar um data warehouse por meio de seu recurso de pool do SQL dedicado, que pode utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Fábrica de dados do Azure
* Azure Machine Learning
* Stream Analytics do Azure

Para obter mais informações sobre o serviço de integração entre serviços do Azure, leia o artigo sobre [Integração de parceiros](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

A integração do Power BI permite que você combine o poder da computação de um data warehouse com os relatórios e a visualização dinâmica do Power BI. Atualmente, a integração do Power BI inclui:

* Conexão **direta** : uma conexão mais avançada com aplicação lógica em relação A um data warehouse provisionado com o pool SQL dedicado. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abrir no Power BI** : O botão “Abrir no Power BI” passa informações de instância para o Power BI para uma maneira simplificada de se conectar.

Para obter mais informações, consulte [Integrar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou a [Documentação do Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory oferece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração dedicada ao pool do SQL com o Azure Data Factory inclui:

* **Procedimentos Armazenados** : orquestra a execução de procedimentos armazenados.
* **Copiar** : Use o ADF para mover dados para o pool SQL dedicado. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou PolyBase nos bastidores.

Para obter mais informações, consulte [Integrar com o Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerenciado que permite criar modelos complexos utilizando um grande conjunto de ferramentas de previsão. O pool SQL dedicado tem suporte como origem e destino para esses modelos e tem a seguinte funcionalidade:

* **Ler dados:** Impulsionar modelos em escala usando o T-SQL em um pool SQL dedicado.
* **Gravar dados:** Confirme as alterações de qualquer modelo de volta para um pool SQL dedicado.

Para obter mais informações, consulte [Integrar com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

O Stream Analytics do Azure é uma infraestrutura complexa, totalmente gerenciada para processamento e consumo de dados de eventos gerados por meio do hub de eventos do Azure.  A integração com o pool SQL dedicado permite que os dados de streaming sejam efetivamente processados e armazenados junto com dados relacionais, permitindo uma análise mais profunda e mais avançada.  

* **Saída do trabalho:** Envie a saída de trabalhos de Stream Analytics diretamente para um pool SQL dedicado.

Para obter mais informações, consulte [Integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
