---
title: Logs do Monitor do Azure com um aplicativo multilocatário
description: Configure e use logs do Azure Monitor com um aplicativo SaaS de banco de dados Azure Multitenant
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214500"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurar e usar logs do Azure Monitor com um aplicativo SQL Database SaaS multilocatário

Neste tutorial, você configura e usa [registros do Monitor Do Azure](/azure/log-analytics/log-analytics-overview) para monitorar piscinas elásticas e bancos de dados. Este tutorial baseia-se no [tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md). Ele mostra como usar os registros do Azure Monitor para aumentar o monitoramento e o alerta fornecidos no portal Azure. Os registros do Azure Monitor suportam o monitoramento de milhares de piscinas elásticas e centenas de milhares de bancos de dados. Os logs do Azure Monitor fornecem uma única solução de monitoramento, que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure em várias assinaturas do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instale e configure registros do Monitor Azure.
> * Use os registros do Monitor do Azure para monitorar pools e bancos de dados.

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo de banco de dados por locatário SaaS Wingtip Tickets é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte o [Tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS, e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitore e gerencie o desempenho do banco de dados e do pool elástico com registros do Monitor Do Azure

Para o Banco de Dados SQL do Azure, o monitoramento e o alerta estão disponíveis em bancos de dados e pools no portal do Azure. Esse monitoramento e alertas internos são convenientes, mas também específicos do recurso. Isso significa que são adequados para monitorar grandes instalações ou fornecer uma exibição unificada de recursos e assinaturas.

Para cenários de alto volume, você pode usar registros do Monitor Do Azure para monitoramento e alerta. O Azure Monitor é um serviço separado do Azure que permite análises sobre logs reunidos em um espaço de trabalho de serviços potencialmente muitos. Os logs do Azure Monitor fornecem uma linguagem de consulta incorporada e ferramentas de visualização de dados que permitem análise seletiva de dados operacionais. A solução Análise do SQL fornece várias exibições e consultas de monitoramento e alertas de pools elásticos e bancos de dados. Os logs do Monitor do Azure também fornecem um designer de visualizações personalizado.

Os workspaces do OMS agora são chamados de workspaces do Log Analytics. Os workspaces do Log Analytics e as soluções de análise são abertas no portal do Azure. O portal do Azure é o ponto de acesso mais novo, mas pode ser o que está por trás do portal do Operations Management Suite em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Criar dados de diagnóstico de desempenho simulando uma carga de trabalho em seus locatários 

1. No PowerShell ISE, abra *.. WingtipTicketsSaaS-MultiTenantDb-master\\Módulos\\de\\aprendizagem Monitoramento e Gerenciamento Demo-PerformanceMonitoringAndManagement.ps1 . \\* Mantenha esse script aberto, pois você talvez queira executar vários dos cenários de geração de carga durante este tutorial.
1. Se você já não fez isso, provisione um lote de locatários para tornar o contexto de monitoramento mais interessante. Esse processo leva alguns minutos.

   a. Definir **$DemoScenario = 1**, _Provisão de um lote de inquilinos_.

   b. Para executar o script e implantar mais 17 locatários, pressione F5.

1. Agora, inicie o gerador de carga para executar uma carga simulada em todos os locatários.

    a. Conjunto **$DemoScenario = 2**, Gerar carga de intensidade normal _(aproximadamente 30 DTU)_.

    b. Para executar o script, pressione F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo de banco de dados por locatário SaaS Wingtip Tickets

Os scripts de banco de dados multilocatários Wingtip Tickets SaaS e o código-fonte do aplicativo estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Para obter as etapas para fazer o download e desbloquear os scripts de PowerShell do Wingtip Tickets, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instale e configure o espaço de trabalho do Log Analytics e a solução Azure SQL Analytics

O Azure Monitor é um serviço separado que deve ser configurado. Os logs do Azure Monitor coletam dados de log, telemetria e métricas em um espaço de trabalho do Log Analytics. Assim como outros recursos no Azure, o espaço de trabalho do Log Analytics deve ser criado. O workspace não precisa ser criado no mesmo grupo de recursos que os aplicativos que ele está monitorando. Mas isso geralmente faz mais sentido. Para o aplicativo Wingtip Tickets, use um único grupo de recursos para garantir que o workspace seja excluído com o aplicativo.

1. No PowerShell ISE, abra *.. WingtipTicketsSaaS-MultiTenantDb-master\\Módulos\\de aprendizagem Monitoramento\\de desempenho e análise de log de gerenciamento\\Demo-LogAnalytics.ps1 . \\*
1. Para executar o script, pressione F5.

Agora você pode abrir os registros do Azure Monitor no portal Azure. Leva alguns minutos para coletar a telemetria no espaço de trabalho do Log Analytics e para torná-la visível. Quanto mais tempo você deixa o sistema coletando dados, mais interessante é a experiência. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Use o espaço de trabalho do Log Analytics e a solução SQL Analytics para monitorar pools e bancos de dados


Neste exercício, abra o espaço de trabalho log analytics no portal Azure para olhar a telemetria recolhida para os bancos de dados e pools.

1. Navegue até [o portal Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir o espaço de trabalho do Log Analytics. Em seguida, pesquise por Log Analytics.

   ![Abrir espaço de trabalho do Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o workspace chamado _wtploganalytics-&lt;user&gt;_.

1. Selecione **Visão geral** para abrir a solução de análise de log no portal Azure.

   ![Visão geral](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode demorar alguns minutos até que a solução esteja ativa. 

1. Selecione o bloco **Análise de SQL do Azure** para abri-lo.

    ![Bloco Visão Geral](media/saas-dbpertenant-log-analytics/overview.png)

1. Os modos de exibição na solução rolam lateralmente, com sua própria barra de rolagem interna na parte inferior. Se necessário, atualize a página.

1. Para explorar a página de resumo, selecione os blocos ou bancos de dados individuais para abrir um gerenciador de detalhes.

    ![Painel de análise de log](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Altere a configuração de filtro para modificar o intervalo de tempo. Para este tutorial, selecione **Última 1 hora**.

    ![Filtro de tempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione um banco de dados individual para explorar o uso de consulta e a métrica para esse banco de dados.

    ![Análise de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver a métrica de uso, role a página de análise para a direita.
 
     ![Métrica de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Role a página de análise para a esquerda e selecione o bloco do servidor na lista **Informações do Recurso**.  

    ![Lista de Informações do Recursos](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Uma página é aberta mostrando os pools e os bancos de dados no servidor.

    ![Servidor com pools e bancos de dados](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecione um pool. Na página do pool que é aberta, role para a direita a fim de ver as métricas do pool. 

    ![Métricas do pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Voltando para o espaço de trabalho do Log Analytics, selecione **Portal do OMS** para abrir o espaço de trabalho nele.

    ![Espaço de trabalho do Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No espaço de trabalho do Log Analytics, você poderá explorar ainda mais os dados de métrica e log. 

O monitoramento e o alerta nos logs do Azure Monitor são baseados em consultas sobre os dados no espaço de trabalho, ao contrário do alerta definido em cada recurso no portal Azure. Baseando alertas em consultas, você pode definir um único alerta sobre todos os bancos de dados em vez de uma definição para cada banco de dados. As consultas são limitadas apenas pelos dados disponíveis no workspace.

Para obter mais informações sobre como usar os logs do Monitor do Azure para consultar e definir alertas, consulte [Trabalhar com regras de alerta nos registros do Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Os registros do Monitor do Azure para as taxas do Banco de Dados SQL com base no volume de dados no espaço de trabalho. Nesse tutorial, você criou um workspace gratuito, que é limitado a 500 MB por dia. Após esse limite ser atingido, os dados não são mais adicionados ao workspace.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Instale e configure registros do Monitor Azure.
> * Use os registros do Monitor do Azure para monitorar pools e bancos de dados.

Experimente o [Tutorial de análise de locatário](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo de banco de dados por locatário Saas Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Logs do Azure Monitor](../azure-monitor/insights/azure-sql.md)
