---
title: Monitore os recursos do Azure com o Monitor Do Azure | Microsoft Docs
description: Descreve como coletar e analisar dados de monitoramento de recursos no Azure usando o Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249263"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorando os recursos do Azure com o Monitor Do Azure
Quando você tem aplicativos críticos e processos de negócios que dependem dos recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

> [!IMPORTANT]
> Este artigo se aplica a todos os serviços no Azure que usam o Azure Monitor. Os recursos de computação, incluindo VMs e App Service, geram os mesmos dados de monitoramento descritos aqui, mas também possuem um sistema operacional convidado que também pode gerar logs e métricas. Consulte a documentação de monitoramento desses serviços para obter detalhes sobre como coletar e analisar esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Monitor é um serviço de monitoramento de pilha completa no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e no local. A [plataforma de dados Do Azure Monitor](../platform/data-platform.md) coleta dados em [logs](../platform/data-platform-logs.md) e métricas onde eles podem ser [analisados](../platform/data-platform-metrics.md) em conjunto usando um conjunto completo de ferramentas de monitoramento, conforme descrito nas seções a seguir.

- [O que você pode fazer com o Azure Monitor Metrics?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [O que você pode fazer com o Azure Monitor Logs?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Assim que você cria um recurso do Azure, o Azure Monitor é ativado e começa a coletar métricas e registros de atividades que você pode [visualizar e analisar no portal Azure](#monitoring-in-the-azure-portal). Com alguma configuração, você pode coletar dados adicionais de monitoramento e habilitar recursos adicionais. Consulte [Os dados de monitoramento](#monitoring-data) abaixo para obter detalhes sobre quaisquer requisitos de configuração.


## <a name="costs-associated-with-monitoring"></a>Custos associados ao monitoramento
Não há custo para analisar dados de monitoramento que são coletados por padrão. Isso inclui o seguinte:

- Coletando métricas da plataforma e analisando-as com o explorador de métricas.
- Coletando registro de atividades e analisando-o no portal Azure.
- Criando uma regra de alerta de registro de atividade.

Não há custos do Azure Monitor para coleta e exportação de registros e métricas, mas pode haver custos relacionados associados ao destino:

- Custos associados à ingestão e retenção de dados ao coletar registros e métricas no espaço de trabalho do Log Analytics. Consulte [os preços do Monitor Do Azure para Análise de Log](https://azure.microsoft.com/pricing/details/monitor/).
- Custos associados ao armazenamento de dados ao coletar registros e métricas para uma conta de armazenamento do Azure. Consulte [os preços do Armazenamento Azure para armazenamento blob](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Custos associados ao streaming de hubs de eventos ao encaminhar logs e métricas para o Azure Event Hubs. Consulte [os preços do Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Pode haver custos do Azure Monitor associados aos seguintes. Veja [os preços do Monitor Do Azure:](https://azure.microsoft.com/pricing/details/monitor/)

- Executando uma consulta de log.
- Criando uma regra de alerta de consulta métrica ou log.
- Enviando uma notificação de qualquer regra de alerta.
- Acessando métricas através de API.

## <a name="monitoring-data"></a>Dados de monitoramento
Os recursos no Azure geram [logs](../platform/data-platform-logs.md) e [métricas mostrados](../platform/data-platform-metrics.md) no diagrama a seguir. Consulte a documentação de cada serviço do Azure para os dados específicos que eles geram e quaisquer soluções ou insights adicionais que eles fornecem.

![Visão geral](media/monitor-azure-resource/logs-metrics.png)



- [Métricas da plataforma](../platform/data-platform-metrics.md) - Valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. 
- [Logs de recursos](../platform/platform-logs-overview.md) - Forneça informações sobre operações que foram realizadas dentro de um recurso do Azure (o plano de dados), por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação a um banco de dados. O conteúdo e a estrutura dos registros de recursos variam de acordo com o tipo de serviço e recurso do Azure.
- [Registro de atividades](../platform/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (o plano de gerenciamento), por exemplo, criando um novo recurso ou iniciando uma máquina virtual. Esta é uma informação sobre o quê, quem, e quando para quaisquer operações de gravação (PUT, POST, DELETE) tomadas sobre os recursos em sua assinatura.


## <a name="configuration-requirements"></a>Requisitos de configuração

### <a name="configure-monitoring"></a>Configurar monitoramento
Alguns dados de monitoramento são coletados automaticamente, mas você pode precisar executar alguma configuração dependendo de suas necessidades. Consulte as informações abaixo para obter informações específicas para cada tipo de dados de monitoramento.

- [Métricas da plataforma](../platform/data-platform-metrics.md) - As métricas da plataforma são coletadas automaticamente no [Azure Monitor Metrics](../platform/data-platform-metrics.md) sem necessidade de configuração. Crie uma configuração de diagnóstico para enviar entradas para o Azure Monitor Logs ou para encaminhá-las fora do Azure.
- [Logs de recursos](../platform/platform-logs-overview.md) - Os logs de recursos são gerados automaticamente pelos recursos do Azure, mas não coletados sem uma configuração de diagnóstico.  Crie uma configuração de diagnóstico para enviar entradas para o Azure Monitor Logs ou para encaminhá-las fora do Azure.
- [Registro de atividades](../platform/platform-logs-overview.md) - O registro de atividades é coletado automaticamente sem necessidade de configuração e pode ser visualizado no portal Azure. Crie uma configuração de diagnóstico para copiá-los aos Logs do Monitor do Azure ou para encaminhá-los fora do Azure.

### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
A coleta de dados no Azure Monitor Logs requer um espaço de trabalho do Log Analytics. Você pode começar a monitorar seu serviço rapidamente criando um novo espaço de trabalho, mas pode haver valor em usar um espaço de trabalho que está coletando dados de outros serviços. Consulte [Criar um espaço de trabalho do Log Analytics no portal Azure](../learn/quick-create-workspace.md) para obter detalhes sobre a criação de um espaço de trabalho e projetar sua implantação de [Logs do Monitor do Azure](../platform/design-logs-deployment.md) para ajudar a determinar o melhor design de espaço de trabalho para suas necessidades. Se você usar um espaço de trabalho existente em sua organização, então você exigirá permissões apropriadas conforme descrito no [Gerenciar acesso a dados de log e espaços de trabalho no Azure Monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Configurações de Diagnóstico
As configurações de diagnóstico definem para onde devem ser enviados registros de recursos e métricas para um determinado recurso. Os possíveis destinos são:

- [O espaço de trabalho do Log Analytics](../platform/resource-logs-collect-workspace.md) permite analisar dados com outros dados de monitoramento coletados pelo Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos do Azure Monitor, como alertas de log e visualizações. 
- [Hubs de](../platform/resource-logs-stream-event-hubs.md) eventos para transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise de log. 
- [Conta de armazenamento do Azure](../platform/resource-logs-collect-storage.md) que é útil para auditoria, análise estática ou backup.

Siga o procedimento em [Criar configuração de diagnóstico para coletar registros e métricas da plataforma no Azure](../platform/diagnostic-settings.md) para criar e gerenciar as configurações de diagnóstico através do portal Azure. Consulte [Criar configuração de diagnóstico no Azure usando um modelo de Gerenciador de recursos](../platform/diagnostic-settings-template.md) para defini-los em um modelo e habilitar o monitoramento completo de um recurso quando ele for criado.


## <a name="monitoring-in-the-azure-portal"></a>Monitoramento no portal do Azure
 Você pode acessar dados de monitoramento para a maioria dos recursos do Azure no menu do recurso no portal Azure. Isso lhe dará acesso aos dados de um único recurso usando ferramentas padrão do Azure Monitor. Alguns serviços do Azure fornecerão diferentes opções, então você deve consultar a documentação desse serviço para obter informações adicionais. Use o **menu Do Monitor do Azure** para analisar dados de todos os recursos monitorados. 

### <a name="overview"></a>Visão geral
Muitos serviços incluirão dados de monitoramento na página de **Visão Geral** como uma visualização rápida da operação. Normalmente, isso será baseado em um subconjunto de métricas de plataforma armazenado nas Métricas do Azure Monitor. Outras opções de monitoramento estarão normalmente disponíveis em uma seção de **Monitoramento** dos serviços. .

![Página de visão geral](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insights e Soluções 
Alguns serviços fornecerão ferramentas além dos recursos padrão do Azure Monitor. [Os insights](../insights/insights-overview.md) fornecem uma experiência de monitoramento personalizada baseada na plataforma de dados do Azure Monitor e recursos padrão. [As soluções](../insights/solutions.md) fornecem lógica de monitoramento predefinida construída no Azure Monitor Logs. 

Se um serviço tiver uma visão do Azure Monitor, você pode acessá-lo a partir do **Monitoramento** no menu de cada recurso. Acesse todos os insights e soluções do menu **do Monitor Azure.**

![Insights](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Métricas
Analise métricas no portal Azure usando o [metrics explorer](../platform/metrics-getting-started.md) que está disponível no item de menu **Métricas** para a maioria dos serviços. Esta ferramenta permite que você trabalhe com métricas individuais ou combine várias para identificar correlações e tendências. 

- Consulte [Como começar com o Azure Metrics Explorer](../platform/metrics-getting-started.md) para o básico do explorador de métricas.
- Consulte [recursos avançados do Azure Metrics Explorer](../platform/metrics-charts.md) para recursos avançados do explorador de métricas, como usar várias métricas e aplicar filtros e dividir.

![Métricas](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Log de atividades 
Exibir entradas no registro de atividades no portal Azure com o filtro inicial definido para o recurso atual. Copie o registro de atividades em um espaço de trabalho do Log Analytics para acessá-lo para usá-lo em consultas de log e pastas de trabalho. 

- Veja [Exibir e recuperar eventos de registro de atividades do Azure](../platform/activity-log-view.md) para obter detalhes sobre a visualização do registro de atividades e a recuperação de entradas usando uma variedade de métodos.
- Consulte a documentação do seu serviço Azure para os eventos específicos que são registrados.

![Log de Atividade](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor
O Azure Monitor Logs consolida registros e métricas de vários serviços e outras fontes de dados para análise com uma poderosa ferramenta de consulta. Como descrito acima, crie uma configuração de diagnóstico para coletar métricas da plataforma, log de atividades e logins de recursos em um espaço de trabalho do Log Analytics no Azure Monitor.

[O Log Analytics](../log-query/get-started-portal.md) permite que você trabalhe com [consultas de log,](../log-query/log-query-overview.md)que é um recurso poderoso do Azure Monitor que permite que você execute análises avançadas de dados de log usando uma linguagem de consulta totalmente em destaque. Abra o Log Analytics do **Logs** no menu **'Monitorando'** um recurso do Azure para trabalhar com consultas de log usando o recurso como escopo de [consulta](../log-query/scope.md#query-scope). Isso permite analisar dados em várias tabelas para apenas esse recurso. Use **logs** do menu Do Monitor do Azure para acessar logs para todos os recursos. 

- Consulte [Iniciar com consultas de log no Azure Monitor](../log-query/get-started-queries.md) para obter um tutorial sobre como usar o idioma de consulta usado para escrever consultas de log.
- Consulte [os logs de recursos do Collect Azure no espaço de trabalho do Log Analytics no Azure Monitor](../platform/resource-logs-collect-workspace.md) para obter informações sobre como os logs de recursos são coletados nos logs do Monitor do Azure e detalhes de como acessá-los em uma consulta.
- Consulte [o modo Collection](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) para obter uma explicação de como os dados de registro de recursos estão estruturados nos Logs do Monitor do Azure.
- Consulte a documentação de cada serviço do Azure para obter detalhes sobre sua tabela no Azure Monitor Logs.

![Logs](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitoramento da linha de comando
Você pode acessar dados de monitoramento coletados de seu recurso a partir de uma linha de comando ou incluir em um script usando a Interface de Linha de Comando [Do Azure PowerShell](/powershell/azure/) ou [Azure](/cli/azure/). 

- Consulte [a referência de métricas CLI](/cli/azure/monitor/metrics) para acessar dados métricos da CLI.
- Consulte [a referência CLI Log Analytics](/cli/azure/ext/log-analytics/monitor/log-analytics) para acessar dados do Azure Monitor Logs usando uma consulta de log da CLI.
- Consulte [a referência de métricas do Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric) para acessar dados métricos do Azure PowerShell.
- Consulte [a referência de consulta de log do Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para acessar os dados do Azure Monitor Logs usando uma consulta de log do Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitoramento da API REST
Inclua dados de monitoramento coletados do seu recurso em um aplicativo personalizado usando uma API REST.

- Consulte o passo a passo do [API do Azure Monitoring REST](../platform/rest-api-walkthrough.md) para obter detalhes sobre o acesso às métricas da API Do Monitor Do Azure.
- Consulte [a Azure Log Analytics REST API](https://dev.loganalytics.io/) para obter informações sobre como acessar os dados do Azure Monitor Logs usando uma consulta de log do Azure PowerShell.

## <a name="alerts"></a>Alertas
[Os alertas](../platform/alerts-overview.md) notificam-no proativamente e potencialmente tomam medidas quando condições importantes são encontradas em seus dados de monitoramento. Você cria uma regra de alerta que define um alvo para o alerta, as condições para criar um alerta e quaisquer ações a serem tomadas em resposta.

Diferentes tipos de dados de monitoramento são usados para diferentes tipos de regras de alerta.

- [Alerta de registro de atividade](../platform/alerts-activity-log.md) - Crie um alerta quando uma entrada for criada no registro de atividades que corresponda a critérios específicos. Isso permite que você seja notificado, por exemplo, quando um determinado tipo de recurso for criado ou se uma alteração de configuração falhar.
- [Alerta métrico](../platform/alerts-metric.md) - Crie um alerta quando um valor métrico exceder um limite específico. Os alertas métricos são mais responsivos do que outros alertas e podem ser resolvidos automaticamente quando o problema é corrigido.
- [Alerta de consulta de log](../platform/alerts-log.md) - Execute uma consulta de log em intervalos regulares e crie um alerta se uma determinada condição for encontrada. Isso permite que você realize análises complexas em vários conjuntos de dados e .

Use **alertas** do menu de um recurso para exibir alertas e gerenciar regras de alerta para esse recurso. Apenas alertas de registro de atividade e alertas métricos usam recursos individuais do Azure como alvo. Os alertas de consulta de log usam um espaço de trabalho do Log Analytics como alvo e são baseados em uma consulta que pode acessar quaisquer logs armazenados nesse espaço de trabalho. Use o menu Azure Monitor para visualizar e gerenciar alertas para todos os recursos e as regras de alerta de consulta de log.

- Consulte os artigos para os diferentes tipos de alertas acima para obter detalhes sobre a criação de regras de alerta.
- Consulte [Criar e gerenciar grupos de ação no portal Azure](../platform/action-groups.md) para obter detalhes sobre a criação de um grupo de ação que permite gerenciar respostas a alertas.



## <a name="next-steps"></a>Próximas etapas

* Consulte [serviços, esquemas e categorias suportadas para logs de recursos do Azure](../platform/diagnostic-logs-schema.md) para obter detalhes de registros de recursos para diferentes serviços do Azure.  
