---
title: Faq monitor do Azure | Microsoft Docs
description: Respostas para perguntas frequentes sobre o Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: db63ce2d56eb78bf6b361d530511b6902c1cb6d5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637781"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Monitor do Azure perguntas frequentes

Este FaQ da Microsoft é uma lista de perguntas comumente feitas sobre o Azure Monitor.

## <a name="general"></a>Geral

### <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
[O Azure Monitor](overview.md) é um serviço no Azure que fornece monitoramento de desempenho e disponibilidade para aplicativos e serviços no Azure, em outros ambientes na nuvem ou no local. O Azure Monitor coleta dados de várias fontes em uma plataforma de dados comum, onde podem ser analisados para tendências e anomalias. Recursos ricos no Azure Monitor ajudam você a identificar e responder rapidamente a situações críticas que podem afetar sua aplicação.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual é a diferença entre o Azure Monitor, o Log Analytics e o Application Insights?
Em setembro de 2018, a Microsoft combinou o Azure Monitor, o Log Analytics e o Application Insights em um único serviço para fornecer um poderoso monitoramento de ponta a ponta de seus aplicativos e dos componentes em que eles confiam. Os recursos do Log Analytics e do Application Insights não foram alterados, embora alguns recursos tenham sido remarcados para O Monitor do Azure, a fim de refletir melhor seu novo escopo. O mecanismo de dados de log e a linguagem de consulta do Log Analytics agora são referidos como Logs do Monitor do Azure. Consulte [as atualizações da terminologia do Azure Monitor](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quanto custa o Azure Monitor?
Os recursos do Monitor Azure que são automaticamente habilitados, como coleta de métricas e registros de atividade, são fornecidos sem nenhum custo. Há um custo associado a outros recursos, como consultas de log e alerta. Consulte a página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter informações detalhadas sobre preços.

### <a name="how-do-i-enable-azure-monitor"></a>Como habilitar o Monitor Azure?
O Azure Monitor está habilitado no momento em que você cria uma nova assinatura do Azure e as métricas de registro de [atividades](platform/activity-logs-overview.md) e plataforma saem automaticamente [coletadas.](platform/data-platform-metrics.md) Crie [configurações de diagnóstico](platform/diagnostic-settings.md) para coletar informações mais detalhadas sobre o funcionamento de seus recursos do Azure e adicione [soluções](insights/solutions.md) de monitoramento e insights para fornecer análises adicionais sobre dados [coletados](insights/insights-overview.md) para determinados serviços. 

### <a name="how-do-i-access-azure-monitor"></a>Como acessar o Azure Monitor?
Acesse todos os recursos e dados do Azure Monitor no menu **Monitor** no portal Azure. A seção **Monitoramento** do menu para diferentes serviços do Azure fornece acesso às mesmas ferramentas com dados filtrados para um determinado recurso. Os dados do Azure Monitor também são acessíveis para uma variedade de cenários usando CLI, PowerShell e uma API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Existe uma versão no local do Azure Monitor?
Não. O Azure Monitor é um serviço de nuvem escalável que processa e armazena grandes quantidades de dados, embora o Azure Monitor possa monitorar recursos que estão no local e em outras nuvens.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>O Azure Monitor pode monitorar os recursos no local?
Sim, além de coletar dados de monitoramento dos recursos do Azure, o Azure Monitor pode coletar dados de máquinas virtuais e aplicativos em outras nuvens e no local. Consulte [Fontes de dados de monitoramento do Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>O Azure Monitor integra-se ao System Center Operations Manager?
Você pode conectar o grupo de gerenciamento de operações do System Center existente ao Azure Monitor para coletar dados de agentes no Azure Monitor Logs. Isso permite que você use consultas de log e solução para analisar dados coletados dos agentes. Você também pode configurar os agentes existentes do System Center Operations Manager para enviar dados diretamente ao Azure Monitor. Consulte [O Connect Operations Manager no Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Quais endereços IP o Azure Monitor usa?
Consulte [endereços IP usados pelo Application Insights e log analytics](app/ip-addresses.md) para obter uma listagem dos endereços IP e portas necessários para que os agentes e outros recursos externos acessem o Azure Monitor. 

## <a name="monitoring-data"></a>Dados de monitoramento

### <a name="where-does-azure-monitor-get-its-data"></a>Onde o Azure Monitor consegue seus dados?
O Azure Monitor coleta dados de várias fontes, incluindo logs e métricas da plataforma e recursos do Azure, aplicativos personalizados e agentes em execução em máquinas virtuais. Outros serviços como o Azure Security Center e o Network Watcher coletam dados em um espaço de trabalho do Log Analytics para que possam ser analisados com dados do Azure Monitor. Você também pode enviar dados personalizados para o Azure Monitor usando a API REST para logs ou métricas. Consulte [Fontes de dados de monitoramento do Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Quais dados são coletados pelo Azure Monitor? 
O Azure Monitor coleta dados de uma variedade de fontes em [logs](platform/data-platform-logs.md) ou [métricas](platform/data-platform-metrics.md). Cada tipo de dados tem suas próprias vantagens relativas, e cada um suporta um conjunto particular de recursos no Azure Monitor. Existe um banco de dados de métricas únicas para cada assinatura do Azure, enquanto você pode criar vários espaços de trabalho do Log Analytics para coletar logs dependendo de suas necessidades. Consulte [a plataforma de dados do Azure Monitor](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Existe uma quantidade máxima de dados que eu possa coletar no Azure Monitor?
Não há limite para a quantidade de dados métricos que você pode coletar, mas esses dados são armazenados por um máximo de 93 dias. Consulte [Retenção de Métricas](platform/data-platform-metrics.md#retention-of-metrics). Não há limite na quantidade de dados de log que você pode coletar, mas ele pode ser afetado pelo nível de preços que você escolher para o espaço de trabalho do Log Analytics. Veja [detalhes de preços](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Como acessar os dados coletados pelo Azure Monitor?
Insights e soluções fornecem uma experiência personalizada para trabalhar com dados armazenados no Azure Monitor. Você pode trabalhar diretamente com dados de log usando uma consulta de log escrita no Kusto Query Language (KQL). No portal Azure, você pode escrever e executar consultas e analisar interativamente dados usando o Log Analytics. Analise métricas no portal Azure com o Metrics Explorer. Consulte [Analisar dados de log no Azure Monitor](log-query/log-query-overview.md) e começar com o [Azure Metrics Explorer](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Soluções e insights

### <a name="what-is-an-insight-in-azure-monitor"></a>O que é uma visão no Azure Monitor?
Os insights fornecem uma experiência de monitoramento personalizada para determinados serviços do Azure. Eles usam as mesmas métricas e logs que outros recursos no Azure Monitor, mas podem coletar dados adicionais e fornecer uma experiência única no portal Azure. Veja [insights no Azure Monitor](insights/insights-overview.md).

Para ver insights no portal Azure, consulte a seção **Insights** do menu **Monitor** ou a seção **Monitoramento** do menu do serviço.

### <a name="what-is-a-solution-in-azure-monitor"></a>O que é uma solução no Azure Monitor?
As soluções de monitoramento são conjuntos de lógica empacotados para monitorar um determinado aplicativo ou serviço com base nos recursos do Azure Monitor. Eles coletam dados de log no Azure Monitor e fornecem consultas de log e visualizações para suas análises usando uma experiência comum no portal Azure. Consulte [soluções de monitoramento no Monitor Azure](insights/solutions.md).

Para ver soluções no portal Azure, clique em **Mais** na seção **Insights** do menu **Monitor.** Clique **em Adicionar** para adicionar soluções adicionais ao espaço de trabalho.

## <a name="logs"></a>Logs

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual é a diferença entre o Azure Monitor Logs e o Azure Data Explorer?
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Monitor Logs é construído em cima do Azure Data Explorer e usa a mesma Linguagem de Consulta kusto (KQL) com algumas pequenas diferenças. Consulte [as diferenças de idioma da consulta de log do Azure Monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Como recuperar dados de log?
Todos os dados são recuperados de um espaço de trabalho do Log Analytics usando uma consulta de log escrita usando o Kusto Query Language (KQL). Você pode escrever suas próprias consultas ou usar soluções e insights que incluem consultas de log para um determinado aplicativo ou serviço. Consulte [visão geral das consultas de log no Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>O que é um workspace do Log Analytics?
Todos os dados de log coletados pelo Azure Monitor são armazenados em um espaço de trabalho do Log Analytics. Um espaço de trabalho é essencialmente um contêiner onde os dados de registro são coletados de uma variedade de fontes. Você pode ter um único espaço de trabalho do Log Analytics para todos os seus dados de monitoramento ou pode ter requisitos para vários espaços de trabalho. Consulte [Projetar a implantação do Azure Monitor Logs](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Você pode mover um espaço de trabalho existente do Log Analytics para outra assinatura do Azure?
Você pode mover um espaço de trabalho entre grupos de recursos ou assinaturas, mas não para uma região diferente. Consulte [Mover um espaço de trabalho do Log Analytics para diferentes grupos de assinatura ou recursos](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Por que não consigo ver os botões Do Explorador de Consulta e Salvar no Log Analytics?

Os botões de **regra** de consulta **Explorer,** **Save** e New alert não estão disponíveis quando o [escopo da consulta](log-query/scope.md) é definido como um recurso específico. Para criar alertas, salvar ou carregar uma consulta, o Log Analytics deve ser escopo para um espaço de trabalho. Para abrir o Log Analytics no contexto do espaço de trabalho, **selecione Logs** no menu Do Monitor do **Azure.** O último workspace usado é selecionado, mas você pode selecionar qualquer outro workspace. Consulte [o escopo de consulta de log e o intervalo de tempo no Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Por que estou recebendo o erro: "Registre o provedor de recursos 'Microsoft.Insights' para que esta assinatura habilite essa consulta" ao abrir o Log Analytics de uma VM? 
Muitos provedores de recursos são automaticamente registrados, mas você pode precisar registrar manualmente alguns provedores de recursos. O escopo de registro é sempre a assinatura. Para saber mais, veja [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Por que não estou recebendo nenhuma mensagem de erro de acesso ao abrir o Log Analytics de uma VM? 
Para exibir os Logs da VM, você precisará receber permissão de leitura para os workspaces que armazenam os logs da VM. Nesses casos, o administrador deve conceder a você permissões no Azure.

## <a name="alerts"></a>Alertas

### <a name="what-is-an-alert-in-azure-monitor"></a>O que é um alerta no Azure Monitor?
Os alertas trabalham de forma proativa, mandando notificações quando encontram condições importante em seus dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Existem vários tipos de alertas:

- Métrica - O valor métrico excede um limite.
- Consulta de log - Os resultados de uma consulta de log correspondem a critérios definidos.
- Registro de atividades - O evento de registro de atividades corresponde aos critérios definidos.
- Teste web - Resultados do teste de disponibilidade correspondem a critérios definidos.


Veja [visão geral dos alertas no Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>O que é um grupo de ação?
Um grupo de ação é uma coleção de notificações e ações que podem ser acionadas por um alerta. Vários alertas podem usar um único grupo de ação, permitindo que você aproveite conjuntos comuns de notificações e ações. Consulte [Criar e gerenciar grupos de ação no portal Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>O que é uma regra de ação?
Uma regra de ação permite modificar o comportamento de um conjunto de alertas que correspondem a determinados critérios. Isso permite que você execute tais requisitos, como desativar ações de alerta durante uma janela de manutenção. Você também pode aplicar um grupo de ação a um conjunto de alertas em vez de aplicá-los diretamente às regras de alerta. Consulte [as regras de ação](platform/alerts-action-rules.md).

## <a name="agents"></a>Agentes

### <a name="does-azure-monitor-require-an-agent"></a>O Monitor Azure precisa de um agente?
Um agente só é obrigado a coletar dados do sistema operacional e cargas de trabalho em máquinas virtuais. As máquinas virtuais podem estar localizadas no Azure, outro ambiente em nuvem ou no local. Consulte [a visão geral dos agentes do Monitor Do Azure](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual é a diferença entre os agentes do Monitor Azure?
A extensão azure Diagnostic é para máquinas virtuais Azure e coleta dados para o Azure Monitor Metrics, Azure Storage e Azure Event Hubs. O agente Log Analytics é para máquinas virtuais no Azure, outro ambiente em nuvem ou no local e coleta dados para o Azure Monitor Logs. O agente dependency requer o agente Log Analytics e coletou detalhes e dependências do processo. Consulte [a visão geral dos agentes do Monitor Do Azure](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Meu agente usa minha conexão ExpressRoute?
O tráfego para o Azure Monitor usa o circuito Microsoft peering ExpressRoute. Consulte a [documentação do ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para obter uma descrição dos diferentes tipos de tráfego ExpressRoute. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Como posso confirmar que o agente do Log Analytics é capaz de se comunicar com o Azure Monitor?
No painel de controle no computador do agente, selecione **Configurações de segurança &,** **Microsoft Monitoring Agent** . Na guia **Azure Log Analytics (OMS),** um ícone de marca de verificação verde confirma que o agente é capaz de se comunicar com o Azure Monitor. Um ícone amarelo de aviso significa que o agente está tendo problemas. Uma razão comum é que o serviço **Microsoft Monitoring Agent** parou. Use o gerenciador de controle de serviço para reiniciar o serviço.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Como faço para impedir que o agente do Log Analytics se comunique com o Azure Monitor?
Para agentes conectados diretamente ao Log Analytics, abra o Painel de Controle e selecione **Configurações de segurança &,** **Microsoft Monitoring Agent**. Na guia **OMS (Azure Log Analytics,** análise de log) remova todos os espaços de trabalho listados. No System Center Operations Manager, remova o computador da lista de computadores gerenciados do Log Analytics. O Operations Manager atualiza a configuração do agente para não relatar mais para o Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Qual a quantidade de dados enviada por agente?
A quantidade de dados enviados por agente depende:

* Das soluções que você habilitou
* Do número de logs e contadores de desempenho sendo coletados
* Do volume de dados nos logs

Consulte [Gerenciar o uso e os custos com o Azure Monitor Logs](platform/manage-cost-storage.md) para obter detalhes.

Para computadores capazes de executar o agente WireData, use a seguinte consulta para ver quantos dados estão sendo enviados:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanta largura de banda de rede é usada pelo Microsoft Management Agent (MMA) ao enviar dados para o Azure Monitor?
A largura de banda é uma função na quantidade de dados enviados. Dados são compactados conforme eles são enviados pela rede.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Como posso ser notificado quando a coleta de dados do agente log analytics pára?

Use as etapas descritas em [Criar um novo alerta do log](platform/alerts-metric.md) para ser notificado quando a coleta de dados for interrompida. Use as seguintes configurações para a regra de alerta:

- **Definir condição de alerta**: Especifique o espaço de trabalho do Log Analytics como o destino dos recursos.
- **Critérios do alerta** 
   - **Nome do sinal** *: Pesquisa de log personalizado*
   - **Consulta de pesquisa**:`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de alerta**: **Com base no** número de *resultados,* **Condição** *Maior que*, **Valor limite** *0*
   - **Avaliado com base em:** **Período (em minutos)** *30,* Frequência **(em minutos)** *10*
- **Definir os detalhes do alerta** 
   - **Nome**: *Coleta de dados interrompida*
   - **Gravidade**: *Aviso*

Especifique um grupo de [ação](platform/action-groups.md) existente ou novo para que, quando o alerta de log corresponder aos critérios, você seja notificado se tiver um batimento cardíaco faltando por mais de 15 minutos.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quais são os requisitos de firewall para os agentes do Azure Monitor?
Consulte [os requisitos de firewall da rede](platform/log-analytics-agent.md#network-requirements)para obter detalhes sobre os requisitos de firewall.


## <a name="visualizations"></a>Visualizações

### <a name="why-cant-i-see-view-designer"></a>Por que não posso ver o View Designer?

O View Designer só está disponível para usuários atribuídos com permissões de contribuinte ou superior no espaço de trabalho Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar:*

* [Aplicativo .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnósticos do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplicativo web Java](app/java-troubleshoot.md)

*Não recebo qualquer valor de meu servidor*

* [Definir exceções de firewall](app/ip-addresses.md)
* [Configurar um servidor ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>É possível usar o Application Insights com...?

* [Aplicativos da Web em um servidor IIS no Azure VM ou no conjunto de escala de máquina virtual Do Zure](app/azure-vm-vmss-apps.md)
* [Aplicativos Web em um servidor IIS : em uma VM ou local](app/asp-net.md)
* [Aplicativos web Java](app/java-get-started.md)
* [Aplicativos do Node.js](app/nodejs.md)
* [Aplicativos Web no Azure](app/azure-web-apps.md)
* [Serviços de Nuvem no Azure](app/cloudservices.md)
* [Servidores de aplicativo executando em Docker](app/docker.md)
* [Aplicativos Web de página única](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Aplicativo da área de trabalho do Windows](app/windows-desktop.md)
* [Outras plataformas](app/platforms.md)

### <a name="is-it-free"></a>É gratuito?

Sim, para uso experimental. No plano de preço básico, seu aplicativo pode enviar uma determinada margem de dados por mês gratuitamente. A margem gratuita é grande o suficiente para cobrir o desenvolvimento e publicação de um aplicativo para um número reduzido de usuários. Você pode definir um limite para impedir que mais de uma determinada quantidade de dados seja processada.

Volumes maiores de telemetria são cobrados pelo Gb. Fornecemos algumas dicas sobre como [limitar seus encargos](app/pricing.md).

O plano Empresarial incorre em uma encargo para cada dia em que cada nó do servidor Web envia telemetria. É adequado se você quiser usar a Exportação Contínua em grande escala.

[Leia o plano de preço](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página Uso e custos estimados** em um recurso do Application Insights. Há um gráfico de uso recente. Você pode definir um limite de volume de dados, se desejar.
* Abra a [Folha de Cobrança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver suas contas em todos os recursos.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>O que o Application Insights modifica no meu projeto?
Os detalhes dependem do tipo de projeto. Para um aplicativo Web:

* Adiciona estes arquivos ao seu projeto:
  * ApplicationInsights.config
  * ai.js
* Instala estes pacotes NuGet:
  * *API do Application Insights* - a API principal
  * *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor
  * *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente
* Os pacotes incluem os seguintes assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:
  * Web.config
  * packages.config
* (Somente novos projetos - se você [adicionar insights de aplicativos a um projeto existente,][start]você tem que fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com o ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido\_na página-mestre Views/Shared/ Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões anteriores do SDK?
Consulte as [notas de versão](app/release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?
No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

### <a name="what-is-status-monitor"></a>O que é o Status Monitor?

Um aplicativo da área de trabalho que você pode usar no servidor Web do IIS para ajudar a configurar o Application Insights em aplicativos Web. Ele não coleta telemetria: você pode interrompê-lo quando não estiver configurando um aplicativo. 

[Saiba mais](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Qual a telemetria coletada pelo Application Insights?

A partir dos aplicativos Web do servidor:

* Solicitações HTTP
* [Dependências](app/asp-net-dependencies.md). Chamadas para: Banco de Dados SQL; chamadas HTTP para serviços externos; tabela, armazenamento de blobs, fila e BD Cosmos do Azure. 
* [Exceções](app/asp-net-exceptions.md) e rastreamentos de pilha.
* [Contadores de desempenho](app/performance-counters.md) - Se você usar [o Monitor de Status,](app/monitor-performance-live-website-now.md) [o monitoramento do Azure para serviços de aplicativos,](app/azure-web-apps.md) [o monitoramento do Azure para vm ou conjunto de escala de máquina virtual](app/azure-vm-vmss-apps.md)ou o escritor de [insights de aplicativos coletados.](app/java-collectd.md)
* [Eventos e métricas personalizados](app/api-custom-events-metrics.md) que você codifica.
* [Logs de Rastreamento](app/asp-net-trace-logs.md) se você configurar o coletor apropriado.

A partir das [páginas da Web do cliente](app/javascript.md):

* [Contagens de exibição de página](app/usage-overview.md)
* [Chamadas AJAX](app/asp-net-dependencies.md) Solicitações feitas a partir de um script em execução.
* Dados de carregamento de exibição de página
* Contagens de sessão e usuários
* [IDs de Usuário Autenticado](app/api-custom-events-metrics.md#authenticated-users)

A partir de outras fontes, se você configurá-las:

* [Diagnósticos do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importar no Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Eu posso filtrar ou modificar alguma telemetria?

Sim, no servidor você pode gravar:

* Processador de Telemetria para filtrar ou adicionar propriedades a itens de telemetria selecionados antes de serem enviados do seu aplicativo.
* Inicializador de Telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre [ASP.NET](app/api-filtering-sampling.md) ou [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como são calculados os dados de cidade, país/região e outros dados de localização geográfica?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente Web usando [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: coletamos o endereço IP do remetente.
* Telemetria do Servidor: o módulo Application Insights coleta o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver configurado.
* Para saber mais sobre como os dados de endereço IP e geolocalização são coletados no Application Insights, consulte este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


É possível configurar o `ClientIpHeaderTelemetryInitializer` para coletar o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP`. [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

É possível [usar o Power BI](app/export-power-bi.md ) para exibir sua telemetria de solicitação em um mapa.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?
Veja [Privacidade e Retenção de Dados][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>O que acontece com a telemetria do Application Insight quando um servidor ou dispositivo perde a conexão com o Azure?

Todos os nossos SDKs, incluindo o Web SDK, incluem "transporte confiável" ou "transporte robusto". Quando o servidor ou dispositivo perde a conexão com o Azure, a telemetria é [armazenada localmente no sistema de arquivos](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (SDKs do servidor) ou no HTML5 Session Storage (Web SDK). O SDK tentará periodicamente enviar essa telemetria até que nosso serviço de ingestão o considere "obsoleto" (48 horas para logs, 30 minutos para métricas). A telemetria velha será descartada. Em alguns casos, como quando o armazenamento local está cheio, a repetição não ocorrerá.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>É possível enviar dados pessoais na Telemetria?

Isso é possível se o seu código envia tais dados. Isso também pode acontecer se as variáveis nos rastreamentos de pilha incluírem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais sejam devidamente tratados. [Saiba mais sobre privacidade e retenção de dados ](app/data-retention-privacy.md).

**Todos** os octetos do endereço web do cliente são sempre definidos como 0 depois que os atributos de localização geográfica são pesquisados.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Minha chave de instrumentação está visível na fonte da minha página web. 

* Essa é uma prática comum em soluções de monitoramento.
* Ele não pode ser usado para roubar seus dados.
* Ele pode ser usado para distorcer seus dados ou disparar alertas.
* Não temos conhecimento se algum cliente teve tais problemas.

Você pode:

* Use duas teclas de instrumentação separadas (recursos separados do Application Insights), para dados de clientes e servidores. Ou
* Gravar um proxy que execute no seu servidor e o cliente Web envie dados através desse proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?
Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo usar recursos do Application Insights simples ou múltiplos?

Use um recurso único para todos os componentes ou funções em um único sistema de negócios. Use recursos separados para desenvolvimento, teste e versões de lançamento e para aplicativos independentes.

* [Consulte a discussão aqui](app/separate-resources.md)
* [Exemplo : serviço de nuvem com funções Web e funções de trabalho](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como faço para alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](app/separate-resources.md)
* [Exemplo : serviço de nuvem com funções Web e funções de trabalho](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Quais são as contagens de Sessão e Usuário?

* O SDK do JavaScript define um cookie de usuário no cliente Web para identificar os usuários que retornam e, um cookie de sessão para atividades de grupo.
* Se não houver nenhum script do lado do cliente, você poderá [definir cookies no server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um usuário real usar seu site em diferentes navegadores, usar navegação em modo privado/incógnito ou usar diferentes computadores, então, eles serão contados mais de uma vez.
* Para identificar um usuário conectado entre navegadores e computadores, adicione uma chamada a [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Eu habilitei tudo no Application Insights?
| O que você deverá ver | Como obter isso | Por que você deseja isso |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes da Web](app/monitor-web-app-availability.md) |Tenha certeza que o aplicativo Web está ativo |
| Desempenho do aplicativo para servidores: tempos de resposta... |[Adicionar o Application Insights ao seu projeto](app/asp-net.md) ou [Instalar o AI Status Monitor no servidor](app/monitor-performance-live-website-now.md) (ou escreva seu próprio código para [rastrear dependências](app/api-custom-events-metrics.md#trackdependency)) |Detectar problemas de desempenho |
| Telemetria de dependência |[Instalar o AI Status Monitor no servidor](app/monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha por meio de exceções |[Inserir chamadas TrackException em seu código](app/asp-net-exceptions.md) (mas alguns são informados automaticamente) |Detectar e diagnosticar exceções |
| Pesquisar rastreamentos de log |[Adicionar um adaptador de registro em log](app/asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de uso do cliente: modos de exibição de página, sessões,... |[Inicializador de JavaScript em páginas da Web](app/javascript.md) |Análise de uso |
| Métricas de cliente personalizadas |[Rastreando chamadas em páginas da Web](app/api-custom-events-metrics.md) |Aprimorar a experiência do usuário |
| Métricas de servidor personalizadas |[Rastreando chamadas no servidor](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens nos gráficos de Pesquisa e Métricas são desiguais?

A [Amostragem](app/sampling.md) reduz o número de itens de telemetria (solicitações, eventos personalizados e, assim por diante) que são realmente enviados de seu aplicativo para o portal. Em Pesquisa, você visualiza o número de itens realmente recebidos. Nos gráficos de métrica que exibem uma contagem de eventos, você visualiza o número de eventos originais que ocorreu. 

Cada item transmitido carrega uma propriedade `itemCount` que mostra quantos eventos originais esse item representa. Para observar a amostragem em operação, é possível executar essa consulta no Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automação

#### <a name="configuring-application-insights"></a>Configurando o Application Insights

Você pode [gravar scripts do PowerShell](app/powershell.md) usando o Azure Resource Monitor para:

* Criar e atualizar recursos do Application Insights.
* Definir o plano de preços.
* Obter a chave de instrumentação.
* Adicionar um alerta de métrica.
* Adicionar um teste de disponibilidade.

Não é possível configurar um relatório do Metric Explorer ou configurar a exportação contínua.

#### <a name="querying-the-telemetry"></a>Consultar a telemetria

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas do [Analytics](app/analytics.md).

### <a name="how-can-i-set-an-alert-on-an-event"></a>Como configurar um alerta em um evento?

Os alertas do Azure são somente em métricas. Crie uma métrica personalizada que cruze um limite de valor sempre que o evento ocorrer. Em seguida, configure um alerta na métrica. Você receberá uma notificação sempre que a métrica cruzar o limiar em qualquer direção; você não receberá uma notificação até a primeira travessia, não importa se o valor inicial é alto ou baixo; há sempre uma latência de alguns minutos.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Há encargos de transferência de dados entre um aplicativo Web e o Application Insights?

* Se seu aplicativo Web do Azure estiver hospedado em um data center, onde há um ponto de extremidade de coleta do Application Insights, não haverá cobrança. 
* Se não houver um ponto de extremidade de coleta no data center do host, então, a telemetria do seu aplicativo incorrerá em [Encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde seu recurso Application Insights está hospedado. Depende apenas da distribuição de nossos pontos de extremidade.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>É possível enviar telemetria para o portal do Application Insights?

É recomendável usar nossos SDKs e usar a [API de SDK](app/api-custom-events-metrics.md). Existem variantes do SDK para várias [plataformas](app/platforms.md). Esses SDKs tratam buffer, compressão, limitação, repetições e, assim por diante. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e o [protocolo de ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

### <a name="can-i-monitor-an-intranet-web-server"></a>É possível monitorar um servidor Web de intranet?

Sim, mas você precisará permitir o tráfego para nossos serviços por exceções de firewall ou redirecionamentos de proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Examine nossa lista de serviços e endereços IP [aqui](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Exceção de firewall

Permita que o servidor Web envie telemetria para os pontos de extremidade. 

#### <a name="gateway-redirect"></a>Gateway redirecionado

Encaminhe o tráfego de seu servidor para um gateway na sua intranet substituindo pontos de extremidade na sua configuração. Se essas propriedades de “Ponto de extremidade” não estiverem presentes na sua configuração, essas classes usarão os valores padrão mostrados abaixo no exemplo ApplicationInsights.config. 

Seu gateway deve rotear o tráfego para o endereço básico do nosso ponto de extremidade. Em sua configuração, substitua os valores padrão por `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo ApplicationInsights.config com pontos de extremidade padrão:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> O ApplicationIdProvider está disponível a partir de v2.6.0.



#### <a name="proxy-passthrough"></a>Passagem por proxy

A passagem por proxy pode ser alcançada configurando um proxy de nível de máquina ou de nível de aplicativo.
Para obter mais informações, consulte o artigo do dotnet no [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>É possível executar testes na Web de Disponibilidade em um servidor de intranet?

Nossos [testes na Web](app/monitor-web-app-availability.md) executam em pontos de presença distribuídos no mundo inteiro. Existem duas soluções:

* Porta de firewall : permitir solicitações para seu servidor da [lista mutável e longa dos agentes de teste na Web](app/ip-addresses.md).
* Grave seu próprio código para enviar solicitações periódicas ao seu servidor de dentro de sua intranet. Você pode executar testes na Web do Visual Studio para essa finalidade. O testador pode enviar os resultados ao Application Insights usando a API TrackAvailability().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo demora para coletar a telemetria?

A maioria dos dados do Application Insights tem uma latência inferior a cinco minutos. Alguns dados podem demorar mais; normalmente, arquivos de log maiores. Para saber mais, confira [SLA do Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres

Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para contêineres. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>O que *outros processos* representam sob a exibição nó?

**Outros processos** visam ajudá-lo a entender claramente a causa raiz do alto uso de recursos em seu nó. Isso permite distinguir o uso entre processos containerizados versus processos não contêineres.

O que são esses **Outros Processos?** 

Estes são processos não contêineres que são executados em seu nó.  

Como calculamos isso?

**Outros Processos Uso** = *total do CAdvisor* - *Uso de processo containerizado*

Os **outros processos** incluem:

- Processos não-containerizados do Kubernetes autogerenciados ou gerenciados 

- Processos de tempo de execução de contêineres  

- Kubelet  

- Processos do sistema em execução em seu nó 

- Outras cargas de trabalho não-Kubernetes em execução em hardware de nó ou VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo os valores de propriedade Imagem e Nome preenchidos quando consultai a tabela ContainerLog.

Para a versão do agente ciprod12042019 e posterior, por padrão, essas duas propriedades não são preenchidas para cada linha de log para minimizar o custo incorrido nos dados de log coletados. Existem duas opções para consultar a tabela que incluem essas propriedades com seus valores:

#### <a name="option-1"></a>Opção 1 

Junte-se a outras tabelas para incluir esses valores de propriedade nos resultados.

Modifique suas consultas para incluir propriedades ```ContainerInventory``` Image e ImageTag da tabela, juntando-se à propriedade ContainerID. Você pode incluir a propriedade Nome (como ```ContainerLog``` apareceu anteriormente na tabela) do campo ContaineName da tabela KubepodInventory, juntando-se à propriedade ContainerID. Esta é a opção recomendada.

O exemplo a seguir é uma consulta detalhada de amostra que explica como obter esses valores de campo com as adesões.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opção 2

Reative a coleta dessas propriedades para cada linha de registro de contêineres.

Se a primeira opção não for conveniente devido às alterações de consulta ```log_collection_settings.enrich_container_logs``` [envolvidas,](insights/container-insights-agent-config.md)você poderá reativar a coleta desses campos ativando a configuração no mapa de configuração do agente conforme descrito nas configurações de configuração de coleta de dados .

> [!NOTE]
> A segunda opção não é recomendada com grandes clusters que possuem mais de 50 nós porque gera chamadas de servidor de API de todos os nós do cluster para realizar esse enriquecimento. Essa opção também aumenta o tamanho dos dados para cada linha de log coletada.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Posso ver as métricas coletadas em Grafana?

O Azure Monitor para contêineres suporta métricas de visualização armazenadas no espaço de trabalho do Log Analytics nos painéis Grafana. Nós fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do painel da Grafana para que você comece e faça referência para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em dashboards grafana personalizados. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorar meu cluster de motores AKS com o Monitor Azure para contêineres?

O Azure Monitor para contêineres suporta o monitoramento das cargas de trabalho dos contêineres implantados no cluster(s) de cluster sutecida no Azure. Para obter mais detalhes e uma visão geral das etapas necessárias para permitir o monitoramento deste cenário, consulte [Usando o Monitor Azure para contêineres para motor AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados no meu espaço de trabalho do Log Analytics?

Se você não conseguir ver nenhum dado no espaço de trabalho do Log Analytics em um determinado momento todos os dias, você pode ter atingido o limite padrão de 500 MB ou a tampa diária especificada para controlar a quantidade de dados a serem coletados diariamente. Quando o limite é cumprido para o dia, a coleta de dados pára e retoma apenas no dia seguinte. Para revisar o uso de seus dados e atualizar para um nível de preço diferente com base nos padrões de uso previstos, consulte [o uso e o custo dos dados log](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os estados de contêiner especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres parados e em execução. A tabela é preenchida por um fluxo de trabalho dentro do agente que consulta o docker por todos os contêineres (em execução e parados) e encaminha esses dados ao espaço de trabalho do Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como resolver *erro de registro de assinatura ausente?*

Se você receber o erro **Faltando registro de assinatura para Microsoft.OperationsManagement,** você pode resolvê-lo registrando o provedor de recursos **Microsoft.OperationsManagement** na assinatura onde o espaço de trabalho é definido. A documentação para saber como fazer isso pode ser encontrada [aqui](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para clusters AKS habilitados para RBAC?

A solução de monitoramento de contêineres não suporta RBAC, mas é suportada com o Monitor Azure para Contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados desses clusters.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como habilito a coleta de log para contêineres no namespace kube-system por meio do Helm?

A coleta de log de contêineres no namespace kube-system está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para obter mais informações, consulte o [Monitor do Azure para a](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) página GitHub de contêineres. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como posso atualizar o omsagent para a versão mais recente?

Para saber como atualizar o agente, confira [Gerenciamento de agente](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Como habilito o registro em log de várias linhas?

Atualmente, o Azure Monitor para contêineres não suporta registro multi-linha, mas há solução sem solução disponível. Você pode configurar todos os serviços para gravar em formato JSON e, em seguida, o Docker/Moby vai gravá-los como uma única linha.

Por exemplo, você pode encapsular seu log como um objeto JSON, conforme mostrado no exemplo de um aplicativo do Node.js abaixo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados serão parecidos com o seguinte exemplo no Azure Monitor para logs quando você consultar para ele:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para uma olhada detalhada no problema, revise o seguinte link do [GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como resolver erros de AD do Azure quando ameio logs ao vivo? 

Você pode ver o seguinte erro: **A url de resposta especificada na solicitação não corresponde às\>urls de resposta configuradas para o aplicativo: '<ID do aplicativo'**. A solução para resolvê-lo pode ser encontrada no artigo [Como visualizar dados de contêineres em tempo real com o Azure Monitor para contêineres](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar cluster após o embarque?

Se depois de habilitar o Azure Monitor para contêineres para um cluster AKS, você excluirá o espaço de trabalho do Log Analytics para o que o cluster estava enviando seus dados, ao tentar atualizar o cluster para o outro, ele falhará. Para contornar isso, você terá que desativar o monitoramento e, em seguida, reative-o fazendo referência a um espaço de trabalho válido diferente em sua assinatura. Quando você tentar executar a atualização do cluster novamente, ele deve processar e concluir com sucesso.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quais portas e domínios preciso abrir/lista branca para o agente?

Consulte os [requisitos](insights/container-insights-onboard.md#network-firewall-requirements) de firewall da Rede para as informações de configuração de proxy e firewall necessárias para o agente contêiner com nuvens Azure, Azure US e Azure China 21Vianet.

## <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para VMs. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

### <a name="can-i-onboard-to-an-existing-workspace"></a>É possível fazer a integração com um workspace existente?
Se as máquinas virtuais já estiverem conectadas a um espaço de trabalho do Log Analytics, você poderá continuar usando esse espaço de trabalho durante a integração com o Azure Monitor para VMs, desde que ele esteja em uma das regiões compatíveis listadas [aqui](insights/vminsights-enable-overview.md#prerequisites).


### <a name="can-i-onboard-to-a-new-workspace"></a>É possível fazer a integração com um novo workspace? 
Se, atualmente, as VMs não estiverem conectadas a um espaço de trabalho do Log Analytics existente, você precisará criar um espaço de trabalho para armazenar os dados. A criação de um workspace padrão será feita automaticamente se você configurar uma VM individual do Azure para o Azure Monitor para VMs por meio do portal do Azure.

Se você optar por usar o método baseado em script, essas etapas serão cobertas no [Monitor Enable Azure para VMs usando o artigo do modelo Azure PowerShell ou Resource Manager.](insights/vminsights-enable-at-scale-powershell.md) 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que fazer se minha VM já estiver subordinada a um workspace existente?
Caso você já esteja coletando dados de suas máquinas virtuais, talvez você já tenha configurado uma delas para relatar os dados para um espaço de trabalho do Log Analytics existente.  Desde que esse workspace esteja em uma de nossas regiões compatíveis, você poderá habilitar o Azure Monitor para VMs nesse workspace pré-existente.  Se o espaço de trabalho que você já está usando não estiver em uma de nossas regiões suportadas, você não poderá embarcar no Azure Monitor para VMs neste momento.  Estamos trabalhando ativamente para dar suporte a outras regiões.


### <a name="why-did-my-vm-fail-to-onboard"></a>Por que minha VM não pôde ser integrada?
Ao fazer a integração de uma VM do Azure por meio do portal do Azure, ocorrem as seguintes etapas:

* Um espaço de trabalho do Log Analytics padrão é criado, caso essa opção tenha sido selecionada.
* O agente do Log Analytics será instalado nas VMs do Azure usando uma extensão de VM, se for determinado que isso é necessário.  
* O Dependency Agent do Mapa do Azure Monitor para VMs será instalado nas VMs do Azure usando uma extensão, se for determinado que isso é necessário. 

Durante o processo de integração, verificamos o status de cada um dos itens acima para retornar um status de notificação a você no portal. A configuração do workspace e a instalação do agente normalmente levam de 5 a 10 minutos. A visualização dos dados de monitoramento no portal leva de 5 a 10 minutos adicionais.  

Se você tiver iniciado a integração e vir mensagens indicando que a VM precisa ser integrada, permita uma margem de até 30 minutos para que a VM conclua o processo. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Eu não vejo alguns ou quaisquer dados nos gráficos de desempenho para o meu VM
Nossos gráficos de desempenho foram atualizados para usar dados armazenados na tabela *InsightsMetrics.*  Para ver os dados nesses gráficos, você precisará atualizar para usar a nova solução VM Insights.  Consulte nosso [FAQ GA](insights/vminsights-ga-release-faq.md) para obter informações adicionais.

Se você não ver dados de desempenho na tabela de discos ou em alguns dos gráficos de desempenho, seus contadores de desempenho podem não estar configurados no espaço de trabalho. Para resolver isso, execute o [script do PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell) a seguir.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Qual a diferença entre o recurso Mapa do Azure Monitor para VMs e o Mapa do Serviço?
O recurso Mapa do Azure Monitor para VMs baseia-se no Mapa do Serviço, mas tem as seguintes diferenças:

* A exibição de Mapa pode ser acessada por meio da folha da VM e do Azure Monitor para VMs no Azure Monitor.
* As conexões no Mapa agora são clicáveis e mostram uma exibição dos dados de métrica de conexão no painel lateral da conexão selecionada.
* Há uma nova API que é usada para criar os mapas, a fim de dar um melhor suporte a mapas mais complexos.
* As VMs monitoradas agora estão incluídas no nó do grupo de clientes e o gráfico de rosca mostra a proporção de máquinas virtuais monitoradas vs. não monitoradas no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* As máquinas virtuais monitoradas agora estão incluídas nos nós do grupo de portas do servidor e o gráfico de rosca mostra a proporção de computadores monitorados vs. não monitorados no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o Mapa do Aplicativo do Application Insights.
* Os painéis laterais foram atualizados e não possuem o conjunto completo de integrações que foram suportadas no Mapa de Serviços - Gerenciamento de atualizações, Rastreamento de alterações, segurança e service desk. 
* A opção para escolher grupos e computadores a serem mapeados foi atualizada e agora dá suporte a Assinaturas, Grupos de Recursos, conjuntos de dimensionamento de máquinas virtuais do Azure e Serviços de nuvem.
* Não é possível criar grupos de computadores do Mapa do Serviço no recurso Mapa do Azure Monitor para VMs.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que meus gráficos de desempenho mostram linhas pontilhadas?
Isso pode ocorrer por alguns motivos.  Nos casos em que há uma lacuna na coleta de dados, as linhas são mostradas como pontilhadas.  Se você tiver modificado a frequência de amostragem de dados dos contadores de desempenho habilitados (a configuração padrão é coletar dados a cada 60 segundos), você poderá ver linhas pontilhadas no gráfico caso tenha escolhido um intervalo de tempo restrito para o gráfico e a frequência de amostragem for menor que o tamanho do bucket usado no gráfico (por exemplo, a frequência de amostragem é de intervalos de 10 minutos e cada bucket no gráfico é de 5 minutos).  A escolha de um intervalo de tempo maior para a exibição deverá fazer com que as linhas do gráfico apareçam como linhas sólidas em vez de pontos, neste caso.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Os grupos são compatíveis com o Azure Monitor para VMs?
Sim, depois de instalar o agente de Dependência, coletamos informações das VMs para exibir grupos com base em assinaturas, grupos de recursos, conjuntos de dimensionamento de máquinas virtuais e serviços de nuvem.  Se você estiver usando o Mapa de serviços e tiver criado grupos de máquinas, eles também serão exibidos.  Os grupos de computadores também aparecerão no filtro de grupos se você os criou para o workspace que está visualizando. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como fazer para ver os detalhes para o que está orientando a linha do 95º percentil nos gráficos de desempenho de agregação?
Por padrão, a lista é classificada para mostrar as VMs que têm o valor mais alto para o 95º percentil na métrica selecionada, exceto pelo gráfico de Memória disponível, que mostra os computadores com o valor mais baixo do 5º percentil.  Ao clicar no gráfico, a exibição **Lista N Principais** será aberta com a métrica apropriada selecionada.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como o recurso Mapa manipula os IPs duplicados entre diferentes VNETs e sub-redes?
Se você estiver duplicando intervalos de IP com VMs ou conjuntos de dimensionamento de máquinas virtuais do Azure em sub-redes e VNETs, isso poderá fazer com que o Mapa do Azure Monitor para VMs exiba informações incorretas. Esse é um problema conhecido e estamos investigando opções para melhorar essa experiência.

### <a name="does-map-feature-support-ipv6"></a>O recurso Mapa dá suporte ao IPv6?
Atualmente, o recurso Mapa dá suporte apenas ao IPv4. Estamos considerando o suporte para IPv6. Também damos suporte ao IPv4 por túnel dentro do IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Ao carregar um mapa para um Grupo de Recursos ou outro grupo grande, o mapa fica difícil de ser exibido
Embora tenhamos feito melhorias no Mapa para lidar com configurações grandes e complexas, percebemos que um mapa pode ter uma variedade de nós, conexões e um nó funcionando como um cluster.  Temos o compromisso de continuar aprimorando o suporte para aumentar a escalabilidade.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia Desempenho tem uma aparência diferente do gráfico de rede na página Visão Geral da VM do Azure?

A página de visão geral de uma VM do Azure exibe gráficos com base na medida do host da atividade na VM convidada.  Para o gráfico de rede na Visão Geral da VM do Azure, ela exibe apenas o tráfego de rede que será cobrado.  Isso não inclui tráfego de rede intervirtual.  Os dados e gráficos mostrados para o Azure Monitor for VMs são baseados em dados da VM convidado e o gráfico de rede exibe todo o tráfego TCP/IP que está de entrada e saída para essa VM, incluindo rede intervirtual.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como o tempo de resposta é medido para dados armazenados no VMConnection e exibidos no painel de conexão e nas câmaras de trabalho?

O tempo de resposta é uma aproximação. Como não instrumentamos o código do aplicativo, realmente não sabemos quando uma solicitação começa e quando a resposta chega. Em vez disso, observamos os dados sendo enviados em uma conexão e, em seguida, os dados voltando sobre essa conexão. Nosso agente mantém o controle desses envios e recebe e tenta emparelhá-los: uma seqüência de envios, seguida de uma seqüência de recebe é interpretada como um par de solicitações/respostas. O tempo entre essas operações é o tempo de resposta. Ele incluirá a latência da rede e o tempo de processamento do servidor.

Essa aproximação funciona bem para protocolos que são baseados em solicitação/resposta: uma única solicitação sai na conexão e uma única resposta chega. Este é o caso de HTTP(S) (sem pipelining), mas não satisfeito para outros protocolos.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Quais são as limitações se estiver no plano de preços Gratuito do Log Analytics?
Se você configurou o Azure Monitor com um espaço de trabalho do Log Analytics usando o tipo de preço *Gratuito*, o recurso Mapa do Azure Monitor para VMs dará suporte a apenas cinco máquinas conectadas ao espaço de trabalho. Se houver cinco VMs conectadas a um workspace gratuito, ao desconectar uma das VMs e depois conectar uma nova VM, a nova VM não será monitorada e refletida na página Mapa.  

Nesta condição, você será solicitado com a opção **Try Now** quando abrir a VM e selecionar **Insights** no painel esquerdo, mesmo depois de ter sido instalado já na VM.  No entanto, não serão exibidas as opções como normalmente ocorreria se essa VM não estivesse incorporada ao Azure Monitor para VMs. 


## <a name="next-steps"></a>Próximas etapas
Se sua pergunta não for respondida aqui, você pode consultar os seguintes fóruns para perguntas e respostas adicionais.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Para obter comentários gerais no Azure Monitor, visite o [fórum de comentários](https://feedback.azure.com/forums/34192--general-feedback).
