---
title: Controle de Segurança Do Azure - Registro e Monitoramento
description: Registro e monitoramento de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545493"
---
# <a name="security-control-logging-and-monitoring"></a>Controle de Segurança: Registro e Monitoramento

O registro e o monitoramento de segurança se concentram em atividades relacionadas à habilitação, aquisição e armazenamento de registros de auditoria para serviços do Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft mantém fontes de tempo para os recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

Como configurar a sincronização de tempo para os recursos de computação do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.2 | 6.5, 6.6 | Cliente |

Ingerir logs via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, use o Log Analytics Workspace(s) para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como coletar logs e métricas da plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Como coletar registros internos de host do Azure Virtual Machine com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Como começar com o Azure Monitor e a integração siem de terceiros:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Cliente |

Habilite as configurações de diagnóstico nos recursos do Azure para acesso a registros de auditoria, segurança e diagnóstico. Os registros de atividades, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data, endereços de origem, endereços de destino e outros elementos úteis.

Como coletar logs e métricas da plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Entenda o registro e diferentes tipos de registro no Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.4 | 6.2, 6.3 | Cliente |

Se o recurso de computação é de propriedade da Microsoft, então a Microsoft é responsável por monitorá-lo. Se o recurso de computação é de sua organização, é sua responsabilidade monitorá-lo. Você pode usar o Azure Security Center para monitorar o sistema operacional. Os dados coletados pelo Security Center do sistema operacional incluem tipo e versão do SISTEMA OPERACIONAL, Logs de sistema operacional (Registros de Eventos do Windows), processos em execução, nome da máquina, endereços IP e login no usuário. O Log Analytics Agent também coleta arquivos de falha.

Como coletar registros internos de host do Azure Virtual Machine com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Entenda a coleta de dados do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

No Azure Monitor, defina o período de retenção do Espaço de Trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.6 | 6.7 | Cliente |

Analisar e monitorar registros para comportamento anômalo e revisar regularmente os resultados. Use o Espaço de Trabalho log analytics do Azure Monitor para revisar logs e executar consultas em dados de log.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. 

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Entenda o espaço de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6,8 | Cliente |

Use o Azure Security Center com o Log Analytics Workspace para monitorar e alertar sobre atividades anômalas encontradas em registros de segurança e eventos.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel.

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como gerenciar alertas no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Como alertar sobre os dados de log analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.8 | 8.6 | Cliente |

Habilite a coleta de eventos antimalware para máquinas virtuais e serviços de nuvem do Azure.

Como configurar o Microsoft Antimalware para Máquinas Virtuais:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Como configurar o Microsoft Antimalware for Cloud Services:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Entenda o Antimalware da Microsoft:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,9 | 8.7 | Cliente |

Implementar uma solução de terceiros para o registro de DNS.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 8.8 | Cliente |

Configure manualmente o registro do console e a Transcrição do PowerShell em uma base por nó.

## <a name="next-steps"></a>Próximas etapas

Veja o próximo controle de segurança: [Controle de identidade e acesso](security-control-identity-access-control.md)
