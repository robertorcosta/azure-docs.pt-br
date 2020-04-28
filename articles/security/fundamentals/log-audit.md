---
title: Auditoria e log de segurança do Azure | Microsoft Docs
description: Saiba mais sobre os logs disponíveis no Azure e as informações de segurança que você pode obter.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750796"
---
# <a name="azure-security-logging-and-auditing"></a>Auditoria e log de segurança do Azure

O Azure fornece uma ampla gama de opções de registro de log e auditoria de segurança configuráveis para ajudá-lo a identificar lacunas em seus mecanismos e políticas de segurança. Este artigo discute a geração, a coleta e a análise de logs de segurança de serviços hospedados no Azure.

> [!Note]
> Determinadas recomendações neste artigo podem resultar em maior uso de recursos de computação, rede ou dados e aumentar os custos de licença ou assinatura.

## <a name="types-of-logs-in-azure"></a>Tipos de logs no Azure

Os aplicativos em nuvem são complexos com muitas partes móveis. Os dados de log podem fornecer informações sobre seus aplicativos e ajudá-lo a:

- Solucionar problemas antigos ou evitar possíveis
- Melhorar o desempenho ou a facilidade de manutenção do aplicativo
- Automatizar ações que, de outra forma, exigirão intervenção manual

Os logs do Azure são categorizados nos seguintes tipos:
* **Logs de controle/gerenciamento** fornecem informações sobre as operações CREATE, UPDATE e DELETE do Azure Resource Manager. Para obter mais informações, confira [Logs de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md).

* **Os logs do plano de dados** fornecem informações sobre eventos gerados como parte do uso de recursos do Azure. Exemplos desse tipo de log são os logs do aplicativo, de segurança e do sistema de eventos do Windows em uma VM (máquina virtual) e os [logs de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md) que são configurados por meio do Azure Monitor.

* Os **eventos processados** fornecem informações sobre os eventos/alertas analisados que foram processados em seu nome. Exemplos desse tipo são os [Alertas da Central de Segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md), nos quais a [Central de Segurança do Azure](../../security-center/security-center-intro.md) processou e analisou sua assinatura e fornece alertas de segurança concisos.

A tabela a seguir lista os tipos mais importantes de logs disponíveis no Azure:

| Categoria do log | Tipo de log | Uso | Integração |
| ------------ | -------- | ------ | ----------- |
|[Logs de atividade](../../azure-monitor/platform/platform-logs-overview.md)|Eventos de plano de controle nos recursos do Azure Resource Manager|  Fornecem informações sobre as operações executadas em recursos de sua assinatura.|    API REST, [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Logs de recursos do Azure](../../azure-monitor/platform/platform-logs-overview.md)|Dados frequentes sobre a operação de recursos do Azure Resource Manager na assinatura|   Fornecem informações sobre as operações que o recurso executou por conta própria.| Azure Monitor|
|[Relatórios de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Logs e relatórios | Relata atividades de conexão do usuário e informações de atividades do sistema sobre gerenciamento de usuários e grupos.|[API do Graph](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Máquinas virtuais e serviços de nuvem](../../azure-monitor/learn/quick-collect-azurevm.md)|Serviço de log de eventos do Windows e syslog do Linux|  Capturam dados do sistema e dados de logs nas máquinas virtuais e transferem os dados para uma conta de armazenamento de sua escolha.|   Windows (usando o armazenamento [WAD](../../monitoring-and-diagnostics/azure-diagnostics.md) [Diagnóstico do Azure para Windows]) e Linux no Azure Monitor|
|[Análise de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Log de armazenamento, fornece dados de métrica de uma conta de armazenamento|Fornece informações das solicitações de rastreamento, analisa tendências de uso e diagnostica problemas com a conta de armazenamento.|   API REST ou [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Logs de fluxo do NSG (grupo de segurança de rede)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formato JSON, mostra os fluxos de entrada e saída por regra|Exibe informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede.|[Observador de rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insights](../../azure-monitor/app/app-insights-overview.md)|Logs, exceções e diagnóstico personalizado|  Fornece um serviço APM (monitoramento de desempenho de aplicativos) para desenvolvedores da Web em várias plataformas.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Dados do processo/alertas de segurança](../../security-center/security-center-intro.md)|  Alertas da central de segurança do Azure, alertas de logs de Azure Monitor|    Fornece informações e alertas de segurança.|  APIs REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de log com sistemas SIEM locais
A [integração de alertas da central de segurança](../../security-center/security-center-export-data-to-siem.md) discute como sincronizar alertas da central de segurança, os eventos de segurança de máquina virtual coletados pelos logs de diagnóstico do Azure e os logs de auditoria do Azure com seus logs de Azure monitor ou solução Siem.

## <a name="next-steps"></a>Próximas etapas

- [Auditoria e log](management-monitoring-overview.md): proteja seus dados mantendo a visibilidade e respondendo rapidamente aos alertas de segurança em tempo hábil.

- [Registro de log de segurança e coleção de log de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): imponha essas configurações para garantir que suas instâncias do Azure estão coletando os logs de segurança e de auditoria corretos.

- [Definir as configurações de auditoria para uma coleção de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): se você for um administrador de uma coleção de sites, recupere o histórico de ações de usuários individuais e o histórico de ações realizadas durante um intervalo de datas específico.

- [Pesquisar o log de auditoria no Centro de Segurança e Conformidade do Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): use o Centro de Segurança e Conformidade do Office 365 para pesquisar o log de auditoria unificado e exibir as atividades de usuário e do administrador em sua organização do Office 365.
