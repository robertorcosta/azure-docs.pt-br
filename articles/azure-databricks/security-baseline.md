---
title: Linha de base de segurança do Azure para Azure Databricks
description: Linha de base de segurança do Azure para Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d417f412592d87dec751b9d98a26bf786c719e5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796844"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Linha de base de segurança do Azure para Azure Databricks

A linha de base de segurança do Azure para Azure Databricks contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral das linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Diretrizes**: implante Azure Databricks em sua própria VNet (rede virtual) do Azure. A implantação padrão do Azure Databricks é um serviço totalmente gerenciado no Azure: todos os recursos do plano de dados, incluindo uma VNet à qual todos os clusters serão associados, são implantados em um grupo de recursos bloqueado. No entanto, se você precisar de personalização de rede, poderá implantar Azure Databricks recursos do plano de dados em sua própria rede virtual (injeção de VNet), permitindo que você implemente configurações de rede personalizadas. Você pode aplicar seu próprio NSG (grupo de segurança de rede) com regras personalizadas para restrições de tráfego de egresso específicas.

Além disso, você pode configurar regras NSG para especificar restrições de tráfego de saída na sub-rede em que sua instância de Azure Databricks é implantada. O Firewall do Azure pode ser configurado para espaços de trabalho injetados na VNET.

