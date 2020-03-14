---
title: Monitoramento e ajuste de desempenho
description: Uma visão geral dos recursos de monitoramento e ajuste de desempenho e metodologia no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268724"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitoramento e ajuste de desempenho no banco de dados SQL do Azure

Para monitorar o desempenho de um banco de dados no banco de dados SQL do Azure, comece monitorando os recursos de CPU e e/s usados pela sua carga de trabalho em relação ao nível de desempenho do banco de dados escolhido na seleção de uma camada de serviço e nível de desempenho específicos. Para fazer isso, o banco de dados SQL do Azure emite métricas de recursos que podem ser exibidas no portal do Azure ou usando uma destas ferramentas de gerenciamento do SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Para bancos de dados individuais e em pool, o banco de dados SQL do Azure fornece uma série de consultores de banco de dados para fornecer recomendações de ajuste de desempenho inteligente e opções de ajuste automático para melhorar o desempenho. Além disso, Análise de Desempenho de Consultas mostra detalhes sobre as consultas responsáveis pela maior parte da CPU e do uso de e/s para bancos de dados individuais e em pool.

O banco de dados SQL do Azure fornece recursos adicionais de monitoramento e ajuste apoiados por inteligência artificial para ajudá-lo a solucionar problemas e maximizar o desempenho de seus bancos de dados e soluções. Você pode optar por configurar a [exportação de streaming](sql-database-metrics-diag-logging.md) desses [Intelligent insights](sql-database-intelligent-insights.md) e outros logs de recursos do banco de dados SQL e métricas para um dos vários destinos para consumo e análise, particularmente usando a [análise de SQL](../azure-monitor/insights/azure-sql.md)). Análise de SQL do Azure é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de todos os seus bancos de dados SQL do Azure em escala e em várias assinaturas em uma única exibição. Para obter uma lista dos logs e das métricas que você pode exportar, confira [telemetria de diagnóstico para exportação](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Por fim, o SQL tem seus próprios recursos de monitoramento e diagnóstico com [SQL Server repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [DMVs (exibições de gerenciamento dinâmico)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consulte [monitoramento usando DMVs](sql-database-monitoring-with-dmvs.md) para ver scripts para monitorar uma variedade de problemas de desempenho.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Recursos de monitoramento e ajuste no portal do Azure

No portal do Azure, o banco de dados SQL do Azure fornece monitoramento de métricas de recurso para todos os tipos de implantação. Adicional, para bancos de dados individuais e em pool, os consultores e Análise de Desempenho de Consultas de banco de dados fornecem recomendações de ajuste de consulta e análise de desempenho de consultas. Por fim, no portal do Azure, você pode habilitar automático para servidores lógicos e seus bancos de dados individuais e em pool.

### <a name="sql-database-resource-monitoring"></a>Monitoramento de recursos do banco de dados SQL

Você pode monitorar rapidamente as seguintes métricas de recurso no portal do Azure na exibição de **métricas** :

- **Uso de DTU**

  Verifique se um banco de dados ou pool elástico está atingindo 100% do uso de DTU por um longo período de tempo. O alto uso de DTU indica que sua carga de trabalho pode precisar de mais recursos de CPU ou de e/s. Ele também pode indicar consultas que precisam ser otimizadas.
- **Uso da CPU**

  Verifique se um banco de dados, um pool elástico ou uma instância gerenciada está atingindo 100% do uso da CPU por um longo período de tempo. Alta CPU indica que sua carga de trabalho pode precisar de mais recursos de CPU ou de e/s. Ele também pode indicar consultas que precisam ser otimizadas.
- **Uso de e/s**

  Verifique se uma instância de banco de dados, pool elástico ou gerenciar está atingindo os limites de e/s do armazenamento subjacente. Alto uso de e/s indica que sua carga de trabalho pode precisar de mais recursos de CPU ou e/s. Ele também pode indicar consultas que precisam ser otimizadas.

  ![Métricas do recurso](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Consultores de banco de dados
' O banco de dados SQL do Azure inclui [consultores de banco de dados](sql-database-advisor.md) que fornecem recomendações de ajuste de desempenho para bancos únicos e em pool. Essas recomendações estão disponíveis na portal do Azure, bem como usando o [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Você também pode habilitar o [ajuste automático](sql-database-automatic-tuning.md) para que o banco de dados SQL possa implementar automaticamente essas recomendações de ajuste.

### <a name="query-performance-insight"></a>Análise de Desempenho de Consultas

[Análise de desempenho de consultas](sql-database-query-performance.md) mostra o desempenho na portal do Azure de consultas mais longas e de execução mais longa para bancos de dados individuais e em pool.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Gerar avaliações inteligentes de problemas de desempenho

O banco de dados SQL do Azure [Intelligent insights](sql-database-intelligent-insights.md) usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho. O Intelligent Insights detecta automaticamente os problemas de desempenho com bancos de dados no Azure SQL Database com base em tempos de espera de execução de consulta, erros ou tempos limite. Depois de detectada, é executada uma análise detalhada que gera um log de recursos (chamado sqlsights) com uma [avaliação inteligente dos problemas](sql-database-intelligent-insights-troubleshoot-performance.md). Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho.

O Intelligent Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Habilitar a exportação de streaming de métricas e logs de recursos

Você pode habilitar e configurar a [exportação de streaming da telemetria de diagnóstico](sql-database-metrics-diag-logging.md) para um dos vários destinos, incluindo o log de recursos Intelligent insights. Use a [análise de SQL](../azure-monitor/insights/azure-sql.md) e outros recursos para consumir essa telemetria de diagnóstico adicional para identificar e resolver problemas de desempenho.

Você define as configurações de diagnóstico para as categorias de fluxo de métricas e logs de recursos para bancos de dados individuais, bancos de dados em pool, pools elásticos, instâncias gerenciadas e bancos de dados de instância para um dos recursos do Azure a seguir.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics espaço de trabalho no Azure monitor

Você pode transmitir métricas e logs de recursos para um [espaço de trabalho log Analytics no Azure monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Os dados transmitidos aqui podem ser consumidos pela [análise do SQL](../azure-monitor/insights/azure-sql.md), que é uma solução de monitoramento somente na nuvem que fornece monitoramento inteligente de seus bancos de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitoramento coletados e também permitem que você aproveite outros recursos do Azure Monitor, como alertas e visualizações.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

Você pode transmitir métricas e logs de recursos para os [hubs de eventos do Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Streaming de telemetria de diagnóstico para hubs de eventos para fornecer a seguinte funcionalidade:

- **Transmitir logs para sistemas de registro em log e telemetria de terceiros**

  Transmita todas as suas métricas e logs de recursos para um único Hub de eventos para canalizar dados de log para uma ferramenta de SIEM ou log Analytics de terceiros.
- **Criar uma plataforma de registro em log e telemetria personalizada**

  A natureza de publicação-assinatura altamente escalonável dos hubs de eventos permite que você inscreva com flexibilidade as métricas e os logs de recursos em uma plataforma de telemetria personalizada. Consulte [projetando e dimensionando uma plataforma de telemetria de escala global nos hubs de eventos do Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.
- **Exibir a integridade do serviço transmitindo dados para Power BI**

  Use os hubs de eventos, Stream Analytics e Power BI para transformar seus dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Consulte [Stream Analytics e Power bi: um painel de análise em tempo real para dados de streaming](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) para obter detalhes sobre essa solução.

### <a name="azure-storage"></a>Armazenamento do Azure

Transmita métricas e logs de recursos para o [armazenamento do Azure](../azure-monitor/platform/resource-logs-collect-storage.md). Use o armazenamento do Azure para arquivar grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Usar eventos estendidos no mecanismo do banco de dados SQL

Além disso, você pode usar [eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) no SQL para monitoramento avançado e solução de problemas adicionais. A arquitetura de eventos estendidos permite que os usuários coletem tantos ou menos dados necessários para solucionar ou identificar um problema de desempenho. Para obter informações sobre como usar eventos estendidos no banco de dados SQL, consulte [eventos estendidos no banco de dados SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre recomendações de desempenho inteligente para bancos de dados individuais e em pool, consulte [recomendações de desempenho do Database Advisor](sql-database-advisor.md).
- Para mais informações sobre monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho, consulte [Intelligent Insights do SQL Azure](sql-database-intelligent-insights.md).
'''''''''