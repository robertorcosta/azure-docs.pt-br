---
title: Azure Monitor fontes de dados de livros de trabalho | Docs da Microsoft
description: Simplifique relatórios complexos com workbooks adiadores do Monitor Azure pré-construídos e personalizados construídos a partir de várias fontes de dados
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248574"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados dos livros de trabalho do Azure Monitor

As atos de trabalho são compatíveis com um grande número de fontes de dados. Este artigo irá levá-lo através de fontes de dados que estão atualmente disponíveis para as abanuais do Azure Monitor.

## <a name="logs"></a>Logs

As câmaras de trabalho permitem consultar logs das seguintes fontes:

* Logs do Monitor do Azure (Recursos de Insights de Aplicativos e Espaços de Trabalho do Log Analytics.)
* Dados centrados em recursos (registros de atividade)

Os autores da carteira de trabalho podem usar consultas KQL que transformam os dados de recursos subjacentes para selecionar um conjunto de resultados que pode ser visualizado como texto, gráficos ou grades.

![Captura de tela da interface de relatório de registros de livros de trabalho](./media/workbooks-overview/logs.png)

Os autores de livros de trabalho podem facilmente consultar vários recursos criando uma experiência de relatório selada verdadeiramente unificada.

## <a name="metrics"></a>Métricas

Os recursos do Azure emitem [métricas](data-platform-metrics.md) que podem ser acessadas através de livros de trabalho. As métricas podem ser acessadas em livros de trabalho através de um controle especializado que permite especificar os recursos de destino, as métricas desejadas e sua agregação. Esses dados podem então ser plotados em gráficos ou grades.

![Captura de tela dos gráficos de métricas da carteira de trabalho da utilização da CPU](./media/workbooks-overview/metrics-graph.png)

![Captura de tela da interface de métricas da carteira de trabalho](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Gráfico de Recursos do Azure 

As obras de trabalho suportam a consulta de recursos e seus metadados usando o Azure Resource Graph (ARG). Essa funcionalidade é usada principalmente para criar escopos de consulta personalizados para relatórios. O escopo de recursos é expresso através de um subconjunto KQL que o ARG suporta – o que muitas vezes é suficiente para casos de uso comum.

Para fazer um controle de consulta, use essa fonte de dados, use o tipo de inserção do tipo Consulta para escolher o Gráfico de Recursos do Azure e selecione as assinaturas a destino. Use o controle de consulta para adicionar o subconjunto ARG KQL que seleciona um subconjunto de recursos interessante.


![Captura de tela da consulta KQL do Gráfico de Recursos do Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alertas (visualização)

As obras permitem que os usuários visualizem os alertas ativos relacionados aos seus recursos. Esse recurso permite a criação de relatórios que reúnem dados de notificação (alerta) e informações diagnósticas (métricas, logs) em um relatório. Essas informações também podem ser unidas para criar relatórios ricos que combinam insights entre essas fontes de dados.

Para fazer um controle de consulta, use essa fonte de dados, use o tipo de inserção do tipo Consulta para escolher Alertas e selecionar as assinaturas, grupos de recursos ou recursos para segmentar. Use as gotas do filtro de alerta para selecionar um subconjunto interessante de alertas para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Saúde da carga de trabalho (visualização)

O Azure Monitor possui funcionalidade que monitora proativamente a disponibilidade e o desempenho dos sistemas operacionais convidados windows ou Linux. O Azure Monitor modela os principais componentes e suas relações, critérios de como medir a saúde desses componentes e quais componentes alertam quando uma condição insalubre é detectada. Os workbooks permitem que os usuários usem essas informações para criar relatórios interativos ricos.

Para fazer um controle de consulta, use essa fonte de dados, use o **tipo de queda** de consulta para escolher a Workload Health e selecionar recursos de assinatura, grupo de recursos ou VM para segmentar. Use as gotas do filtro de saúde para selecionar um subconjunto interessante de incidentes de saúde para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Saúde de recursos do Azure 

As obras de trabalho suportam a obtenção de recursos de saúde do Azure e a combiná-lo com outras fontes de dados para criar relatórios de saúde ricos e interativos

Para fazer um controle de consulta, use essa fonte de dados, use o **tipo de query** para baixo para escolher a saúde do Azure e selecione os recursos a serem direcionados. Use as gotas do filtro de saúde para selecionar um subconjunto interessante de problemas de recursos para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (visualização)

As adias agora têm suporte para consultas dos clusters [do Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto.](https://docs.microsoft.com/azure/kusto/query/index)   

![Captura de tela da janela de consulta do Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
