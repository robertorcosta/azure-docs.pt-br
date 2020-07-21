---
title: Perguntas frequentes sobre o Azure Monitor | Microsoft Docs
description: Respostas para perguntas frequentes sobre o Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/15/2020
ms.openlocfilehash: 5366166a31ee45c74c34b8af0e01da251bd7f7f0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499215"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Monitor

Essas perguntas frequentes da Microsoft são uma lista de perguntas comuns sobre o Azure Monitor.

## <a name="general"></a>Geral

### <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O [Azure Monitor](overview.md) é um serviço no Azure que fornece monitoramento de desempenho e disponibilidade para aplicativos e serviços no Azure, em outros ambientes de nuvem ou localmente. O Azure Monitor coleta dados de várias fontes e os coloca em uma plataforma de dados comum para serem analisados quanto a tendências e anomalias. Recursos avançados do Azure Monitor ajudam a identificar e responder rapidamente a situações críticas que podem afetar seu aplicativo.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual é a diferença entre o Azure Monitor, o Log Analytics e o Application Insights?
Em setembro de 2018, a Microsoft combinou o Azure Monitor, o Log Analytics e o Application Insights em um serviço para fornecer monitoramento de ponta a ponta avançado de seus aplicativos e dos componentes dos quais eles dependem. Os recursos do Log Analytics e do Application Insights não foram alterados, embora alguns recursos tenham passado para a marca do Azure Monitor a fim de refletir melhor seu novo escopo. O mecanismo de dados de log e a linguagem de consulta do Log Analytics agora são chamados de Logs do Azure Monitor. Confira [Atualizações de terminologia do Azure Monitor](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quanto custa o Azure Monitor?
Os recursos do Azure Monitor que são habilitados automaticamente, como a coleta de métricas e logs de atividade, são fornecidos sem custo. Há um custo associado a outros recursos, como consultas de log e alertas. Confira a [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter informações detalhadas sobre preços.

### <a name="how-do-i-enable-azure-monitor"></a>Como faço para habilitar o Azure Monitor?
O Azure Monitor é habilitado no momento em que você cria uma assinatura do Azure e o [Log de atividades](./platform/platform-logs-overview.md) e as [métricas](platform/data-platform-metrics.md) de plataforma são coletadas automaticamente. Crie [configurações de diagnóstico](platform/diagnostic-settings.md) para coletar informações mais detalhadas sobre a operação dos recursos do Azure e adicione [soluções de monitoramento](insights/solutions.md) e [insights](insights/insights-overview.md) para fornecer análise adicional sobre os dados coletados de serviços específicos. 

### <a name="how-do-i-access-azure-monitor"></a>Como faço para acessar o Azure Monitor?
Acesse todos os recursos e dados do Azure Monitor no menu **Monitorar** no portal do Azure. A seção **Monitoramento** do menu para diferentes serviços do Azure oferece acesso às mesmas ferramentas com os dados filtrados para um recurso específico. Os dados do Azure Monitor também podem ser acessados de várias formas, usando a CLI, o PowerShell e uma API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Há uma versão local do Azure Monitor?
Não. O Azure Monitor é um serviço de nuvem escalonável que processa e armazena grandes quantidades de dados, embora Azure Monitor possa monitorar recursos locais e em outras nuvens.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>O Azure Monitor pode monitorar recursos locais?
Sim, além de coletar dados de monitoramento de recursos do Azure, o Azure Monitor pode coletar dados de máquinas virtuais e aplicativos em outras nuvens e locais. Confira [Fontes de dados de monitoramento para o Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>O Azure Monitor se integra ao System Center Operations Manager?
Você pode conectar seu grupo de gerenciamento do System Center Operations Manager existente com o Azure Monitor para coletar dados de agentes para Logs do Azure Monitor. Isso permite que você use consultas de log e soluções para analisar dados coletados dos agentes. Você também pode configurar agentes existentes do System Center Operations Manager para enviar dados diretamente para o Azure Monitor. Confira [Conectar o Operations Manager com o Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Quais endereços IP o Azure Monitor usa?
Confira [endereços IP usados pelo Application Insights e pelo Log Analytics](app/ip-addresses.md) para obter uma lista dos endereços IP e portas necessárias para que agentes e outros recursos externos acessem o Azure Monitor. 

## <a name="monitoring-data"></a>Dados de monitoramento

### <a name="where-does-azure-monitor-get-its-data"></a>De qual local o Azure Monitor obtém dados?
O Azure Monitor coleta dados em uma variedade de fontes, incluindo logs e métricas da plataforma e dos recursos do Azure, aplicativos personalizados e agentes em execução em máquinas virtuais. Outros serviços, como a Central de Segurança do Azure e o Observador de Rede, coletam dados e os colocam em um workspace do Log Analytics para que possam ser analisados com os dados do Azure Monitor. Você também pode enviar dados personalizados para o Azure Monitor usando a API REST para logs ou métricas. Confira [Fontes de dados de monitoramento para o Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Quais dados são coletados pelo Azure Monitor? 
O Azure Monitor coleta dados de uma variedade de fontes na forma de [logs](platform/data-platform-logs.md) ou [métricas](platform/data-platform-metrics.md). Cada tipo de dados tem vantagens relativas próprias e cada um deles é compatível com um determinado conjunto de recursos no Azure Monitor. Há um banco de dados de métricas para cada assinatura do Azure, embora você possa criar vários workspaces do Log Analytics para coletar logs, dependendo de seus requisitos. Confira [Plataforma de dados do Azure Monitor](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Há uma quantidade máxima de dados que posso coletar no Azure Monitor?
Não há nenhum limite para a quantidade de dados de métrica que você pode coletar, mas esses dados são armazenados por um máximo de 93 dias. Confira [Retenção de Métricas](platform/data-platform-metrics.md#retention-of-metrics). Não há limite para a quantidade de dados de log que você pode coletar, mas isso pode ser afetado pelo tipo de preço que você escolher para o workspace do Log Analytics. Consulte [detalhes de preço](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Como faço para acessar os dados coletados pelo Azure Monitor?
As informações e soluções fornecem uma experiência personalizada para trabalhar com os dados armazenados no Azure Monitor. Você pode trabalhar diretamente com os dados de log usando uma consulta de log escrita na KQL (Linguagem de Consulta Kusto). No portal do Azure, você pode escrever e executar consultas e analisar dados interativamente usando o Log Analytics. Analise as métricas no portal do Azure com o Metrics Explorer. Confira [Analisar dados de log no Azure Monitor](log-query/log-query-overview.md) e [Introdução ao Metrics Explorer do Azure](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Soluções e insights

### <a name="what-is-an-insight-in-azure-monitor"></a>O que é um insight no Azure Monitor?
Os insights fornecem uma experiência de monitoramento personalizada para determinados serviços do Azure. Eles usam as mesmas métricas e logs que outros recursos no Azure Monitor, mas podem coletar dados adicionais e fornecer uma experiência única no portal do Azure. Confira [Insights no Azure Monitor](insights/insights-overview.md).

Para ver insights no portal do Azure, confira a seção **Insights** do menu **Monitorar** ou a seção **Monitoramento** do menu do serviço.

### <a name="what-is-a-solution-in-azure-monitor"></a>O que é uma solução no Azure Monitor?
As soluções de monitoramento são conjuntos de lógica empacotados para monitorar um aplicativo ou serviço específico com base em recursos do Azure Monitor. Elas coletam dados de log no Azure Monitor e fornecem consultas de log e exibições para analisá-las usando uma experiência comum no portal do Azure. Confira [Soluções de monitoramento no Azure Monitor](insights/solutions.md).

Para ver soluções no portal do Azure, clique em **Mais** na seção **Insights** do menu **Monitorar**. Clique em **Adicionar** para adicionar outras soluções ao workspace.

## <a name="logs"></a>Logs

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual é a diferença entre os logs do Azure Monitor e o Azure Data Explorer?
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Os Logs do Azure Monitor são criados com base no Azure Data Explorer e usam a mesma KQL (Linguagem de Consulta Kusto) com algumas pequenas diferenças. Confira [Diferenças na linguagem de consulta de log do Azure Monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Como faço para recuperar dados de log?
Todos os dados são recuperados de um workspace do Log Analytics usando uma consulta de log escrita em KQL (Linguagem de Consulta Kusto). Você pode escrever suas consultas ou usar soluções e insights que incluem consultas de log para um determinado aplicativo ou serviço. Confira [Visão geral sobre consultas de log no Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>O que é um workspace do Log Analytics?
Todos os dados de log coletados pelo Azure Monitor são armazenados em um workspace do Log Analytics. Um workspace é essencialmente um contêiner em que os dados de log são coletados de uma variedade de fontes. Você pode ter um workspace do Log Analytics para todos os seus dados de monitoramento ou pode ter requisitos para vários workspaces. Confira [Projeto da implantação de logs do Azure Monitor](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Você pode mover um workspace do Log Analytics existente para outra assinatura do Azure?
Você pode mover um workspace entre grupos de recursos ou assinaturas, mas não para uma região diferente. Confira [Mover um workspace do Log Analytics para uma assinatura ou grupo de recursos diferente](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Por que não consigo ver os botões do Gerenciador de Consultas e de Salvar no Log Analytics?

Os botões do **Gerenciador de Consultas**, de **Salvar** e de **Nova regra de alerta** não estão disponíveis quando o [escopo de consulta](log-query/scope.md) está definido para um recurso específico. Para criar alertas, salvar ou carregar uma consulta, o Log Analytics ter um workspace como escopo. Para abrir o Log Analytics no contexto do workspace, selecione **Logs** no menu do **Azure Monitor**. O último workspace usado é selecionado, mas você pode selecionar qualquer outro workspace. Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Por que estou recebendo o erro: "Registre o provedor de recursos 'Microsoft.Insights' para esta assinatura para habilitar essa consulta" ao abrir o Log Analytics em uma VM? 
Muitos provedores de recursos são automaticamente registrados, mas pode ser necessário registrar manualmente alguns provedores de recursos. O escopo de registro é sempre a assinatura. Para saber mais, veja [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Por que não estou obtendo uma mensagem de erro de acesso ao abrir o Log Analytics em VM? 
Para exibir os Logs da VM, você precisará receber permissão de leitura para os workspaces que armazenam os logs da VM. Nesses casos, o administrador deve conceder a você permissões no Azure.

## <a name="metrics"></a>Métricas

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Por que as métricas do SO convidado da minha máquina virtual do Azure não aparecem no Metrics Explorer?
[As métricas de plataforma](insights/monitor-azure-resource.md#monitoring-data) são coletadas automaticamente para recursos do Azure. Você deve executar algumas configurações para coletar métricas do SO convidado de uma máquina virtual. Para uma VM do Windows, instale a extensão de diagnóstico e configure o coletor do Azure Monitor conforme descrito em [Instalar e configurar a extensão WAD (Diagnóstico do Microsoft Azure)](platform/diagnostics-extension-windows-install.md). Para o Linux, instale o agente Telegraf conforme descrito em [Coletar métricas personalizadas para uma VM do Linux com o agente Telegraf do InfluxData](platform/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Alertas

### <a name="what-is-an-alert-in-azure-monitor"></a>O que é um alerta no Azure Monitor?
Os alertas trabalham de forma proativa, mandando notificações quando encontram condições importante em seus dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Há vários tipos de alertas:

- Métrica – o valor da métrica excede um limite.
- Consulta de log – os resultados de uma consulta de log correspondem aos critérios definidos.
- Log de atividades – o evento do log de atividades corresponde a critérios definidos.
- Teste na Web – os resultados dos critérios definidos de correspondência do teste de disponibilidade.


Confira [Visão geral dos alertas no Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>O que é um grupo de ações?
Um grupo de ações é uma coleção de notificações e ações que podem ser disparadas por um alerta. Vários alertas podem usar um grupo de ações, permitindo que você aproveite conjuntos comuns de notificações e ações. Confira [Criar e gerenciar grupos de ações no portal do Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>O que é uma regra de ação?
Uma regra de ação permite modificar o comportamento de um conjunto de alertas que correspondem a um determinado critério. Isso permite que você execute requisitos como desabilitar ações de alerta durante uma janela de manutenção. Você também pode aplicar um grupo de ações a um conjunto de alertas em vez de aplicá-los diretamente às regras de alerta. Confira [Regras de ação](platform/alerts-action-rules.md).

## <a name="agents"></a>Agentes

### <a name="does-azure-monitor-require-an-agent"></a>O Azure Monitor requer um agente?
Um agente só é necessário para coletar dados do sistema operacional e das cargas de trabalho em máquinas virtuais. As máquinas virtuais podem estar localizadas no Azure, em outro ambiente de nuvem ou localmente. Confira [Visão geral dos agentes do Azure Monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual é a diferença entre os agentes do Azure Monitor?
A extensão Diagnóstico do Azure é para máquinas virtuais do Azure e coleta dados para métricas do Azure Monitor, para o Armazenamento do Azure e para Hubs de Eventos do Azure. O agente do Log Analytics é para máquinas virtuais no Azure, outro ambiente de nuvem ou local e coleta dados para Logs do Azure Monitor. O Dependency Agent requer o agente do Log Analytics e os detalhes e as dependências do processo coletado. Confira [Visão geral dos agentes do Azure Monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>O agente de tráfego usa a minha conexão do ExpressRoute?
O tráfego para o Azure Monitor usa o circuito do ExpressRoute de emparelhamento da Microsoft. Confira a [documentação do ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para obter uma descrição dos diferentes tipos de tráfego do ExpressRoute. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Como faço para confirmar se o agente do Log Analytics é capaz de se comunicar com o Azure Monitor?
No Painel de Controle no computador do agente, selecione **Segurança & Configurações**, **Microsoft Monitoring Agent**. Na guia **Log Analytics do Azure (OMS)** , um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o Azure Monitor. Um ícone de aviso amarelo significa que o agente está tendo problemas. Uma motivo comum é que o serviço **Microsoft Monitoring Agent** foi interrompido. Use o gerenciador de controle de serviço para reiniciar o serviço.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Como faço para que o agente do Log Analytics pare de se comunicar com o Azure Monitor?
Para agentes conectados diretamente ao Log Analytics, abra o Painel de Controle e selecione **Segurança e Configurações**, **Microsoft Monitoring Agent**. Na guia **Log Analytics do Azure (OMS)** , remova todos os workspaces listados. No System Center Operations Manager, remova o computador da lista de computadores gerenciados do Log Analytics. O Operations Manager atualiza a configuração do agente para não relatar mais para o Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Qual a quantidade de dados enviada por agente?
A quantidade de dados enviados por agente depende:

* Das soluções que você habilitou
* Do número de logs e contadores de desempenho sendo coletados
* Do volume de dados nos logs

Confira [Gerenciar o uso e os custos com os Logs do Azure Monitor](platform/manage-cost-storage.md) para obter detalhes.

Para computadores capazes de executar o agente WireData, use a seguinte consulta para ver quantos dados estão sendo enviados:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanta largura de banda de rede é usada pelo MMA (Agente de Gerenciamento da Microsoft) ao enviar dados para o Azure Monitor?
A largura de banda é uma função na quantidade de dados enviados. Dados são compactados conforme eles são enviados pela rede.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Como faço para ser notificado quando a coleta de dados do agente do Log Analytics for interrompida?

Use as etapas descritas em [Criar um novo alerta do log](platform/alerts-metric.md) para ser notificado quando a coleta de dados for interrompida. Use as seguintes configurações para a regra de alerta:

- **Definir condição de alerta**: Especifica seu workspace do Log Analytics como o destino do recurso.
- **Critérios do alerta** 
   - **Nome do Sinal**: *Pesquisa de logs personalizada*
   - **Consulta de pesquisa**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de Alerta**: **Com base no** *número de resultados*, na **Condição** *maior que*, no **valor limite** *0*
   - **Avaliado com base em**: **Período (em minutos)** *30*, **Frequência (em minutos)** *10*
- **Definir detalhes do alerta** 
   - **Name**: *Interrupção da coleta de dados*
   - **Gravidade**: *Aviso*

Especifique um [Grupo de Ações](platform/action-groups.md) existente para que, quando o alerta do log corresponder aos critérios, você seja notificado se tiver uma pulsação ausente por mais de 15 minutos.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quais são os requisitos de firewall para agentes do Azure Monitor?
Confira [Requisitos de firewall de rede](platform/log-analytics-agent.md#network-requirements)para obter detalhes sobre os requisitos de firewall.


## <a name="visualizations"></a>Visualizações

### <a name="why-cant-i-see-view-designer"></a>Por que não consigo ver o Designer de Exibição?

O Designer de Exibição só está disponível para os usuários atribuídos com permissões de Colaborador ou superior no workspace do Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar:*

* [Aplicativo .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnóstico do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplicativo Web Java](app/java-troubleshoot.md)

*Não recebo dados do meu servidor:*

* [Definir exceções de firewall](app/ip-addresses.md)
* [Configurar um servidor ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app/java-agent.md)

*Quantos Application Insights devo implantar?:*

* [Como projetar sua implantação do Application Insights: Um ou muitos recursos do Application Insights?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>É possível usar o Application Insights com...?

* [Aplicativos Web em um servidor IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure](app/azure-vm-vmss-apps.md)
* [Aplicativos Web em um servidor IIS : em uma VM ou local](app/asp-net.md)
* [Aplicativos Web Java](app/java-get-started.md)
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
* (Somente novos projetos – se você [adicionar o Application Insights a um projeto existente][start], precisará fazer isso manualmente). Insere snippets no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/\_Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões anteriores do SDK?
Consulte as [notas de versão](app/release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?
No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Posso usar `providers('Microsoft.Insights', 'components').apiVersions[0]` em minhas implantações do Azure Resource Manager?

Não recomendamos o uso desse método de preenchimento da versão da API. A versão mais recente pode representar versões prévias que podem conter alterações da falha. Mesmo com versões mais recentes que não são versões prévias, as versões de API nem sempre são compatíveis com os modelos existentes ou, em alguns casos, a versão da API pode não estar disponível para todas as assinaturas.

### <a name="what-is-status-monitor"></a>O que é o Status Monitor?

Um aplicativo da área de trabalho que você pode usar no servidor Web do IIS para ajudar a configurar o Application Insights em aplicativos Web. Ele não coleta telemetria: você pode interrompê-lo quando não estiver configurando um aplicativo. 

[Saiba mais](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Qual a telemetria coletada pelo Application Insights?

A partir dos aplicativos Web do servidor:

* Solicitações HTTP
* [Dependências](app/asp-net-dependencies.md). Chamadas para: Banco de Dados SQL; chamadas HTTP para serviços externos; Azure Cosmos DB, tabela, armazenamento de blobs e fila. 
* [Exceções](app/asp-net-exceptions.md) e rastreamentos de pilha.
* [Contadores de desempenho](app/performance-counters.md) – se você usa [Status Monitor](app/monitor-performance-live-website-now.md), [Monitoramento do Azure para Serviços de Aplicativos](app/azure-web-apps.md), [Monitoramento do Azure para VM ou conjunto de dimensionamento de máquinas virtuais](app/azure-vm-vmss-apps.md) ou o [Gravador coletado do Application Insights](app/java-collectd.md).
* [Eventos e métricas personalizados](app/api-custom-events-metrics.md) que você codifica.
* [Logs de Rastreamento](app/asp-net-trace-logs.md) se você configurar o coletor apropriado.

A partir das [páginas da Web do cliente](app/javascript.md):

* [Contagens de exibição de página](app/usage-overview.md)
* [Chamadas AJAX](app/asp-net-dependencies.md) Solicitações feitas a partir de um script em execução.
* Dados de carregamento de exibição de página
* Contagens de sessão e usuários
* [IDs de Usuário Autenticado](app/api-custom-events-metrics.md#authenticated-users)

A partir de outras fontes, se você configurá-las:

* [Diagnóstico do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importar no Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Eu posso filtrar ou modificar alguma telemetria?

Sim, no servidor você pode gravar:

* Processador de Telemetria para filtrar ou adicionar propriedades a itens de telemetria selecionados antes de serem enviados do seu aplicativo.
* Inicializador de Telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre [ASP.NET](app/api-filtering-sampling.md) ou [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como os dados de cidade, país/região e outros dados de localização geográfica são calculados?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente Web usando [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: Coletamos o endereço IP do remetente.
* Telemetria do servidor: O módulo Application Insights coleta o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver configurado.
* Para saber mais sobre como o endereço IP e os dados de localização geográfica são coletados no Application Insights consulte este [artigo](./app/ip-collection.md).


É possível configurar o `ClientIpHeaderTelemetryInitializer` para coletar o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP`. [Saiba mais](https://apmtips.com/posts/2016-07-05-client-ip-address/).

É possível [usar o Power BI](app/export-power-bi.md ) para exibir sua telemetria de solicitação em um mapa.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?
Veja [Privacidade e retenção de dados][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>O que acontece com a telemetria do Application Insights quando um servidor ou dispositivo perde a conexão com o Azure?

Todos os nossos SDKs, incluindo o SDK da Web, incluem "transporte confiável" ou "transporte robusto". Quando o servidor ou dispositivo perde a conexão com o Azure, a telemetria é [armazenada localmente no sistema de arquivos](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (SDKs do servidor) ou no Armazenamento de Sessão do HTML5 (SDK da Web). Periodicamente, o SDK tentará enviar novamente essa telemetria até que o serviço de ingestão o considere "obsoleto" (48 horas para logs, 30 minutos para métricas). A telemetria obsoleta será descartada. Em alguns casos, como quando o armazenamento local está cheio, a repetição não ocorrerá.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>É possível enviar dados pessoais na Telemetria?

Isso é possível se o seu código envia tais dados. Isso também pode acontecer se as variáveis nos rastreamentos de pilha incluírem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais sejam devidamente tratados. [Saiba mais sobre privacidade e retenção de dados ](app/data-retention-privacy.md).

**Todos** os octetos do endereço web do cliente são sempre definidos como 0 depois que os atributos de localização geográfica são pesquisados.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Minha Chave de Instrumentação está visível na origem da minha página da Web. 

* Essa é uma prática comum em soluções de monitoramento.
* Ele não pode ser usado para roubar seus dados.
* Ele pode ser usado para distorcer seus dados ou disparar alertas.
* Não temos conhecimento se algum cliente teve tais problemas.

Você pode:

* Usar duas Chaves de instrumentação separadas (recursos do Application Insights separados) para dados do servidor e cliente. Ou
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
* Se não houver nenhum script do lado do cliente, você poderá [definir cookies no server](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
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

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas do [Analytics](./log-query/log-query-overview.md).

### <a name="how-can-i-set-an-alert-on-an-event"></a>Como configurar um alerta em um evento?

Os alertas do Azure são somente em métricas. Crie uma métrica personalizada que cruze um limite de valor sempre que o evento ocorrer. Em seguida, configure um alerta na métrica. Você receberá uma notificação sempre que a métrica cruzar o limite em qualquer direção; você não receberá uma notificação até o primeiro cruzamento, não importando se o valor inicial é alto ou baixo; sempre haverá uma latência de alguns minutos.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Há encargos de transferência de dados entre um aplicativo Web e o Application Insights?

* Se seu aplicativo Web do Azure estiver hospedado em um data center, onde há um ponto de extremidade de coleta do Application Insights, não haverá cobrança. 
* Se não houver um ponto de extremidade de coleta no data center do host, então, a telemetria do seu aplicativo incorrerá em [Encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde seu recurso Application Insights está hospedado. Depende apenas da distribuição de nossos pontos de extremidade.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>É possível enviar telemetria para o portal do Application Insights?

É recomendável usar nossos SDKs e usar a [API de SDK](app/api-custom-events-metrics.md). Existem variantes do SDK para várias [plataformas](app/platforms.md). Esses SDKs tratam buffer, compressão, limitação, repetições e, assim por diante. No entanto, o [esquema de ingestão](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) e o [protocolo de ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

### <a name="can-i-monitor-an-intranet-web-server"></a>É possível monitorar um servidor Web de intranet?

Sim, mas você precisará permitir o tráfego para nossos serviços por exceções de firewall ou redirecionamentos de proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Examine nossa lista de serviços e endereços IP [aqui](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Exceção de firewall

Permita que o servidor Web envie telemetria para os pontos de extremidade. 

#### <a name="gateway-redirect"></a>Redirecionamento de gateway

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
> O ApplicationIdProvider está disponível desde a v2.6.0.



#### <a name="proxy-passthrough"></a>Passagem de proxy

A passagem de proxy pode ser obtida com a configuração de um proxy de nível de computador ou de nível de aplicativo.
Para obter mais informações, confira o artigo do dotnet sobre [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo de Web.config:
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

### <a name="health-feature-is-in-private-preview"></a>O recurso de integridade está em versão prévia privada

Estamos planejando fazer uma série de alterações para adicionar funcionalidades e abordar seus comentários. O recurso de integridade fará transição para uma versão prévia privada no final de junho de 2020 e, para obter mais informações, examine este [comunicado sobre atualizações do Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/).

### <a name="what-does-other-processes-represent-under-the-node-view"></a>O que *Outros processos* representam na Exibição de nó?

Esses **Outros processos** servem para ajudá-lo a entender claramente a causa raiz do alto uso de recursos em seu nó. Isso permite que você diferencie o uso entre processos em contêineres e processos que não estão em contêineres.

Quais são esses **Outros processos**? 

Esses são processos que não estão em contêineres e são executados no seu nó.  

Como calculamos isso?

**Outros processos** = *Uso total de CAdvisor* - *Uso de processo em contêiner*

Os **Outros processos** incluem:

- Processos de Kubernetes autogerenciados ou gerenciados que não estão em contêineres 

- Processos de tempo de execução de contêiner  

- Kubelet  

- Processos do sistema em execução no seu nó 

- Outras cargas de trabalho que não são de Kubernetes em execução no hardware do nó ou em VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo os valores das propriedades Image e Name preenchidos quando consulto a tabela ContainerLog.

Para agente de versão ciprod12042019 e posteriores, por padrão, essas duas propriedades não são preenchidas para cada linha de log para minimizar os custos com os dados de log coletados. Há duas opções para consultar a tabela que inclui essas propriedades com seus valores:

#### <a name="option-1"></a>Opção 1 

Fazer junção de outras tabelas para incluir esses valores de propriedade nos resultados.

Modifique suas consultas para incluir as propriedades Image e ImageTag da tabela ```ContainerInventory``` fazendo junção na propriedade ContainerID. Você pode incluir a propriedade Name (pois ela apareceu anteriormente na tabela ```ContainerLog```) do campo ContaineName da tabela KubepodInventory fazendo junção na propriedade ContainerID. Essa é a opção recomendada.

O exemplo a seguir é uma consulta detalhada de exemplo que explica como obter esses valores de campo por meio de junções.

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

Reabilitar a coleção para essas propriedades para cada linha de log de contêiner.

Se a primeira opção não for conveniente devido a alterações de consulta envolvidas, você poderá reabilitar a coleta desses campos habilitando a configuração ```log_collection_settings.enrich_container_logs``` no mapa de configuração do agente, conforme descrito em [definições de configuração de coleta de dados](insights/container-insights-agent-config.md).

> [!NOTE]
> A segunda opção não é recomendada com clusters grandes que têm mais de 50 nós porque ela gera chamadas ao servidor de API em cada nó do cluster para executar esse enriquecimento. Essa opção também aumenta o tamanho dos dados para cada linha de log coletada.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Posso exibir as métricas coletadas no Grafana?

O Azure Monitor para contêineres é compatível com a exibição de métricas armazenadas em seu workspace do Log Analytics em painéis do Grafana. Nós fornecemos um modelo para baixar do [repositório do painel](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do Grafana para você começar e fazer referência e para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em painéis personalizados do Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorar meu cluster do AKS-Engine com o Azure Monitor para contêineres?

O Azure Monitor para contêineres é compatível com o monitoramento de cargas de trabalho de contêiner implantadas em clusters do AKS-Engine (anteriormente conhecido como ACS-Engine) hospedados no Azure. Para obter mais detalhes e uma visão geral das etapas necessárias para habilitar o monitoramento para esse cenário, confira [Usando o Azure Monitor para contêineres para o AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados em meu workspace do Log Analytics?

Se não for possível ver dados no workspace do Log Analytics em um determinado horário, talvez tenha sido atingido o limite de 500 MB padrão ou o limite diário especificado para controlar a quantidade de dados a serem coletados diariamente. Quando o limite do dia for atingido, a coleta de dados será interrompida e retomada somente no próximo dia. Para examinar seu uso de dados e atualizar para um tipo de preço diferente com base em seus padrões de uso previstos, confira [Uso e custos de dados de log](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>O que são os estados de contêineres especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres parados e em execução. A tabela é preenchida por um fluxo de trabalho dentro do agente que consulta o docker por todos os contêineres (em execução e parados) e encaminha esses dados ao espaço de trabalho do Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como faço para resolver o erro *Registro de assinatura ausente*?

Se você receber o erro **Registro de assinatura ausente para o Microsoft.OperationsManagement**, resolva-o registrando o provedor de recursos **Microsoft.OperationsManagement** na assinatura em que o workspace está definido. A documentação para saber como fazer isso pode ser encontrada [aqui](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para clusters do AKS habilitados para RBAC?

A solução de monitoramento de contêiner não é compatível com o RBAC, mas é compatível com o Azure Monitor para Contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados desses clusters.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como habilito a coleta de log para contêineres no namespace kube-system por meio do Helm?

A coleta de log de contêineres no namespace kube-system está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para saber mais, confira a página do GitHub [Azure Monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers). 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como posso atualizar o omsagent para a versão mais recente?

Para saber como atualizar o agente, confira [Gerenciamento de agente](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Como habilito o registro em log de várias linhas?

No momento o Azure Monitor para contêineres não é compatível com registro em log de várias linhas, mas há soluções alternativas disponíveis. Você pode configurar todos os serviços para gravar em formato JSON e, em seguida, o Docker/Moby vai gravá-los como uma única linha.

Por exemplo, você pode encapsular seu log como um objeto JSON, conforme mostrado no exemplo de um aplicativo do Node.js abaixo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados serão parecidos com o seguinte exemplo no Azure Monitor para logs ao consultá-los:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para detalhes sobre esse problema, examine o [link do GitHub](https://github.com/moby/moby/issues/22920) a seguir.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como resolvo erros do Azure AD ao habilitar logs dinâmicos? 

Você poderá ver o seguinte erro: **A URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: '<ID do aplicativo\>'** . A solução para isso pode ser encontrada no artigo [Como exibir dados de contêiner em tempo real com o Azure Monitor para contêineres](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster após a integração?

Se, depois de habilitar o Azure Monitor para contêineres para um cluster do AKS, você excluir o workspace do Log Analytics ao qual o cluster estava enviando os dados, ao tentar atualizar o cluster, ele falhará. Para contornar isso, você precisará desabilitar o monitoramento e reabilitá-lo fazendo referência a um workspace diferente válido em sua assinatura. Quando você tentar executar a atualização do cluster novamente, isso deverá ser processado e concluído com êxito.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quais portas e domínios eu preciso abrir/adicionar à lista de permissões para o agente?

Confira os [Requisitos de firewall de rede](insights/container-insights-onboard.md#network-firewall-requirements) para obter as informações de configuração de proxy e firewall necessárias para o agente em contêiner com as nuvens do Azure, do Governo dos EUA do Azure e do Azure China 21Vianet.

## <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para VMs. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

### <a name="can-i-onboard-to-an-existing-workspace"></a>É possível fazer a integração com um workspace existente?
Se as máquinas virtuais já estiverem conectadas a um espaço de trabalho do Log Analytics, você poderá continuar usando esse espaço de trabalho durante a integração com o Azure Monitor para VMs, desde que ele esteja em uma das regiões compatíveis listadas [aqui](insights/vminsights-enable-overview.md#prerequisites).


### <a name="can-i-onboard-to-a-new-workspace"></a>É possível fazer a integração com um novo workspace? 
Se, atualmente, as VMs não estiverem conectadas a um espaço de trabalho do Log Analytics existente, você precisará criar um espaço de trabalho para armazenar os dados. A criação de um workspace padrão será feita automaticamente se você configurar uma VM individual do Azure para o Azure Monitor para VMs por meio do portal do Azure.

Se você optar por usar o método baseado em script, veja essas etapas no artigo [Habilitar o Azure Monitor para VMs usando o Azure PowerShell ou o modelo do Resource Manager](insights/vminsights-enable-at-scale-powershell.md). 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que fazer se minha VM já estiver subordinada a um workspace existente?
Caso você já esteja coletando dados de suas máquinas virtuais, talvez você já tenha configurado uma delas para relatar os dados para um espaço de trabalho do Log Analytics existente.  Desde que esse workspace esteja em uma de nossas regiões compatíveis, você poderá habilitar o Azure Monitor para VMs nesse workspace pré-existente.  Se o workspace que você já está usando não estiver em uma de nossas regiões compatíveis, você não conseguirá fazer a integração ao Azure Monitor para VMs nesse momento.  Estamos trabalhando ativamente para dar suporte a outras regiões.


### <a name="why-did-my-vm-fail-to-onboard"></a>Por que minha VM não pôde ser integrada?
Ao fazer a integração de uma VM do Azure por meio do portal do Azure, ocorrem as seguintes etapas:

* Um espaço de trabalho do Log Analytics padrão é criado, caso essa opção tenha sido selecionada.
* O agente do Log Analytics será instalado nas VMs do Azure usando uma extensão de VM, se for determinado que isso é necessário.  
* O Dependency Agent do Mapa do Azure Monitor para VMs será instalado nas VMs do Azure usando uma extensão, se for determinado que isso é necessário. 

Durante o processo de integração, verificamos o status de cada um dos itens acima para retornar um status de notificação a você no portal. A configuração do workspace e a instalação do agente normalmente levam de 5 a 10 minutos. A exibição dos dados de monitoramento no portal leva 5 a 10 minutos adicionais.  

Se você tiver iniciado a integração e vir mensagens indicando que a VM precisa ser integrada, permita uma margem de até 30 minutos para que a VM conclua o processo. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo alguns dados ou nenhum dado nos gráficos de desempenho de minha VM
Nossos gráficos de desempenho foram atualizados para usar dados armazenados na tabela *InsightsMetrics*.  Para ver os dados nesses gráficos, você precisará atualizar para usar a nova solução VM Insights.  Vejas nossas [perguntas frequentes sobre a GA](insights/vminsights-ga-release-faq.md) para obter informações adicionais.

Se você não vir dados de desempenho na tabela de disco ou em alguns dos gráficos de desempenho, os contadores de desempenho poderão não estar configurados no workspace. Para resolver isso, execute o [script do PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell) a seguir.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Qual a diferença entre o recurso Mapa do Azure Monitor para VMs e o Mapa do Serviço?
O recurso Mapa do Azure Monitor para VMs baseia-se no Mapa do Serviço, mas tem as seguintes diferenças:

* A exibição de Mapa pode ser acessada por meio da folha da VM e do Azure Monitor para VMs no Azure Monitor.
* As conexões no Mapa agora são clicáveis e mostram uma exibição dos dados de métrica de conexão no painel lateral da conexão selecionada.
* Há uma nova API que é usada para criar os mapas, a fim de dar um melhor suporte a mapas mais complexos.
* As VMs monitoradas agora estão incluídas no nó do grupo de clientes e o gráfico de rosca mostra a proporção de máquinas virtuais monitoradas vs. não monitoradas no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* As máquinas virtuais monitoradas agora estão incluídas nos nós do grupo de portas do servidor e o gráfico de rosca mostra a proporção de computadores monitorados vs. não monitorados no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o Mapa do Aplicativo do Application Insights.
* Os painéis laterais foram atualizados e não têm o conjunto completo de integrações que eram compatíveis com o Mapa do Serviço: Gerenciamento de Atualizações, Controle de Alterações, Segurança e Central de Serviços. 
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

A página de visão geral de uma VM do Azure exibe gráficos com base na medida do host da atividade na VM convidada.  Para o gráfico de rede na Visão Geral da VM do Azure, ela exibe apenas o tráfego de rede que será cobrado.  Isso não inclui o tráfego de rede entre redes virtuais.  Os dados e os gráficos mostrados para o Azure Monitor para VMs se baseiam nos dados da VM de convidado e o gráfico de rede exibe todo o tráfego de TCP/IP de entrada e saída nessa VM, incluindo o tráfego entre redes virtuais.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como o tempo de resposta é medido para os dados armazenados na VMConnection e exibidos no painel de conexão e nas pastas de trabalho?

O tempo de resposta é uma aproximação. Como não instrumentamos o código do aplicativo, não sabemos realmente quando uma solicitação começa e quando a resposta chega. Em vez disso, observamos os dados que estão sendo enviados em uma conexão e, em seguida, os dados que voltam nessa conexão. Nosso agente controla esses envios e recebimentos e tenta emparelhá-los: uma sequência de envios, seguida por uma sequência de recebimentos é interpretada como um par de solicitação/resposta. O intervalo entre essas operações é o tempo de resposta. Isso incluirá a latência de rede e o tempo de processamento do servidor.

Essa aproximação funciona bem para protocolos que são baseados em solicitação/resposta: uma solicitação sai da conexão e uma resposta chega. Esse é o caso para HTTP (sem pipeline), mas não funciona para outros protocolos.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Quais são as limitações se estiver no plano de preços Gratuito do Log Analytics?
Se você configurou o Azure Monitor com um espaço de trabalho do Log Analytics usando o tipo de preço *Gratuito*, o recurso Mapa do Azure Monitor para VMs dará suporte a apenas cinco máquinas conectadas ao espaço de trabalho. Se houver cinco VMs conectadas a um workspace gratuito, ao desconectar uma das VMs e depois conectar uma nova VM, a nova VM não será monitorada e refletida na página Mapa.  

Nessa condição, uma opção **Experimentar Agora** será exibida quando você abrir a VM e selecionar **Insights** no painel esquerdo, mesmo após já ter sido instalado na VM.  No entanto, não serão exibidas as opções como normalmente ocorreria se essa VM não estivesse incorporada ao Azure Monitor para VMs. 


## <a name="next-steps"></a>Próximas etapas
Se a sua pergunta não estiver respondida aqui, confira os fóruns a seguir para ver outras perguntas e respostas.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Para ver comentários gerais sobre o Azure Monitor, visite o [fórum de comentários](https://feedback.azure.com/forums/34192--general-feedback).
