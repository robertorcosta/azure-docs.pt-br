---
title: Visualização de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes maneiras de visualizar seus dados de Data Explorer do Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139055"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com o Azure Data Explorer 

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria usados para criar soluções de análise complexas para grandes quantidades de dados. O Azure Data Explorer integra-se com várias ferramentas de visualização para que você possa visualizar seus dados e compartilhar os resultados em sua organização. Esses dados podem ser transformados em informações acionáveis para causar um impacto na sua empresa.

A visualização e o relatório de dados é uma etapa crítica no processo de análise de dados. O Azure Data Explorer dá suporte a muitos serviços de BI para que você possa usar aquele que melhor se adapta ao seu cenário e orçamento.

## <a name="kusto-query-language-visualizations"></a>Visualizações de linguagem de consulta Kusto

A linguagem de consulta Kusto [`render operator`](/azure/kusto/query/renderoperator) oferece várias visualizações, como tabelas, gráficos de pizza e gráficos de barras, para representar os resultados da consulta. As visualizações de consulta são úteis em detecção de anomalias e previsão, aprendizado de máquina e muito mais.

## <a name="power-bi"></a>Power BI

O Azure Data Explorer fornece a capacidade de se conectar a [Power bi](https://powerbi.microsoft.com) usando vários métodos: 

  * [Conector interno de Power BI nativo](/azure/data-explorer/power-bi-connector)

  * [Importação de consulta do Azure Data Explorer para o Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

O Azure Data Explorer fornece a capacidade de se conectar ao [Microsoft Excel](https://products.office.com/excel) usando o [conector interno do Excel nativo](excel-connector.md)ou [importar uma consulta](excel-blank-query.md) do Azure data Explorer para o Excel.

## <a name="grafana"></a>Grafana

O [Grafana](https://grafana.com) fornece um plug-in do data Explorer do Azure que permite visualizar dados do data Explorer do Azure. Você [configura o Azure data Explorer como uma fonte de dados para Grafana e, em seguida, visualiza os dados](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

O Azure Data Explorer fornece a capacidade de se conectar ao [Kibana (a página de descoberta) usando o](https://www.elastic.co/guide/en/kibana/6.8/discover.html) K2Bridge, um conector de software livre. Você [configura o Azure data Explorer como uma fonte de dados para Kibana e, em seguida, visualiza os dados](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Conector do ODBC

O Azure Data Explorer fornece um [conector ODBC (Open Database Connectivity)](connect-odbc.md) para que qualquer aplicativo que ofereça suporte a ODBC possa se conectar ao data Explorer do Azure.

## <a name="tableau"></a>Tableau

O Azure Data Explorer fornece a capacidade de se conectar ao [tableau](https://www.tableau.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, [Visualizar os dados no tableau](tableau.md).

## <a name="qlik"></a>Qlik

O Azure Data Explorer fornece a capacidade de se conectar ao [Qlik Sense](https://www.qlik.com) usando o [conector do ODBC](/azure/data-explorer/connect-odbc) e, em seguida, criar painéis do Qlik Sense Sense e visualizar os dados. Usando o vídeo a seguir, você pode aprender a Visualizar dados do Azure Data Explorer com o Qlik Sense. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

O Azure Data Explorer fornece a capacidade de se conectar ao [Sisense](https://www.sisense.com) usando o conector JDBC. Você [configura o Azure data Explorer como uma fonte de dados para Sisense e, em seguida, visualiza os dados](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Você pode usar a [barra intracejada](https://redash.io/) para criar painéis e Visualizar dados. [Configure o Azure data Explorer como uma fonte de dados para retraço e visualize os dados](/azure/data-explorer/redash).