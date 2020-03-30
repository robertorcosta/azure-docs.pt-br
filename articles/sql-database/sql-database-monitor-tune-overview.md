---
title: Monitoramento e ajuste de desempenho
description: Uma visão geral dos recursos de monitoramento e ajuste de desempenho e metodologia no Banco de Dados SQL do Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268724"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitoramento e ajuste de desempenho no Banco de Dados SQL do Azure

Para monitorar o desempenho de um banco de dados no Banco de Dados SQL do Azure, comece monitorando os recursos de CPU e IO usados pela sua carga de trabalho em relação ao nível de desempenho do banco de dados escolhido na seleção de um determinado nível de serviço e nível de desempenho. Para isso, o Azure SQL Database emite métricas de recursos que podem ser visualizadas no portal do Azure ou usando uma dessas ferramentas de gerenciamento de SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Para bancos de dados únicos e agrupados, o Azure SQL Database fornece uma série de Consultores de Banco de Dados para fornecer recomendações inteligentes de ajuste de desempenho e opções automáticas de ajuste para melhorar o desempenho. Além disso, o Query Performance Insight mostra detalhes sobre as consultas responsáveis pelo maior uso de CPU e IO para bancos de dados únicos e agrupados.

O Azure SQL Database fornece recursos adicionais de monitoramento e ajuste apoiados por inteligência artificial para ajudá-lo na solução de problemas e maximização do desempenho de seus bancos de dados e soluções. Você pode optar por configurar a [exportação de streaming](sql-database-metrics-diag-logging.md) desses [Intelligent Insights](sql-database-intelligent-insights.md) e outros registros e métricas de recursos do SQL Database para um dos vários destinos para consumo e análise, particularmente usando [o SQL Analytics](../azure-monitor/insights/azure-sql.md)). O Azure SQL Analytics é uma solução avançada de monitoramento em nuvem para monitorar o desempenho de todos os seus bancos de dados SQL do Azure em escala e em várias assinaturas em uma única exibição. Para obter uma lista dos registros e métricas que você pode exportar, consulte [a telemetria diagnóstica para exportação](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Finalmente, o SQL tem seus próprios recursos de monitoramento e diagnóstico com [a SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [as visualizações de gerenciamento dinâmico (DMVs).](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) Consulte [Monitorando usando DMVs](sql-database-monitoring-with-dmvs.md) para scripts para monitorar uma variedade de problemas de desempenho.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Recursos de monitoramento e ajuste no portal Azure

No portal Azure, o Banco de Dados SQL do Azure fornece monitoramento de métricas de recursos para todos os tipos de implantação. Além disso, para bancos de dados únicos e agrupados, os consultores de banco de dados e o Query Performance Insight fornecem recomendações de ajuste de consulta e análise de desempenho de consulta. Finalmente, no portal Azure, você pode habilitar automaticamente para servidores lógicos e seus bancos de dados únicos e agrupados.

### <a name="sql-database-resource-monitoring"></a>Monitoramento de recursos do Banco de Dados SQL

Você pode monitorar rapidamente as seguintes métricas de recursos no portal Azure na exibição **Métricas:**

- **Uso de DTU**

  Verifique se um banco de dados ou um pool elástico está atingindo 100% do uso de DTU por um longo período de tempo. O alto uso do DTU indica que sua carga de trabalho pode precisar de mais recursos de CPU ou IO. Também pode indicar consultas que precisam ser otimizadas.
- **Uso da CPU**

  Verifique se um banco de dados, um pool elástico ou uma instância gerenciada estão atingindo 100% do uso da CPU por um longo período de tempo. A ALTA CPU indica que sua carga de trabalho pode precisar de mais recursos de CPU ou IO. Também pode indicar consultas que precisam ser otimizadas.
- **Uso de IO**

  Verifique se um banco de dados, um pool elástico ou uma instância de gerenciamento estão atingindo os limites de IO do armazenamento subjacente. O alto uso de IO indica que sua carga de trabalho pode precisar de mais recursos de CPU ou IO. Também pode indicar consultas que precisam ser otimizadas.

  ![Métricas do recurso](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Consultores de banco de dados
' O Banco de Dados SQL do Azure inclui [consultores de banco de dados](sql-database-advisor.md) que fornecem recomendações de ajuste de desempenho para bancos de dados únicos e agrupados. Essas recomendações estão disponíveis no portal Azure, bem como usando [o PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Você também pode [habilitar ajuste automático para](sql-database-automatic-tuning.md) que o Banco de Dados SQL possa implementar automaticamente essas recomendações de ajuste.

### <a name="query-performance-insight"></a>Análise de Desempenho de Consultas

[O Query Performance Insight](sql-database-query-performance.md) mostra o desempenho no portal Azure de consultas de alto consumo e execução mais longas para bancos de dados únicos e agrupados.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Gerar avaliações inteligentes de problemas de desempenho

O Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) usa inteligência incorporada para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos disruptivos que causam baixo desempenho. O Intelligent Insights detecta automaticamente problemas de desempenho com bancos de dados no Banco de Dados SQL do Azure com base em tempos de espera, erros ou intervalos de tempo de execução de consulta. Uma vez detectada, uma análise detalhada é realizada que gera um registro de recursos (chamado SQLInsights) com uma [avaliação inteligente dos problemas](sql-database-intelligent-insights-troubleshoot-performance.md). Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho.

O Intelligent Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Habilite a exportação de streaming de métricas e registros de recursos

Você pode habilitar e configurar a [exportação de streaming de telemetria de diagnóstico](sql-database-metrics-diag-logging.md) para um dos vários destinos, incluindo o registro de recursos intelligent Insights. Use [o SQL Analytics](../azure-monitor/insights/azure-sql.md) e outros recursos para consumir essa telemetria de diagnóstico adicional para identificar e resolver problemas de desempenho.

Você configura configura configurações de diagnóstico para transmitir categorias de métricas e registros de recursos para bancos de dados únicos, bancos de dados agrupados, pools elásticos, instâncias gerenciadas e bancos de dados de instâncias para um dos seguintes recursos do Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Espaço de trabalho do Log Analytics no monitor Azure

Você pode transmitir métricas e logs de recursos para um [espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Os dados transmitidos aqui podem ser consumidos pelo [SQL Analytics](../azure-monitor/insights/azure-sql.md), que é uma solução de monitoramento somente em nuvem que fornece monitoramento inteligente de seus bancos de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitoramento coletados e também permitem que você aproveite outros recursos do Azure Monitor, como alertas e visualizações.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

Você pode transmitir métricas e registros de recursos para [o Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Streaming de telemetria de diagnóstico para hubs de eventos para fornecer as seguintes funcionalidades:

- **Stream logs para sistemas de registro e telemetria de terceiros**

  Transmita todas as suas métricas e registros de recursos para um único hub de eventos para canalizar dados de log para uma ferramenta de análise de log de terceiros.
- **Construa uma plataforma personalizada de telemetria e registro**

  A natureza altamente escalável de assinatura de publicações de hubs de eventos permite que você ingera métricas e logins de recursos de forma flexível em uma plataforma de telemetria personalizada. Consulte [Projetando e Dimensionando uma plataforma de telemetria em escala global no Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.
- **Ver serviço de saúde transmitindo dados para Power BI**

  Use hubs de eventos, stream analytics e power bi para transformar seus dados de diagnóstico em insights quase em tempo real sobre seus serviços do Azure. Consulte [Stream Analytics e Power BI: Um painel de análise em tempo real para transmitir dados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) para obter detalhes sobre esta solução.

### <a name="azure-storage"></a>Armazenamento do Azure

Fluxo de métricas e registros de recursos para [o Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Use o armazenamento do Azure para arquivar grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Use eventos estendidos no mecanismo de banco de dados SQL

Além disso, você pode usar [eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) no SQL para monitoramento avançado adicional e solução de problemas. A arquitetura de eventos estendidos permite que os usuários coletem tanto ou tão poucos dados quanto necessário para solucionar problemas ou identificar um problema de desempenho. Para obter informações sobre o uso de eventos estendidos no Banco de Dados SQL, consulte [Eventos estendidos no Banco de Dados SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre recomendações inteligentes de desempenho para bancos de dados únicos e agrupados, consulte [recomendações de desempenho do orientador do banco de dados](sql-database-advisor.md).
- Para mais informações sobre monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho, consulte [Intelligent Insights do SQL Azure](sql-database-intelligent-insights.md).
'''''''''