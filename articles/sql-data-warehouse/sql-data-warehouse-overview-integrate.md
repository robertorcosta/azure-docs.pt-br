---
title: Crie soluções integradas
description: Ferramentas de solução e parceiros que se integram com um data warehouse provisionado com o SQL Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153291"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integre outros serviços com uma análise de SQL data warehouse 
O recurso de análise de SQL no Azure Synapse Analytics permite que os usuários se integrem a muitos dos outros serviços no Azure. Usando a análise de SQL, você pode criar um data warehouse por meio de seu recurso de pool do SQL, que pode utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Fábrica de dados do Azure
* Azure Machine Learning
* Stream Analytics do Azure

Para obter mais informações sobre o Integration Services no Azure, consulte o artigo [parceiros de integração](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
A integração do Power BI permite combinar a potência de computação de um data warehouse com a visualização e o relatório dinâmico de Power BI. Atualmente, a integração do Power BI inclui:

* Conexão **direta**: uma conexão mais avançada com aplicação lógica em relação A um data warehouse provisionado com o pool do SQL. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abrir no Power bi**: o botão ' abrir no Power Bi ' passa informações da instância para Power bi de uma maneira simplificada de se conectar.

Para obter mais informações, consulte [Integrar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou a [Documentação do Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Fábrica de dados do Azure
O Azure Data Factory oferece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração do pool do SQL com o Azure Data Factory inclui:

* **Procedimentos armazenados**: orquestrar a execução de procedimentos armazenados.
* **Copiar**: Use o ADF para mover dados para o pool do SQL. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou PolyBase nos bastidores. 

Para obter mais informações, consulte [Integrar com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço de análise totalmente gerenciado que permite criar modelos complexos utilizando um grande conjunto de ferramentas de previsão. O pool SQL tem suporte como origem e destino para esses modelos e tem a seguinte funcionalidade:

* **Ler dados:** Impulsionar modelos em escala usando o T-SQL no pool do SQL.
* **Gravar dados:** Confirme as alterações de qualquer modelo de volta para o pool do SQL.

Para obter mais informações, consulte [Integrar com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure
O Stream Analytics do Azure é uma infraestrutura complexa, totalmente gerenciada para processamento e consumo de dados de eventos gerados por meio do hub de eventos do Azure.  A integração com o pool do SQL permite que os dados de streaming sejam efetivamente processados e armazenados juntamente com dados relacionais, permitindo uma análise mais profunda e mais avançada.  

* **Saída do trabalho:** Envie a saída de trabalhos do Stream Analytics diretamente para o pool do SQL.

Para obter mais informações, consulte [Integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


