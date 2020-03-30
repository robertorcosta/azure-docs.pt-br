---
title: Relatórios de planos & implantação de monitoramento - Azure AD
description: Descreve como planejar e executar a impugnação de relatórios e monitoramentos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232116"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planeje um relatório de relatórios e monitoramento do Azure Active Directory

A solução de relatórios e monitoramento do Azure Active Directory (Azure AD) depende de seus requisitos legais, de segurança e operacionais e do ambiente e processos existentes. Este artigo apresenta as várias opções de design e orienta você para a estratégia de implantação correta.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Benefícios do relatório e monitoramento do Azure AD

O relatório Azure AD fornece uma visão abrangente e registros da atividade do Azure AD em seu ambiente, incluindo eventos de login, eventos de auditoria e alterações em seu diretório.

Os dados fornecidos permitem a você:

* determinar como seus aplicativos e serviços são usados.

* detectar riscos potenciais que afetam a saúde do seu ambiente.

* problemas de solução de problemas impedindo que seus usuários advindos de seu trabalho feito.

* obtenha insights vendo eventos de auditoria de alterações no seu diretório Azure AD.

> [!IMPORTANT]
> O monitoramento do Azure AD permite que você encaminhe seus registros gerados pelo azure AD ereportando para diferentes sistemas de destino. Você pode mantê-lo para uso de longo prazo ou integrá-lo com ferramentas de gerenciamento de eventos e informações de segurança (SIEM) de terceiros para obter informações sobre seu ambiente.

Com o monitoramento do Azure AD, você pode encaminhar logs para:

* uma conta de armazenamento do Azure para fins de arquivamento.
* Logs do Azure Monitor, anteriormente conhecido como espaço de trabalho do Azure Log Analytics, onde você pode analisar os dados, criar dashboards e alertar sobre eventos específicos.
* um hub de eventos Azure onde você pode se integrar com suas ferramentas SIEM existentes, como Splunk, Sumologic ou QRadar.

