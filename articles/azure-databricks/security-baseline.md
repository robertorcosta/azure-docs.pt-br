---
title: Linha de base de segurança do Azure para o Azure Databricks
description: Linha de base de segurança do Azure para o Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681702"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Linha de base de segurança do Azure para o Azure Databricks

A linha de base de segurança do Azure para o Azure Databricks contém recomendações que ajudarão você a aprimorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações de como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, confira a [Visão geral sobre linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretrizes**: Implante o Azure Databricks em sua rede virtual (VNet) do Azure. A implantação padrão do Azure Databricks é um serviço totalmente gerenciado no Azure: todos os recursos do plano de dados, incluindo uma VNet à qual todos os clusters serão associados, são implantados em um grupo de recursos bloqueado. No entanto, se você precisar de personalização de rede, poderá implantar os recursos do plano de dados do Azure Databricks em sua rede virtual (injeção de VNet), permitindo a implementação de configurações de rede personalizadas. Você pode aplicar seu NSG (grupo de segurança de rede) com regras personalizadas para restrições específicas de tráfego de saída.

Além disso, você pode configurar regras de NSG para especificar restrições de tráfego de saída na sub-rede em que a instância do Azure Databricks está implantada. O Firewall do Azure pode ser configurado para workspaces injetados de VNET.

* [Como implantar o Azure Databricks em sua Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como gerenciar NSGs](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Diretrizes**: Implante o Azure Databricks em sua rede virtual (VNet) do Azure. Um NSG (grupo de segurança de rede) não é criado automaticamente. Você precisa criar um NSG de linha de base somente com regras padrão do Azure. Quando você implanta um workspace, as regras exigidas pelo Databricks são adicionadas. Você também pode começar com um NSG vazio e as regras apropriadas serão adicionadas automaticamente. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditorias de tráfego. Você também pode enviar logs de fluxo para um workspace do Log Analytics e usar a Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações de rede incorretas.

* [Como implantar o Azure Databricks em sua Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como habilitar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos Web críticos

**Diretrizes**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: Habilite a Proteção contra DDoS do Azure Standard nas Redes Virtuais do Azure associadas às suas instâncias do Azure Databricks para proteção contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP públicos conhecidos mal-intencionados ou não usados.

* [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Entender a proteção contra ameaças para a camada de rede do Azure na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrar pacotes de rede e logs de fluxo

**Diretrizes**: Implante o Azure Databricks em sua rede virtual (VNet) do Azure. Um NSG (grupo de segurança de rede) não é criado automaticamente. Você precisa criar um NSG de linha de base somente com regras padrão do Azure. Quando você implanta um workspace, as regras exigidas pelo Databricks são adicionadas. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo para um workspace do Log Analytics e usar a Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações de rede incorretas.

* [Como implantar o Azure Databricks em sua Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como habilitar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: Implemente uma NVA (solução de virtualização de rede) compatível com IDP/IPS do Azure Marketplace para criar um workspace integrado à rede virtual no qual todos os clusters do Azure Databricks têm um só endereço IP de saída. O endereço IP único pode ser usado como uma camada de segurança adicional com outros serviços e aplicativos do Azure que permitem acesso com base em endereços IP específicos. Você pode usar um Firewall do Azure ou outras ferramentas de terceiros, como NVA, para gerenciar o tráfego de entrada e saída.

Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

* [Como atribuir um único IP público para espaços de trabalho injetados por VNet usando o Firewall do Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Como criar uma NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos Web

**Diretrizes**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: Use Marcas de Serviço para definir controles de acesso à rede em Grupos de segurança de rede que são anexados às Sub-redes às quais sua instância do Azure Databricks está associada. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. As marcas de serviço não são compatíveis com workspaces de VNET não injetados.

* [Entender as marcas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: Defina e implemente configurações de segurança de rede para suas instâncias do Azure Databricks com o Azure Policy. Você pode usar aliases do Azure Policy no namespace "Microsoft.Databricks" para definir definições de política personalizadas. As políticas não serão aplicadas aos recursos dentro do grupo de recursos gerenciado.

Você também pode usar o Azure Blueprints para simplificar implantações do Azure de grande escala por meio do empacotamento de artefatos de ambiente importantes, como modelos do Azure Resource Management, RBAC (controle de acesso baseado em função) e políticas em uma definição de blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender os aliases e a estrutura de definição do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Como criar um Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: Use Marcas para NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados à sua instância do Azure Databricks. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

* [Como criar e usar Marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o Log de Atividades do Azure para monitorar configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias do Azure Databricks. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do Log de Atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de data/hora aprovadas

**Diretrizes**: No entanto, a Microsoft mantém fontes de data/hora para recursos do Azure. Você tem a opção de gerenciar as configurações de sincronização de data/hora para seus recursos de computação. Como o Azure Databricks é um serviço PaaS, você não tem acesso direto às VMs em um cluster do Azure Databricks e não pode definir configurações de sincronização de data/hora.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: Ingerir logs por meio do Azure Monitor para agregar dados de segurança gerados pelo Azure Databricks. No Azure Monitor, você pode consultar o workspace do Log Analytics que está configurado para receber o Databricks e os logs de diagnóstico. Use Contas do Armazenamento do Azure para armazenamento de log de longo prazo/arquivamento ou Hubs de Eventos para exportar dados para outros sistemas. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium

* [Como definir as configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como integrar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o log de auditoria para recursos do Azure

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Para o log de auditoria, o Azure Databricks oferece logs de diagnóstico de ponta a ponta abrangentes sobre as atividades executadas por usuários do Azure Databricks, permitindo que sua empresa monitore padrões de uso detalhados do Azure Databricks.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium

* [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como habilitar as configurações de diagnóstico para o Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Coletar logs de segurança de sistemas operacionais

**Diretrizes**: O Azure Databricks oferece logs de diagnóstico de ponta a ponta abrangentes sobre as atividades executadas por usuários do Azure Databricks, permitindo que sua empresa monitore padrões de uso detalhados do Azure Databricks.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium. O registro em log de segurança de SO não está disponível.

* [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como habilitar as configurações de diagnóstico para o Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico para o Azure Databricks. Se optar por armazenar logs em um workspace do Log Analytics, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas do Armazenamento do Azure para armazenamento de longo prazo/arquivamento. As atividades relacionadas à segurança são rastreadas em logs de auditoria do Databricks.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium

* [Como habilitar as configurações de diagnóstico no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como definir parâmetros de retenção de log para workspaces do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar logs

**Diretrizes**: Habilite as configurações de diagnóstico para o Azure Databricks e envie logs para um workspace do Log Analytics. Use o workspace do Log Analytics para analisar e monitorar os logs do Azure Databricks em relação a comportamento anormal e examine regularmente os resultados.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium

* [Como habilitar as configurações de diagnóstico no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como consultar logs do Azure Databricks enviados para workspace do Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Como integrar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilitar alertas para atividade anômala

**Diretrizes**: Defina configurações de diagnóstico para sua instância do Azure Databricks e envie logs para o workspace do Log Analytics. No workspace do Log Analytics, configure alertas para quando um conjunto predefinido de condições ocorrer.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Observação: Os logs de diagnóstico do Azure Databricks exigem o plano Azure Databricks Premium

* [Como enviar logs do Azure Databricks para workspace do Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Como configurar alertas no workspace do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar o registro em log de antimalware

**Diretrizes**: Não aplicável.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consulta DNS

**Diretrizes**: Não aplicável; o Azure Databricks não processa nem produz logs relacionados a DNS acessível por usuários.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o log de auditoria de linha de comando

**Diretrizes**: Não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: Você pode usar APIs de SCIM do Azure Databricks para gerenciar usuários em um workspace do Azure Databricks e conceder privilégios administrativos a usuários designados. Você também pode gerenciá-los por meio da interface do usuário do Azure Databricks.

* [Como usar as APIs de SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover usuários no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Diretrizes**: O Azure Databricks usa Azure AD (Active Directory) para fornecer acesso ao portal do Azure, bem como ao console do administrador do Azure Databricks. No entanto, o Azure AD não tem o conceito de senhas padrão; você é responsável por alterar ou não permitir senhas padrão para qualquer aplicativo personalizado ou de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: Você pode usar APIs de SCIM do Azure Databricks para adicionar usuários com privilégios de administrador em um Azure Databricks. Você também pode gerenciá-los por meio da interface do usuário do Azure Databricks.

* [Como usar as APIs de SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover usuários no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usar SSO (logon único) com o Azure Active Directory

**Diretrizes**: O Azure Databricks é configurado automaticamente para usar o logon único do Azure Active Directory para autenticar usuários. Os usuários de fora da sua organização devem concluir o processo de convite e serem adicionados ao seu locatário do Active Directory antes de poderem fazer logon no Azure Databricks por meio do logon único. Você pode implementar o SCIM para automatizar o provisionamento e desprovisionamento de usuários em workspaces.

* [Como usar as APIs de SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Entender o logon único para o Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar usuários para seu locatário do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory.

**Diretrizes**: Habilite a MFA do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar recursos do Azure.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso. Além disso, você pode aproveitar os logs de diagnóstico do Azure Databricks.

* [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

* [Como configurar localizações nomeadas no Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: O Azure Databricks é configurado automaticamente para usar o logon único do Azure Active Directory para autenticar usuários. Os usuários de fora da sua organização devem concluir o processo de convite e serem adicionados ao seu locatário do Active Directory antes de poderem fazer logon no Azure Databricks por meio do logon único.

* [Entender o logon único para o Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar usuários para seu locatário do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar associações de grupo, acesso aos aplicativos empresariais e atribuições de função com eficiência. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo. Você também pode implementar APIs de SCIM e logs de diagnóstico do Azure Databricks para examinar acesso de usuários. Você também pode usar APIs de SCIM e logs de diagnóstico do Azure Databricks para examinar acesso de usuários.

Além disso, examine regularmente e gerencie o acesso de usuários no console do administrador do Azure Databricks.

* [Relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Como usar as revisões de acesso de identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Como gerenciar o acesso de usuários no console do administrador do Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorar tentativas de acessar contas desativadas

**Diretrizes**: Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração a qualquer ferramenta de Monitoramento/SIEM. Além disso, você pode usar os logs de diagnóstico do Azure Databricks para examinar a atividade de acesso de usuários.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory e enviando os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics.

* [Como usar APIs de SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alertar sobre o desvio de comportamento de logon em conta

**Diretrizes**: Use os recursos de proteção de identidade e proteção contra riscos do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. Além disso, você pode usar os logs de diagnóstico do Azure Databricks para examinar a atividade de acesso de usuários.

* [Como exibir entradas suspeitas do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e habilitar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como integrar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: Quando os tíquetes de suporte são abertos, os engenheiros Serviço e Suporte ao Cliente solicitarão o consentimento para acessar os dados relevantes do cliente.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: Use marcas para auxiliar no acompanhamento de instâncias do Azure Databricks que processam informações confidenciais.

* [Como criar e usar Marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. A implantação padrão do Azure Databricks é um serviço totalmente gerenciado implantado em sua rede virtual. Se precisar de personalização de rede, você poderá implantar o Azure Databricks em sua rede virtual. A melhor prática é criar workspaces do Azure Databricks separados para diferentes equipes ou departamentos da empresa.

* [Como implantar o Azure Databricks em sua Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitore e bloqueie a transferência não autorizada de informações confidenciais.

**Diretrizes**: Siga a arquitetura de proteção contra exfiltração do Databricks para atenuar a possibilidade de exfiltração dos dados.

* [Proteção contra exfiltração dos dados com o Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

A Microsoft gerencia a infraestrutura subjacente para o Azure Databricks e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: A Microsoft negociará o TLS 1.2 por padrão ao administrar sua instância do Azure Databricks por meio do portal do Azure ou do console do Azure Databricks. O aplicativo Web do Databricks é compatível com o TLS 1.3.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: Não disponível no momento; os recursos de identificação de dados, classificação e prevenção contra perda não estão disponíveis no momento para o Azure Databricks. Marque instâncias do Azure Databricks e recursos relacionados que possam processar informações confidenciais como tal e implemente uma solução de terceiros, se for necessário para fins de conformidade.

A plataforma Databricks é somente de computação e todos os dados são armazenados em outros Serviços de Dados do Azure. Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: No Azure Databricks, você pode usar ACLs (listas de controle de acesso) para configurar a permissão para acessar tabelas de dados, clusters, pools, trabalhos e objetos de espaço de trabalho, como notebooks, experimentos e pastas. Todos os usuários administradores podem gerenciar listas de controle de acesso, assim como os usuários que receberam permissões delegadas para gerenciar listas de controle de acesso. Você pode usar o RBAC do Azure para definir permissões no workspace do Azure Databricks.

Observação: A tabela, o cluster, o pool, o trabalho e o controle de acesso do workspace estão disponíveis somente no Plano Azure Databricks Premium

* [Como gerenciar o controle de acesso no Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: Não aplicável; esta recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o Azure Databricks e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: Um workspace do Azure Databricks consiste em um plano de gerenciamento hospedado em uma rede virtual gerenciada do Azure Databricks e um plano de dados que é implantado em uma rede virtual gerenciada pelo cliente. O plano de controle armazena os notebooks de todos os usuários e os resultados dos notebooks associados em um banco de dados. Por padrão, todos os notebooks e os resultados são criptografados em repouso com uma chave de criptografia diferente.

O DBFS (sistema de arquivos do Databricks) é um sistema de arquivos distribuído montado em um workspace do Azure Databricks e disponível em clusters do Azure Databricks. O DBFS é implementado como uma conta de armazenamento no grupo de recursos gerenciados do seu workspace do Azure Databricks. Por padrão, a conta de armazenamento é criptografada com chaves gerenciadas pela Microsoft. Os dados são armazenados nos seus Serviços de Dados do Azure e você tem a opção de criptografá-los. O uso do DBFS para armazenar dados de produção não é recomendado

Observação: Esses recursos não estão disponíveis para todas as assinaturas do Azure Databricks. Entre em contato com seu representante de conta da Microsoft ou do Databricks para solicitar acesso.

* [Como habilitar chaves gerenciadas pelo cliente para notebooks do Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Como habilitar chaves gerenciadas pelo cliente para o sistema de arquivos do Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use o Azure Monitor com o log de atividades do Azure para criar alertas para quando alterações ocorrerem em workspaces críticos do Azure Databricks.

* [Como criar alertas para eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: Implemente uma solução de gerenciamento de vulnerabilidades de terceiros.

Se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, use os scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster do Azure Databricks e gerenciar os nós por meio do respectivo portal. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: A Microsoft mantém as imagens de base do nó de cluster do Azure Databricks, no entanto, você é responsável por garantir que os nós de cluster permaneçam corrigidos. Para adicionar uma atualização de manutenção a um cluster em execução existente, você deve reiniciar o cluster.

* [Entender as atualizações de manutenção de runtime para o Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patch de software de terceiros

**Diretrizes**: A Microsoft mantém as imagens de base do nó de cluster do Azure Databricks, no entanto, você é responsável por garantir que os aplicativos de terceiros que você instalar permaneçam corrigidos.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: Implemente uma solução de gerenciamento de vulnerabilidades de terceiros que tenha a capacidade de comparar verificações de vulnerabilidade ao longo do tempo. Se você tiver uma assinatura de gerenciamento de vulnerabilidades, poderá usar o portal desse fornecedor para ver e comparar as verificações de vulnerabilidade consecutivas. Observe que cada solução de terceiros funciona de maneira diferente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas.

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Monitoramento da Central de Segurança do Azure**: N/D

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usar a descoberta de ativos do Azure

**Diretrizes**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Verifique se as permissões apropriadas (leitura) existem no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

* [Como criar consultas com o Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Noções básicas obre o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar Marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter inventário de recursos e títulos de software aprovados do Azure.

**Diretrizes**: Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: Não aplicável; o Azure Databricks é um serviço PaaS. Os clientes não têm acesso direto às VMs em um cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.), incluindo instâncias do Azure Databricks em suas assinaturas. Remova os recursos do Azure não aprovados que você descobrir. Para nós de cluster do Azure Databricks, implemente uma solução de terceiros para remover ou alertar sobre software não aprovado.

* [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: Não aplicável; o Azure Databricks é um serviço PaaS. Você não tem acesso direto às VMs em um cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovados

**Diretrizes**: Não aplicável; o Azure Databricks é um serviço PaaS. Você não tem acesso direto às VMs em um cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com o Azure Resource Manager por meio de scripts</div>

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretrizes**: Não aplicável; isso não se aplica a Azure Databricks, pois os usuários (não administradores) do cluster não precisam acessar os nós individuais para executar trabalhos. Por padrão, o administrador do cluster tem acesso de raiz a todos os nós de cluster.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar fisicamente ou logicamente os aplicativos de alto risco

**Diretrizes**: Não aplicável; o parâmetro de comparação é destinado ao Serviço de Aplicativo do Azure ou aos recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: Defina e implemente configurações de segurança padrão para suas instâncias do Azure Databricks com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do Azure Databricks. Observe que qualquer política aplicada não funciona no grupo de recursos gerenciados do Databricks.

* [Como exibir os aliases disponíveis do Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras de sistema operacional

**Diretrizes**: Não aplicável; o Azure Databricks é um serviço PaaS. Você não tem acesso direto às VMs em um cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: Defina e implemente configurações de segurança padrão para suas instâncias do Azure Databricks com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do Azure Databricks. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender os efeitos do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras de sistema operacional

**Diretrizes**: As imagens do sistema operacional do Azure Databricks são gerenciadas e mantidas pela Microsoft. Você é responsável pela implementação da configuração de estado no nível do sistema operacional.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Se você estiver usando definições personalizadas do Azure Policy, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazene imagens personalizadas do sistema operacional com segurança

**Diretrizes**: Se você estiver usando imagens personalizadas para seus nós de cluster do Azure Databricks, envie por push sua imagem de base personalizada para um registro do Docker, como o ACR (Registro de Contêiner do Azure).

* [Como personalizar contêineres com os Serviços de Contêiner do Databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Entender o Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: Defina e implemente configurações de segurança padrão para suas instâncias do Azure Databricks com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do Azure Databricks.

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como exibir os aliases disponíveis do Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretrizes**: Não aplicável; o Azure Databricks é um serviço PaaS. Você não tem acesso direto às VMs em um cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar o monitoramento automatizado de configuração para serviços do Azure

**Diretrizes**: Defina e implemente configurações de segurança padrão para suas instâncias do Azure Databricks com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do Azure Databricks.

* [Como exibir os aliases disponíveis do Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: Implemente uma solução de terceiros para monitorar o estado de seus sistemas operacionais de nó de cluster do Azure Databricks.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use o Azure Key Vault com um escopo de segredo do Azure Databricks para gerenciar e usar segredos com segurança.

* [Como usar o Azure Key Vault com o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: Não disponível no momento; as identidades gerenciadas não estão disponíveis no momento para o Azure Databricks

* [Serviços compatíveis com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição involuntária de credenciais

**Diretrizes**: Implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usar software antimalware gerenciado centralmente

**Diretrizes**: Se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, use os scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster do Azure Databricks e gerenciar os nós por meio do respectivo portal. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, o Serviço de Aplicativo do Azure), no entanto, ele não é executado no seu conteúdo.

Examine previamente todos os arquivos que estão sendo carregados em seus nós de cluster do Azure Databricks ou em recursos relacionados.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: Se você tiver uma assinatura da plataforma de gerenciamento de vulnerabilidades, use os scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster do Azure Databricks e gerenciar os nós por meio do respectivo portal. Observe que cada solução de terceiros funciona de maneira diferente.

* [Como instalar o agente do Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização do Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: Para suas fontes de dados do Azure Databricks, configure um nível apropriado de redundância de dados para seu caso de uso. Por exemplo, se estiver usando uma conta de Armazenamento do Azure para o armazenamento de dados do Azure Databricks, escolha a opção de redundância adequada (LRS, ZRS, GRS, RA-GRS).

* [Fontes de dados para o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Recuperação de desastre regional para clusters do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Diretrizes**: Faça backup de qualquer chave gerenciada pelo cliente relacionada às suas implementações do Azure Databricks no Azure Key Vault. Você também pode usar a API REST e a CLI para criar um backup diário de configurações do Databricks. Você também pode usar a API REST/CLI para criar um backup diário de configurações do Databricks.

* [Como fazer backup de chaves do cofre de chaves no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: Teste a restauração de backup de chaves gerenciadas pelo cliente relacionadas às suas implementações do Azure Databricks.

* [Como restaurar chaves do cofre de chaves no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Verifique se a exclusão reversível está habilitada no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

* [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Você também pode aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: A Central de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretrizes**: Conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato de segurança da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: Exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de maneira manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

* [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias.

**Diretrizes**: * [Siga as regras de envolvimento da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
