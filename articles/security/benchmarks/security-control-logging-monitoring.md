---
title: Controle de Segurança Do Azure - Registro e Monitoramento
description: Registro e monitoramento do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408334"
---
# <a name="security-control-logging-and-monitoring"></a>Controle de Segurança: Registro e Monitoramento

O registro e o monitoramento de segurança se concentram em atividades relacionadas à habilitação, aquisição e armazenamento de registros de auditoria para serviços do Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft mantém fontes de tempo para os recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

- [Como configurar a sincronização de tempo para os recursos de computação do Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Como configurar a sincronização de tempo para recursos de computação do Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.2 | 6.5, 6.6 | Cliente |

Ingerir logs via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, use o Log Analytics Workspace(s) para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como coletar registros e métricas da plataforma com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Como coletar registros internos do host da Azure Virtual Machine com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Como começar com o Azure Monitor e integração SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Cliente |

Habilite as configurações de diagnóstico nos recursos do Azure para acesso a registros de auditoria, segurança e diagnóstico. Os registros de atividades, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data, endereços de origem, endereços de destino e outros elementos úteis.

- [Como coletar registros e métricas da plataforma com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Entenda o registro e diferentes tipos de log no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.4 | 6.2, 6.3 | Cliente |

Se o recurso de computação é de propriedade da Microsoft, então a Microsoft é responsável por monitorá-lo. Se o recurso de computação é de sua organização, é sua responsabilidade monitorá-lo. Você pode usar o Azure Security Center para monitorar o sistema operacional. Os dados coletados pelo Security Center do sistema operacional incluem tipo e versão do SISTEMA OPERACIONAL, sistema operacional (Registros de Eventos do Windows), processos em execução, nome da máquina, endereços IP e logado no usuário. O Log Analytics Agent também coleta arquivos de falha.

- [Como coletar registros internos do host da Azure Virtual Machine com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Entenda a coleta de dados do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

No Azure Monitor, defina o período de retenção do Espaço de Trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

- [Alterar o período de retenção de dados no Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de contas do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.6 | 6.7 | Cliente |

Analisar e monitorar registros para comportamento anômalo e revisar regularmente os resultados. Use o Espaço de Trabalho log analytics do Azure Monitor para revisar logs e executar consultas em dados de log.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Entenda o espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Como realizar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilite alertas para atividades anômalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6,8 | Cliente |

Use o Azure Security Center com o Log Analytics Workspace para monitorar e alertar sobre atividades anômalas encontradas em registros de segurança e eventos.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel.

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como gerenciar alertas no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Como alertar sobre dados de log analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.8 | 8.6 | Cliente |

Habilite a coleta de eventos antimalware para máquinas virtuais e serviços de nuvem do Azure.

- [Como configurar o Microsoft Antimalware para Máquinas Virtuais](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Como configurar o Microsoft Antimalware for Cloud Services](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Entenda o Antimalware da Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,9 | 8.7 | Cliente |

Implemente uma solução de terceiros do Azure Marketplace para solução de registro de DNS de acordo com a necessidade de suas organizações.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,10 | 8.8 | Cliente |

Use o Microsoft Monitoring Agent em todas as máquinas virtuais do Azure Windows suportadas para registrar o evento de criação de processos e o campo CommandLine.   Para máquinas Virtuais Azure Linux suportadas, você pode configurar manualmente o registro do console em uma base por nó e usar o Syslog para armazenar os dados.  Além disso, use o espaço de trabalho Log Analytics do Azure Monitor para revisar logs e realizar consultas em dados registrados de máquinas Virtuais do Azure. 

- [Coleta de dados na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Como realizar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Fontes de dados do Syslog no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo Controle de Segurança: [Controle de Identidade e Acesso](security-control-identity-access-control.md)