> [!NOTE]
Recentemente começamos a usar o termo Logs do Monitor Azure em vez de Log Analytics. Os dados de log ainda são armazenados em um espaço de trabalho do Log Analytics e ainda são coletados e analisados pelo mesmo serviço do Log Analytics. Estamos atualizando a terminologia para refletir melhor a função dos [logs no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Confira as [alterações de terminologia do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) para obter detalhes.

[Saiba mais sobre as políticas de retenção de relatórios.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenciamento e pré-requisitos para relatórios e monitoramento de Azure AD

Você precisará de uma licença premium Azure AD para acessar os logs de login do Azure AD.

Para obter informações detalhadas sobre recursos e licenciamento no [guia de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para implantar o monitoramento e o relatório do Azure AD, você precisará de um usuário que seja um administrador global ou administrador de segurança para o inquilino Azure AD.

Dependendo do destino final dos seus dados de registro, você precisará de um dos seguintes:

* Uma conta de armazenamento do Azure para a qual você tem permissões ListKeys. Recomendamos que você use uma conta de armazenamento geral e não uma conta do Armazenamento de blobs. Para obter informações sobre preços de armazenamento, confira a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Um namespace do Azure Event Hubs para se integrar com soluções SIEM de terceiros.

* Um espaço de trabalho do Log Analytics do Azure para enviar logs aos logs do Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planeje um projeto de implantação de relatórios e monitoramento do Azure

Neste projeto, você definirá as audiências que consumirão e monitorarão relatórios e definirá sua arquitetura de monitoramento Azure AD.

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, eles normalmente o fazem devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando as partes interessadas certas.](https://aka.ms/deploymentplans) Também garantir que as funções dos stakeholders no projeto sejam bem compreendidas documentando os stakeholders e suas contribuições e responsabilidades do projeto.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique proativamente com seus usuários como sua experiência mudará, quando mudará e como ganhar suporte se eles tiverem problemas.

### <a name="document-your-current-infrastructure-and-policies"></a>Documente sua infra-estrutura e políticas atuais

Sua infra-estrutura e políticas atuais impulsionarão seu projeto de relatórios e monitoramento. Certifique-se de que você sabe

* O que, se houver, ferramentas SIEM que você está usando.

* Sua infra-estrutura do Azure, incluindo contas de armazenamento existentes e monitoramento sendo usado.

* Suas políticas de retenção organizacional para logs, incluindo quaisquer estruturas de conformidade aplicáveis necessárias. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planeje uma implantação de relatórios e monitoramento de relatórios e monitoramento do Azure

Relatórios e monitoramento são usados para atender às necessidades de seus negócios, obter insights sobre padrões de uso e aumentar a postura de segurança de suas organizações.

### <a name="business-use-cases"></a>Casos de uso de negócios

* Necessária para solução para atender às necessidades dos negócios
* É bom ter que atender às necessidades dos negócios
* Não aplicável

|Área |Descrição |
|-|-|
|Retenção| **Retenção de registro de mais de 30 dias**. Devido aos requisitos legais ou comerciais, é necessário armazenar registros de auditoria e fazer logins do Azure AD por mais de 30 dias. |
|Análise| **Os registros precisam ser pesquisados.** Os logs armazenados precisam ser pesquisados com ferramentas analíticas. |
| Insights Operacionais| **Insights para várias equipes.** A necessidade de dar acesso a diferentes usuários para obter insights operacionais, como uso de aplicativos, erros de login, uso de autoatendimento, tendências, etc. |
| Insights de segurança| **Insights para várias equipes.** A necessidade de dar acesso a diferentes usuários para obter insights operacionais, como uso de aplicativos, erros de login, uso de autoatendimento, tendências, etc. |
| Integração em sistemas SIEM      | **Integração SIEM**. A necessidade de integrar e transmitir o Azure AD registra logs e registros de auditoria aos sistemas SIEM existentes. |

### <a name="choose-a-monitoring-solution-architecture"></a>Escolha uma arquitetura de solução de monitoramento

Com o monitoramento do Azure AD, você pode encaminhar seus registros de atividades do Azure AD para um sistema que melhor atenda às suas necessidades de negócios. Em seguida, você pode retê-los para relatórios e análises de longo prazo para obter insights sobre o seu ambiente e integrá-lo com ferramentas SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Fluxograma de decisão![Uma imagem mostrando o que é descrito nas seções subseqüentes](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Ao direcionar logs para uma conta de armazenamento Do Zure, você pode mantê-los por mais tempo do que o período de retenção padrão descrito em nossas [políticas de retenção](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Use este método se você precisar arquivar seus logs, mas não precisa integrá-los a um sistema SIEM e não precisa de consultas e análises contínuas. Você ainda pode fazer pesquisas demanda.

Saiba como [encaminhar os dados para sua conta de armazenamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Enviar logs para os logs do Azure Monitor

[Os registros do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolidam dados de monitoramento de diferentes fontes. Ele também fornece um mecanismo de linguagem e análise de consulta que fornece insights sobre o funcionamento de seus aplicativos e o uso de recursos. Ao enviar logs de atividades do Azure AD para logs do Azure Monitor, você pode recuperar, monitorar e alertar rapidamente sobre os dados coletados. Use este método quando você não tiver uma solução SIEM existente para a minha vontade, mas que deseja consultas e análises. Uma vez que seus dados estão nos registros do Azure Monitor, você pode enviá-los para o centro de eventos e de lá para um SIEM, se quiser.

Saiba como [enviar dados para os logs do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Você também pode instalar as visualizações pré-construídas para logs de atividade saqueados do Azure para monitorar cenários comuns envolvendo eventos de login e auditoria.

Saiba como [instalar e usar as exibições do Log Analytics para logs de atividades do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Faça logs de fluxo para o seu hub de eventos Azure

O roteamento de logs para um hub de eventos do Azure permite a integração com ferramentas SIEM de terceiros. Essa integração permite combinar dados de log de atividades do Azure AD com outros dados gerenciados pelo seu SIEM para fornecer em informações mais avançadas sobre seu ambiente. 

Saiba como [enviar seus logs para um hub de eventos](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planejar operações e segurança para relatórios e monitoramento de AD do Azure

As partes interessadas precisam acessar os registros ad do Azure para obter insights operacionais. Os usuários prováveis incluem membros da equipe de segurança, auditores internos ou externos e a equipe de operações de gerenciamento de identidade e acesso.

As funções do Azure AD permitem que você delegue a capacidade de configurar e visualizar relatórios AD do Azure com base na sua função. Identifique quem em sua organização precisa de permissão para ler relatórios Azure AD e qual função seria apropriada para eles. 

As seguintes funções podem ler relatórios azure AD:

* Administrador global

* Administrador de Segurança

* Leitor de segurança

* Leitor de relatórios

Saiba mais sobre [as funções administrativas do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Sempre aplique o conceito de privilégios mínimos para reduzir o risco de um comprometimento da conta.* Considere implementar o [Gerenciamento de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) para proteger ainda mais sua organização.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Implantar relatórios e monitoramento do Azure AD

Dependendo das decisões tomadas anteriormente usando a orientação de design acima, esta seção irá guiá-lo para a documentação sobre as diferentes opções de implantação.

### <a name="consume-and-archive-azure-ad-logs"></a>Consumir e arquivar logs AD do Azure

[Localizar relatórios de atividade no Portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Arquiva os logs do Azure AD em uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementar monitoramento e análise

[Enviar logs para o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analisar logs de atividade do Azure AD com os logs do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretar o esquema de logs de auditoria no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interprete o esquema de logs de logs no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Stream Azure AD faz logs em um hub de eventos do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrar logs do Azure AD com o Splunk usando o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrar logs do Azure AD ao SumoLogic usando o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Próximas etapas

Considere implementar o [Gerenciamento de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Considere implementar [o RBAC (Role-Based Access Control, controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
