---
title: Crie soluções integradas
description: Ferramentas de solução e parceiros que se integram a um pool de SQL do Synapse.
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
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200997"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Integre outros serviços com um pool de SQL do Synapse (data warehouse)

O recurso de pool de SQL dentro do Azure Synapse Analytics permite que os usuários se integrem a vários outros serviços no Azure. Com o SQL do Synapse, é possível criar um data warehouse por meio do recurso do pool de SQL, que permite a utilização de vários serviços adicionais, incluindo:

* Power BI
* Fábrica de dados do Azure
* Azure Machine Learning
* Stream Analytics do Azure

Para obter mais informações sobre o serviço de integração entre serviços do Azure, leia o artigo sobre [Integração de parceiros](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

A integração do Power BI permite que você combine o poder da computação de um data warehouse com os relatórios e a visualização dinâmica do Power BI. Atualmente, a integração do Power BI inclui:

* **Direct Connect**: Uma conexão mais avançada com a aplicação de lógica no data warehouse provisionado usando pool de SQL. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abrir no Power BI**: O botão “Abrir no Power BI” passa informações de instância para o Power BI para uma maneira simplificada de se conectar.

Para obter mais informações, consulte [Integrar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou a [Documentação do Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory oferece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração do pool de SQL com o Azure Data Factory inclui:

* **Procedimentos Armazenados**: orquestra a execução de procedimentos armazenados.
* **Cópia**: usa o ADF para mover dados no pool de SQL. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou PolyBase nos bastidores.

Para obter mais informações, consulte [Integrar com o Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerenciado que permite criar modelos complexos utilizando um grande conjunto de ferramentas de previsão. Além disso, o pool de SQL tem compatibilidade tanto como fonte como destino para esses modelos, com as seguintes funcionalidades:

* **Ler dados:** gere modelos em grande escala usando o T-SQL baseado no pool de SQL.
* **Gravar dados**: aplique as alterações feitas em qualquer modelo ao pool de SQL.

Para obter mais informações, consulte [Integrar com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

O Stream Analytics do Azure é uma infraestrutura complexa, totalmente gerenciada para processamento e consumo de dados de eventos gerados por meio do hub de eventos do Azure.  A integração com o pool de SQL permite transmitir dados para serem efetivamente processados e armazenados junto com dados relacionais, permitindo análise mais profunda e avançada.  

* **Saída do trabalho:** envie a saída dos trabalhos do Stream Analytics diretamente para o pool SQL.

Para obter mais informações, consulte [Integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
