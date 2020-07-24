---
title: Linha de base de segurança do Azure para Azure DevTest Labs
description: Linha de base de segurança do Azure para Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095784"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de base de segurança do Azure para Azure DevTest Labs

A linha de base de segurança do Azure para Azure DevTest Labs contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas
**Diretrizes:** A Microsoft mantém fontes de tempo para recursos do Azure. No entanto, você pode gerenciar as configurações de sincronização de horário para seus recursos de computação.

Consulte o artigo a seguir para saber mais sobre como configurar a sincronização de horário para recursos de computação do Azure: [sincronização de horário para VMs do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** O

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um Log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível do plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível do plano de gerenciamento para suas instâncias do DevTest Labs.

Para obter mais informações, consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/platform/diagnostic-settings.md).

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um Log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível do plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível do plano de gerenciamento para suas instâncias do DevTest Labs.

Para obter mais informações, consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/platform/diagnostic-settings.md).

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais
**Diretrizes:** Azure DevTest Labs máquinas virtuais são criadas e pertencentes ao cliente. Portanto, é responsabilidade da organização monitorá-la. Você pode usar a central de segurança do Azure para monitorar o sistema operacional de computação. Os dados coletados pela central de segurança do sistema operacional incluem o tipo e a versão do so, sistema operacional (logs de eventos do Windows), processos em execução, nome da máquina, endereços IP e usuário conectado. O agente de Log Analytics também coleta arquivos de despejo de memória.

Para obter mais informações, confira os seguintes artigos: 

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança
***Diretrizes:** No Azure Monitor, defina o período de retenção de log para Log Analytics espaços de trabalho associados às suas instâncias de Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

Para obter mais informações, consulte o seguinte artigo: [como definir parâmetros de retenção de log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho Log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para Azure DevTest Labs.

Para obter mais informações, confira os seguintes artigos:

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal
**Diretrizes:** Use o espaço de trabalho do Azure Log Analytics para monitoramento e alertas sobre atividades anormais em logs de segurança e eventos relacionados à sua Azure DevTest Labs.

Para obter mais informações, consulte o seguinte artigo: [como alertar sobre dados de log do log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware
**Diretrizes:** Não aplicável. Azure DevTest Labs não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS
**Diretrizes:** Não aplicável. Azure DevTest Labs não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando
**Diretrizes:** Azure DevTest Labs cria máquinas de computação do Azure que são de propriedade e gerenciadas pelo cliente. Use Microsoft Monitoring Agent em todas as máquinas virtuais Windows do Azure com suporte para registrar o evento de criação de processo e o campo CommandLine. Para máquinas virtuais Linux do Azure com suporte, você pode configurar manualmente o log do console por nó e usar o syslog para armazenar os dados. Além disso, use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas em dados registrados de máquinas virtuais do Azure.

- [Coleta de dados na Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Como executar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Fontes de dados do Syslog no Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo:

- [Alertas de segurança para ambientes no Azure DevTest Labs](environment-security-alerts.md)