---
title: Recursos do Centro de Aposentadoria de Segurança (julho de 2019) | Microsoft Docs
description: Este artigo descreve as características do Security Center que foram aposentados em 31 de julho de 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fda49ae31d7598497a3128a846a3f4e12e84ffe1
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435902"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Recursos do Centro de Aposentadoria de Segurança (julho de 2019)

> [!NOTE]
> Este documento detalha a lista de recursos que foram retirados do Azure Security Center em 31 de julho de 2019.
>
>

Fizemos várias [melhorias](https://azure.microsoft.com/updates/?product=security-center) no Azure Security Center nos seis meses anteriores a julho de 2019.
Com esses recursos aprimorados, removemos alguns recursos redundantes e APIs relacionadas do Security Center em 31 de julho de 2019.

A maioria desses recursos aposentados pode ser substituída por outras funcionalidades no Azure Security Center ou no Azure Log Analytics. Outros recursos podem ser implementados usando [o Azure Sentinel (visualização)](https://azure.microsoft.com/services/azure-sentinel/).

Os recursos do Centro de Segurança aposentado incluem:

- [Painel de eventos](#menu_events)
- [Entrada do menu de pesquisa](#menu_search)
- [Exibir o link clássico de identidade & acesso à identidade e acesso (visualização)](#menu_classicidentity)
- [Botão de mapa de eventos de segurança no mapa de alertas de segurança (visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (visualização)](#menu_customalerts)
- [Investigue o botão em alertas de segurança de proteção contra ameaças](#menu_investigate)
- [Um subconjunto de soluções de Segurança](#menu_solutions)
- [Editar configurações de segurança para políticas de segurança](#menu_securityconfigurations)
- [Painel de segurança e auditoria (originalmente usado no portal OMS) para espaços de trabalho do Log Analytics](#menu_securityomsdashboard)

Este artigo fornece informações detalhadas para cada recurso aposentado e as etapas que você pode tomar para implementar recursos de substituição.

## <a name="events-dashboard"></a>Painel de eventos<a name="menu_events"></a>

O Security Center usa o agente Log Analytics para coletar várias configurações e eventos relacionados à segurança de suas máquinas. Ele armazena esses eventos em seus espaços de trabalho. O [painel de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite que você visualize esses dados e lhe dá um ponto de entrada para o Log Analytics.

Aposentamos o painel de eventos que apareceu quando você selecionou um espaço de trabalho:

![Painel de eventos][2]

### <a name="events-dashboard---the-new-experience"></a>Painel de eventos - a nova experiência

Nós encorajamos você a usar os recursos nativos do Azure Log Analytics para visualizar eventos notáveis em seus espaços de trabalho.

Se você criou eventos notáveis personalizados no Security Center, eles estarão acessíveis. No Log Analytics, vá para Selecionar pesquisas**salvas** **no espaço** > de trabalho . Seus dados não serão perdidos ou modificados. Eventos notáveis nativos também estão disponíveis na mesma tela no Log Analytics.

![Espaço de trabalho salvou pesquisas][3]

## <a name="search-menu-entry"></a>Entrada do menu de pesquisa<a name="menu_search"></a>

Atualmente, o Azure Security Center usa a pesquisa de logs do Azure Monitor para recuperar e analisar seus dados de segurança. Essa tela serve como uma janela para a página de pesquisa do Log Analytics e permite que os usuários executem consultas de pesquisa em seu espaço de trabalho selecionado. Para obter mais informações, consulte [a pesquisa do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Nós aposentamos esta janela de busca:

![Página de pesquisa][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrada do menu de pesquisa - a nova experiência

Nós encorajamos você a usar os recursos nativos do Azure Log Analytics para executar consultas de pesquisa em seus espaços de trabalho. Vá para Azure Log Analytics e selecione **Logs**.

![Página de log analytics][5]

## <a name="classic-identity--access-preview"></a>Acesso ao & de identidade clássica (Pré-visualização)<a name="menu_classicidentity"></a>

A experiência classicidentity & access no Security Center atualmente mostra um painel de informações de identidade e acesso no Log Analytics. Para visualizar este painel:

1. Selecione **Exibir o acesso & identidade clássico**.

   ![Página de identidade][6]

1. Exibir o **painel identidade & access**.

    ![Página de identidade - seleção de espaço de trabalho][7]

1. Selecione um espaço de trabalho para abrir o painel **identidade & access** no Log Analytics para visualizar informações de identidade e acesso em seu espaço de trabalho.

   ![Página de identidade - painel][8]

Aposentamos todas as três telas mostradas nas etapas anteriores. Seus dados permanecem disponíveis na solução de segurança do Log Analytics e não foram modificados ou removidos.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) - a nova experiência

O painel do Log Analytics mostrou insights sobre um único espaço de trabalho. No entanto, os recursos nativos do Security Center fornecem visibilidade a todas as assinaturas e a todos os espaços de trabalho associados a eles. Você pode acessar uma visão fácil de usar que permite que você se concentre no que é importante com as recomendações classificadas de acordo com seu Secure Score.

Todos os recursos do painel **identidade & de acesso** no Log Analytics podem ser acessados selecionando o Acesso ao & de Identidade **(Preview)** dentro do Security Center.

![Página de identidade - aposentadoria experiência clássica][9]

## <a name="security-events-map"></a>Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>

O Security Center fornece um [mapa de alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) para ajudar a identificar ameaças à segurança. O botão **Ir para eventos de segurança** no mapa abre um painel que permite visualizar eventos de segurança brutos no espaço de trabalho selecionado.

Removemos o botão **Ir para o mapa de eventos de segurança** e o painel de espaço de trabalho.

![Mapa de alertas de segurança - botão][10]

Quando você seleciona o botão **Ir para o mapa de eventos de segurança,** ele abriu o painel de inteligência de ameaças (agora aposentado).

![Painel Inteligência contra ameaças][11]

Quando você escolhe um espaço de trabalho para visualizar seu painel de inteligência de ameaças, você abriu a tela (agora aposentada) de alertas de segurança (visualização) no Log Analytics.

![Mapa de alertas de segurança no Log Analytics][12]

Os dados existentes permanecem disponíveis na solução de segurança do Log Analytics e não foram modificados ou removidos.

### <a name="security-events-map---the-new-experience"></a>Mapa de eventos de segurança - a nova experiência

Nós encorajamos você a usar a funcionalidade do mapa de alertas incorporado no Security Center: **Mapa de alertas de segurança (Preview)**. Essa funcionalidade oferece uma experiência otimizada e funciona em todas as assinaturas e espaços de trabalho associados. Ele lhe dá uma visão de alto nível em todo o seu ambiente e não está focado em um único espaço de trabalho.

## <a name="custom-alert-rules-preview"></a>Regras de alerta personalizadas (Visualização)<a name="menu_customalerts"></a>

[Aposentamos a experiência de alertas personalizados](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) em 30 de junho de 2019 porque sua infra-estrutura subjacente foi aposentada. Após a data da aposentadoria, os alertas de segurança personalizados não são mais gerados.
Recomendamos que você [habilite o Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e recrie seus alertas personalizados lá. Alternativamente, você pode criar seus alertas com alertas de log do Azure Monitor.

Para criar alertas personalizados com o Azure Sentinel:

1. [Abra o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) e selecione o espaço de trabalho onde seus alertas personalizados são armazenados
1. Selecione **Análises** no menu
1. Siga as instruções no [tutorial](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) a seguir sobre como criar alertas personalizados no Azure Sentinel

Se você não estiver interessado em usar o Azure Sentinel, você pode criar seus alertas com alertas de log do Azure Monitor. Para obter instruções, consulte [Criar, visualizar e gerenciar alertas de log usando alertas de monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [log do Azure no Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertas personalizados][13]

Para obter mais informações sobre a aposentadoria de alertas personalizados, consulte [Regras de alerta personalizadas no Azure Security Center (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Investigação de alertas de segurança<a name="menu_investigate"></a>

[O recurso de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) no Security Center ajuda você a triagem de um possível incidente de segurança. O recurso permite que você entenda o escopo de um incidente e rastreie sua causa raiz. Removemos esse recurso do Security Center porque ele foi substituído por uma experiência melhorada no [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Quando você seleciona o botão **Investigar** em uma tela **de incidente de segurança,** você abre o Painel de Investigação (Preview) no Log Analytics. Nós aposentamos o Painel de Investigação.

Os dados existentes permanecem disponíveis na solução de segurança do Log Analytics e não foram modificados ou removidos.

![Painel de investigação no Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Investigação - a nova experiência

Nós encorajamos você a fazer a transição para [o Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) para uma rica experiência de investigação. O Azure Sentinel fornece poderosas ferramentas de pesquisa e consulta para caçar ameaças de segurança nas fontes de dados da sua organização.

## <a name="subset-of-security-solutions"></a>Subconjunto de soluções de segurança<a name="menu_solutions"></a>

O Security Center pode habilitar [soluções integradas de segurança no Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Aposentamos as seguintes soluções de parceiros do Security Center. Essas soluções são habilitadas no [Azure Sentinel,](https://azure.microsoft.com/services/azure-sentinel/) juntamente com uma série de fontes de dados adicionais.

- [Soluções de firewall e firewall de aplicativos web de próxima geração](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integração de soluções de segurança que suportam o Formato de Evento Comum (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Proteção de identidade Azure AD](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Após a aposentadoria, você não pode adicionar ou modificar qualquer um dos tipos de solução mencionados na lista anterior, seja da UI ou da API. O Azure Security Center não descobrirá mais novas instâncias dessas soluções parceiras.

Se você tem soluções conectadas existentes, nós encorajamos você a se mudar para o Azure Sentinel.

![Soluções de centros de segurança][16]

## <a name="edit-security-configurations-for-security-policies"></a>Editar configurações de segurança para políticas de segurança<a name="menu_securityconfigurations"></a>

A Central de Segurança do Azure monitora as configurações de segurança aplicando um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o sistema operacional. Essas regras dizem respeito a firewalls, auditoria, políticas de senha e muito mais. Se uma configuração vulnerável é encontrada em um computador, a Central de Segurança gera uma recomendação de segurança. A [tela de configuração de segurança Editar](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizem a configuração padrão de segurança do SISTEMA OPERACIONAL no Security Center.

Nós aposentamos este recurso de pré-visualização. Para redefinir suas configurações de segurança de volta aos seus valores padrão após a data de aposentadoria, faça-o através da API ou Powershell usando as [seguintes instruções](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization).

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---the-new-experience"></a>Editar configurações de segurança - a nova experiência

Pretendemos habilitar o Security Center para suportar o [agente de configuração Convidado](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Essa atualização permitirá um conjunto de recursos muito mais rico, incluindo suporte a mais sistemas operacionais e integração de políticas de hóspedes do Azure para configurações de hóspedes. Depois que essas alterações forem habilitadas, você também terá a capacidade de controlar configurações em escala e aplicá-las automaticamente a novos recursos.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Painel de segurança e auditoria para espaços de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O painel de segurança e auditoria foi originalmente utilizado no portal OMS. No Log Analytics, o painel fornece uma visão geral por espaço de trabalho de eventos e ameaças de segurança notáveis, um mapa de inteligência de ameaças e uma avaliação de identidade e acesso de eventos de segurança salvos no espaço de trabalho. Removemos o painel. Como já recomendamos na ui do painel, aconselhamos que você faça a transição para o Azure Security Center.

![Painel de segurança do Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Painel de segurança e auditoria - a nova experiência

Aconselhamos que você mude para o Azure Security Center. Ele fornece a mesma visão geral de segurança em várias assinaturas e nos espaços de trabalho associados a eles, além de um conjunto de recursos mais rico.

Você pode obter as consultas originais do Log Analytics que preenchem o painel de segurança e auditoria no [repositório gitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) para o Security Center.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Saiba mais sobre [o Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