* [Como implantar Azure Databricks em sua própria rede virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como gerenciar NSGs](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICS

**Diretrizes**: implante Azure Databricks em sua própria VNet (rede virtual) do Azure. Um NSG (grupo de segurança de rede) não é criado automaticamente. Você precisa criar um NSG de linha de base somente com as regras padrão do Azure. Quando você implanta um espaço de trabalho, as regras exigidas pelo databricks são adicionadas. Você também pode começar com um NSG vazio e as regras apropriadas serão adicionadas automaticamente. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditorias de tráfego. Você também pode enviar logs de fluxo para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

* [Como implantar Azure Databricks em sua própria rede virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como habilitar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção contra DDoS do Azure Standard nas redes virtuais do Azure associadas às suas instâncias de Azure Databricks para proteção contra ataques de negação de serviço distribuídos. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP públicos mal-intencionados ou não usados conhecidos.

* [Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Entender a proteção contra ameaças para a camada de rede do Azure na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Diretrizes**: implante Azure Databricks em sua própria VNet (rede virtual) do Azure. Um NSG (grupo de segurança de rede) não é criado automaticamente. Você precisa criar um NSG de linha de base somente com as regras padrão do Azure. Quando você implanta um espaço de trabalho, as regras exigidas pelo databricks são adicionadas. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

* [Como implantar Azure Databricks em sua própria rede virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como habilitar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: implemente uma NVA (solução de virtualização de rede compatível com IDP/IPS) do Azure Marketplace para criar um espaço de trabalho integrado à rede virtual no qual todos os clusters de Azure Databricks têm um único endereço IP de saída. O endereço IP único pode ser usado como uma camada de segurança adicional com outros serviços e aplicativos do Azure que permitem o acesso com base em endereços IP específicos. Você pode usar um firewall do Azure ou outras ferramentas de terceiros, como NVA, para gerenciar o tráfego de entrada e saída.

Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for um requisito, o Firewall do Azure com inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência de ameaças do firewall do Azure pode alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

* [Como atribuir um único IP público para espaços de trabalho injetados por VNet usando o Firewall do Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Como criar um NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Orientação**: use marcas de serviço para definir os controles de acesso à rede em grupos de segurança de rede que são anexados às sub-redes às quais sua instância do Azure Databricks está associada. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados. Não há suporte para marcas de serviço com espaços de trabalho de VNET não injetados.

* [Entender as marcas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança de rede para suas instâncias de Azure Databricks com Azure Policy. Você pode usar Azure Policy aliases no namespace "Microsoft. databricks" para definir definições de política personalizadas. As políticas não serão aplicadas aos recursos dentro do grupo de recursos gerenciado.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de gerenciamento de recursos do Azure, RBAC (controle de acesso baseado em função) e políticas, em uma única definição de Blueprint. Aplique facilmente o plano gráfico a novas assinaturas e ambientes, além de ajustar o controle e o gerenciamento por meio da versão.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender Azure Policy aliases e a estrutura de definição](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Como criar um Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados à sua instância de Azure Databricks. Para regras NSG individuais, use o campo "Descrição" para especificar a necessidade de negócios e/ou duração (etc.) para qualquer regra que permita o tráfego de/para uma rede.

* [Como criar e usar marcas](httphttps://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias de Azure Databricks. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação. Como Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster Azure Databricks e não pode definir configurações de sincronização de tempo.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure Databricks. Em Azure Monitor, você pode consultar o espaço de trabalho Log Analytics que está configurado para receber seus databricks e logs de diagnóstico. Use contas de armazenamento do Azure para armazenamento de log de longo prazo/arquivamento ou hubs de eventos para exportar dados para outros sistemas. Como alternativa, você pode habilitar o e os dados integrados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium

* [Como definir configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como começar a usar Azure Monitor e integração de SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Orientação**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para os recursos do Azure.

Para o log de auditoria, o Azure Databricks fornece logs de diagnóstico de ponta a ponta abrangentes de atividades executadas por Azure Databricks usuários, permitindo que sua empresa monitore Azure Databricks padrões de uso detalhados.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium

* [Como habilitar configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy))

* [Como habilitar as configurações de diagnóstico para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: o Azure Databricks fornece logs de diagnóstico de ponta a ponta abrangentes de atividades executadas por Azure Databricks usuários, permitindo que sua empresa monitore Azure Databricks padrões de uso detalhados.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium. O log de segurança do so não está disponível.

* [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como habilitar as configurações de diagnóstico para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Orientação**: Habilitar configurações de diagnóstico para Azure Databricks. Se optar por armazenar os logs em um espaço de trabalho Log Analytics, defina seu período de retenção de espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. As atividades relacionadas à segurança são controladas em logs de auditoria do databricks.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium

* [Como habilitar as configurações de diagnóstico no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilite as configurações de diagnóstico para Azure Databricks e envie logs para um espaço de trabalho log Analytics. Use o espaço de trabalho Log Analytics para analisar e monitorar seus logs de Azure Databricks para comportamento anormal e examinar regularmente os resultados.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium

* [Como habilitar as configurações de diagnóstico no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como consultar logs de Azure Databricks enviados para Log Analytics espaço de trabalho](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Diretrizes**: defina as configurações de diagnóstico para sua instância do Azure Databricks e envie logs para log Analytics espaço de trabalho. No espaço de trabalho Log Analytics, configure alertas para ocorrer quando um conjunto predefinido de condições ocorrer.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

Observação: Azure Databricks logs de diagnóstico exigem o plano de Azure Databricks Premium

* [Como enviar logs de Azure Databricks para log Analytics espaço de trabalho](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Como configurar alertas no espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável; Azure Databricks não processa nem produz logs relacionados ao DNS acessíveis pelo usuário.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Orientação**: você pode usar Azure DATABRICKS APIs scim para gerenciar usuários em um espaço de trabalho Azure Databricks e conceder privilégios administrativos a usuários designados. Você também pode gerenciá-los por meio da interface do usuário do Azure Databricks.

* [Como usar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover usuários no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: o Azure Databricks usa o Azure Active Directory (AD) para fornecer acesso ao portal do Azure, bem como ao console do administrador do Azure Databricks. No entanto, o Azure AD não tem o conceito de senhas padrão, você é responsável por alterar ou não permitir senhas padrão para qualquer aplicativo personalizado ou de terceiros.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Orientação**: você pode usar Azure DATABRICKS APIs scim para adicionar usuários com privilégios de administrador em um Azure Databricks. Você também pode gerenciá-los por meio da interface do usuário do Azure Databricks.

* [Como usar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover usuários no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Diretrizes**: Azure Databricks é automaticamente configurada para usar Azure Active Directory logon único para autenticar usuários. Os usuários fora da sua organização devem concluir o processo de convite e ser adicionados ao seu locatário de Active Directory antes de poderem fazer logon no Azure Databricks por meio de logon único. Você pode implementar o SCIM para automatizar o provisionamento e desprovisionamento de usuários de espaços de trabalho.

* [Como usar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Entender o logon único para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar usuários para seu locatário do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Use a autenticação multifator para acesso baseado em Azure Active Directory.

**Diretrizes**: habilite o Azure ad MFA e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorar a identidade e o acesso na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar os recursos do Azure.

* [Saiba mais sobre estações de trabalho com acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use a central de segurança do Azure para monitorar a atividade de identidade e acesso. Além disso, você pode aproveitar os logs de diagnóstico Azure Databricks.

* [Como identificar usuários do Azure AD sinalizados para atividades arriscadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar locais nomeados no Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Azure Databricks é automaticamente configurada para usar Azure Active Directory logon único para autenticar usuários. Os usuários fora da sua organização devem concluir o processo de convite e ser adicionados ao seu locatário de Active Directory antes de poderem fazer logon no Azure Databricks por meio de logon único.

* [Entender o logon único para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar usuários para seu locatário do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure ad fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. Você também pode implementar APIs SCIM e Azure Databricks logs de diagnóstico para examinar o acesso do usuário. Você também pode usar APIs do SCIM e Azure Databricks logs de diagnóstico para examinar o acesso do usuário.

Além disso, examine regularmente e gerencie o acesso do usuário no console do administrador do Azure Databricks.

* [Relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Como usar as revisões de acesso de identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Como gerenciar o acesso do usuário no console do administrador do Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com qualquer ferramenta Siem/monitoramento. Além disso, você pode usar Azure Databricks logs de diagnóstico para examinar a atividade de acesso do usuário.

Você pode simplificar esse processo criando configurações de diagnóstico para Azure Active Directory contas de usuário e enviando os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics.

* [Como usar APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure ad para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. Além disso, você pode usar Azure Databricks logs de diagnóstico para examinar a atividade de acesso do usuário.

* [Como exibir entradas arriscadas do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e habilitar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: quando os tíquetes de suporte são abertos, os engenheiros de atendimento ao cliente e de suporte solicitarão o consentimento para acessar os dados relevantes do cliente.

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: atualmente não disponível

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de Azure Databricks instâncias que processam informações confidenciais.

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. A implantação padrão do Azure Databricks é um serviço totalmente gerenciado que é implantado em sua própria rede virtual. Se precisar de personalização de rede, você poderá implantar Azure Databricks em sua própria rede virtual. É uma prática recomendada criar espaços de trabalho Azure Databricks separados para diferentes equipes de negócios ou departamentos.

* [Como implantar Azure Databricks em sua própria rede virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais.

**Diretrizes**: siga a arquitetura de proteção vazamento do databricks para reduzir a possibilidade de vazamento de dados.

* [Proteção de vazamento de dados com Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

A Microsoft gerencia a infraestrutura subjacente para Azure Databricks e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: atualmente não disponível

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Orientação**: a Microsoft negociará o TLS 1,2 por padrão ao administrar sua instância de Azure Databricks por meio do portal do Azure ou Azure Databricks console. O aplicativo Web do databricks dá suporte a TLS 1,3.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: atualmente não disponível; os recursos de identificação de dados, classificação e prevenção de perda não estão disponíveis no momento para Azure Databricks. Marque Azure Databricks instâncias e recursos relacionados que possam estar processando informações confidenciais como tal e implemente uma solução de terceiros, se necessário, para fins de conformidade.

A plataforma databricks é somente de computação e todos os dados são armazenados em outros serviços de dados do Azure. Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: atualmente não disponível

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Orientação**: no Azure Databricks, você pode usar ACLs (listas de controle de acesso) para configurar a permissão para acessar tabelas de dados, clusters, pools, trabalhos e objetos de espaço de trabalho, como notebooks, experimentos e pastas. Todos os usuários administradores podem gerenciar listas de controle de acesso, assim como os usuários que receberam permissões delegadas para gerenciar listas de controle de acesso. Você pode usar o RBAC do Azure para definir permissões no espaço de trabalho Azure Databricks.

Observação: o controle de acesso de tabela, cluster, pool, trabalho e espaço de trabalho está disponível somente no plano de Azure Databricks Premium

* [Como gerenciar o controle de acesso no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para Azure Databricks e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Orientação**: um espaço de trabalho Azure Databricks consiste em um plano de gerenciamento hospedado em uma rede virtual gerenciada por Azure Databricks e um plano de dados que é implantado em uma rede virtual gerenciada pelo cliente. O plano de controle armazena os blocos de anotações de todos os usuários e os resultados do bloco de anotações associados em um banco de dados. Por padrão, todos os blocos de anotações e os resultados são criptografados em repouso com uma chave de criptografia diferente.

O DBFS (sistema de arquivos do databricks) é um sistema de arquivos distribuído montado em um espaço de trabalho Azure Databricks e disponível em clusters Azure Databricks. O DBFS é implementado como uma conta de armazenamento no grupo de recursos gerenciado do seu espaço de trabalho Azure Databricks. Por padrão, a conta de armazenamento é criptografada com chaves gerenciadas pela Microsoft. Seus dados são armazenados nos serviços de dados do Azure que você possui e você tem a opção de criptografá-los. O uso de DBFS para armazenar dados de produção não é recomendado

Observação: esses recursos não estão disponíveis para todas as assinaturas de Azure Databricks. Entre em contato com seu representante de conta do Microsoft ou databricks para solicitar acesso.

* [Como habilitar chaves gerenciadas pelo cliente para blocos de anotações de Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Como habilitar chaves gerenciadas pelo cliente para o sistema de arquivos Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em espaços de trabalho críticos Azure Databricks.

* [Como criar alertas para eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Diretrizes**: implemente uma solução de gerenciamento de vulnerabilidades de terceiros.

Se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar Azure Databricks scripts de inicialização para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster Azure Databricks e gerenciar os nós por meio do portal respectivo. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Diretrizes**: a Microsoft mantém as imagens de base do nó de cluster Azure Databricks, no entanto, você é responsável por garantir que seus nós de cluster permaneçam corrigidos. Para adicionar uma atualização de manutenção a um cluster em execução existente, você deve reiniciar o cluster.

* [Entender as atualizações de manutenção de tempo de execução para Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Diretrizes**: a Microsoft mantém as Azure Databricks imagens base do nó de cluster, no entanto, você é responsável por garantir que todos os aplicativos de terceiros instalados permaneçam corrigidos.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Diretrizes**: implemente uma solução de gerenciamento de vulnerabilidades de terceiros que tenha a capacidade de comparar verificações de vulnerabilidade ao longo do tempo. Se você tiver uma assinatura de gerenciamento de vulnerabilidades, poderá usar o portal desse fornecedor para exibir e comparar as verificações de vulnerabilidade de back-to-back. Observe que cada solução de terceiros funciona de maneira diferente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Use um processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas.

**Diretrizes**: Use um programa de Pontuação de risco comum (por exemplo, o sistema de Pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas por sua ferramenta de verificação de terceiros.

**Monitoramento da central de segurança do Azure**: N/A

**Responsabilidade**: cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas. Verifique se as permissões apropriadas (leitura) existem no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

* [Como criar consultas com o grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Entendendo o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar os recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter o inventário de recursos do Azure aprovados e títulos de software.

**Diretrizes**: defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Orientação**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com o grafo do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: não aplicável; Azure Databricks é um serviço PaaS, os clientes não têm acesso direto às VMs em um cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.), incluindo instâncias de Azure Databricks, dentro de suas assinaturas. Remova os recursos do Azure não aprovados que você descobrir. Para Azure Databricks nós de cluster, implemente uma solução de terceiros para remover ou alertar sobre software não aprovado.

* [Como criar consultas com o grafo do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo de recurso específico com Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Limitar a capacidade dos usuários de interagir com Azure Resource Manager por meio de scripts</div>

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: não aplicável; Isso não se aplica a Azure Databricks, pois os usuários (não administradores) do cluster não precisam acessar os nós individuais para executar trabalhos. Por padrão, o administrador de cluster tem acesso de raiz a todos os nós de cluster.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável; o parâmetro de comparação é destinado ao serviço de aplicativos do Azure ou aos recursos de computação que hospedam aplicativos Web.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Azure Databricks com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Databricks. Observe que qualquer política aplicada não funciona no grupo de recursos gerenciado do databricks.

* [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Azure Databricks com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Databricks. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Diretrizes**: Azure Databricks imagens do sistema operacional gerenciadas e mantidas pela Microsoft. Você é responsável pela implementação da configuração de estado no nível do sistema operacional.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições personalizadas de política do Azure, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: se você estiver usando imagens personalizadas para seus Azure Databricks nós de cluster, envie por push sua imagem base personalizada para um registro do Docker, como o ACR (registro de contêiner do Azure).

* [Como personalizar contêineres com os serviços de contêiner do databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Entender o registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Azure Databricks com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Databricks.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Azure Databricks com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Databricks.

* [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para monitorar o estado de seus sistemas operacionais de nó de cluster Azure Databricks.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use Azure Key Vault com um escopo de segredo Azure Databricks para gerenciar e usar segredos com segurança.

* [Como usar Azure Key Vault com Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Diretrizes**: atualmente não disponível; As identidades gerenciadas não estão disponíveis no momento para Azure Databricks

* [Serviços que dão suporte a identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: atualmente não disponível

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

**Diretrizes**: implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte [controle de segurança: defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar Azure Databricks scripts de inicialização para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster Azure Databricks e gerenciar os nós por meio do portal respectivo. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure app Service), no entanto, ele não é executado no seu conteúdo.

Examine previamente todos os arquivos que estão sendo carregados em seus Azure Databricks nós de cluster ou recursos relacionados.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar Azure Databricks scripts de inicialização para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster Azure Databricks e gerenciar os nós por meio do portal respectivo. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: para suas Azure Databricks fontes de dados, verifique se você configurou um nível apropriado de redundância de dados para seu caso de uso. Por exemplo, se estiver usando uma conta de armazenamento do Azure para seu Azure Databricks armazenamento de dados, escolha a opção de redundância apropriada (LRS, ZRS, GRS, RA-GRS).

* [Fontes de dados para Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Recuperação de desastre regional para clusters do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: faça backup de qualquer chave gerenciada pelo cliente relacionada às suas implementações de Azure Databricks no Azure Key Vault. Você também pode usar a API REST e a CLI para criar um backup diário de configurações do databricks. Você também pode usar a API/CLI REST para criar um backup diário de configurações do databricks.

* [Como fazer backup de chaves do Key Vault no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: teste a restauração de backup de chaves gerenciadas do cliente relacionadas às suas implementações de Azure Databricks.

* [Como restaurar chaves do Key Vault no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Verifique se a exclusão reversível está habilitada no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

* [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Como configurar Automaçãos de fluxo de trabalho na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Você também pode aproveitar o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

* [Consulte a publicação do NIST: guia para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados do cliente foram acessados por uma parte ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato da segurança da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas sentinela.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

* [Como configurar a automação de fluxo de trabalho e os aplicativos lógicos](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [controle de segurança: testes de penetração e exercícios de equipe vermelhos](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas dentro de 60 dias.

**Orientação**: * [siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
