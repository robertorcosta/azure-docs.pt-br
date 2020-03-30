---
title: Visualização de dados do Azure Data Explorer
description: Conheça as diferentes maneiras que você pode visualizar seus dados do Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139055"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com o Azure Data Explorer 

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalável para dados de log e telemetria que é usado para construir soluções de análise complexas para grandes quantidades de dados. O Azure Data Explorer se integra com várias ferramentas de visualização, para que você possa visualizar seus dados e compartilhar os resultados em toda a sua organização. Esses dados podem ser transformados em insights acionáveis para causar impacto no seu negócio.

A visualização e o emissão de relatórios de dados é um passo fundamental no processo de análise de dados. O Azure Data Explorer suporta muitos serviços de BI para que você possa usar o que melhor se encaixa no seu cenário e orçamento.

## <a name="kusto-query-language-visualizations"></a>Visualizações de linguagem de consulta kusto

A linguagem [`render operator`](/azure/kusto/query/renderoperator) de consulta kusto oferece várias visualizações, como tabelas, gráficos de tortas e gráficos de barras para descrever os resultados da consulta. Visualizações de consultas são úteis na detecção e previsão de anomalias, aprendizado de máquina e muito mais.

## <a name="power-bi"></a>Power BI

O Azure Data Explorer oferece a capacidade de se conectar ao [Power BI](https://powerbi.microsoft.com) usando vários métodos: 

  * [Conector BI de energia nativo incorporado](/azure/data-explorer/power-bi-connector)

  * [Importação de consulta do Azure Data Explorer para o Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [consulta SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

O Azure Data Explorer oferece a capacidade de se conectar ao [Microsoft Excel](https://products.office.com/excel) usando o [conector excel nativo incorporado](excel-connector.md)ou importar uma [consulta](excel-blank-query.md) do Azure Data Explorer para o Excel.

## <a name="grafana"></a>Grafana

[O Grafana](https://grafana.com) fornece um plugin Azure Data Explorer que permite visualizar dados do Azure Data Explorer. Você [configura o Azure Data Explorer como fonte de dados para o Grafana e, em seguida, visualiza os dados](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

O Azure Data Explorer oferece a capacidade de se conectar ao [Kibana (a página Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) usando o K2Bridge, um conector de código aberto. Você [configura o Azure Data Explorer como uma fonte de dados para kibana e, em seguida, visualiza os dados](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Conector do ODBC

O Azure Data Explorer fornece um [conector ODBC (Open Database Connectivity)](connect-odbc.md) para que qualquer aplicativo que suporte o ODBC possa se conectar ao Azure Data Explorer.

## <a name="tableau"></a>Tableau

O Azure Data Explorer oferece a capacidade de se conectar ao [Tableau](https://www.tableau.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, [visualizar os dados no Tableau](tableau.md).

## <a name="qlik"></a>Qlik

O Azure Data Explorer fornece a capacidade de se conectar ao [Qlik](https://www.qlik.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, criar dashboards Qlik Sense e visualizar os dados. Usando o vídeo a seguir, você pode aprender a visualizar dados do Azure Data Explorer com Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

O Azure Data Explorer fornece a capacidade de se conectar ao [Sisense](https://www.sisense.com) usando o conector JDBC. Você [configura o Azure Data Explorer como fonte de dados para o Sisense e, em seguida, visualiza os dados](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Você pode usar [o Redash](https://redash.io/) para construir dashboards e visualizar dados. [Configure o Azure Data Explorer como fonte de dados para o Redash e, em seguida, visualize os dados](/azure/data-explorer/redash).