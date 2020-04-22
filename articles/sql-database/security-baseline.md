---
title: Linha de base de segurança do Azure para banco de dados SQL do Azure
description: Linha de base de segurança do Azure para banco de dados SQL do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8c0e7c6dfb1275e1fafbab1dd9e89cb2fe4376ad
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759144"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Linha de base de segurança do Azure para banco de dados SQL do Azure

A linha de base de segurança do Azure Para o Banco de Dados SQL do Azure contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Você pode habilitar o Azure Private Link para permitir o acesso aos Serviços Azure PaaS (por exemplo, Banco de Dados SQL) e serviços hospedados do cliente/parceiro do Azure em um Endpoint privado em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. 

Para permitir que o tráfego alcance o Banco de Dados SQL do Azure, use as marcas de serviço do SQL para permitir o tráfego de saída por meio de Grupos de Segurança de Rede.

As regras de rede virtual permitem que o Banco de Dados SQL do Azure só aceite comunicações enviadas de sub-redes selecionadas dentro de uma rede virtual.

Como configurar o Private Link para o Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Como usar os pontos finais e regras do serviço de rede virtual para servidores de banco de dados:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e remediar as recomendações de proteção de rede para a sub-rede para a sua sub-rede para a nossa base do Azure SQL Database Server. 

Para as Máquinas Virtuais (VM) do Azure que estarão conectadas à instância do Servidor de Banco de Dados SQL do Azure, habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) para os NSGs que protegem essas VMs e enviam logs em uma conta de armazenamento do Azure para auditoria de tráfego. 

Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Entenda a segurança de rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como ativar e usar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Entenda a segurança de rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicativos web.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite o Padrão de Proteção DDoS nas redes virtuais associadas às instâncias do SQL Server para proteções contra ataques distribuídos de negação de serviço. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Para as VMs (Azure Virtual Machines, máquinas virtuais) que estarão conectadas à sua instância de banco de dados SQL do Azure, habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) para os NSGs que protegem essas VMs e enviam logs para uma conta de armazenamento do Azure para auditoria de tráfego. Se necessário para investigar atividades anômalas, habilite a captura de pacotes do Network Watcher.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Habilite o ADVANCED Threat Protection (ATP) para o Banco de Dados SQL do Azure.  Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. O Advanced Threat Protection também integra alertas com o Azure Security Center. 

Entenda e use o Advanced Threat Protection para o Banco de Dados SQL do Azure:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicativos web.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Use tags de serviço de rede virtuais para definir controles de acesso à rede em grupos de segurança de rede ou Firewall Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Ao usar os pontos finais de serviço para o Banco de Dados SQL do Azure, é necessário a saída para o Banco de Dados Azure SQL Endereços IP públicos: Os NSGs (Network Security Groups, grupos de segurança de rede) devem ser abertos aos IPs de banco de dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando tags de serviço NSG para o Banco de Dados SQL Do Azure.

Entenda as tags de serviço com pontos finais de serviço para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Entenda e use tags de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança de rede para as instâncias do servidor de banco de dados Azure SQL com a Diretiva Azure. Você pode usar o namespace "Microsoft.Sql" para definir definições de políticas personalizadas ou usar qualquer uma das definições de diretiva incorporadas projetadas para a proteção da rede de servidor do Banco de Dados Azure SQL. Um exemplo de uma política de segurança de rede incorporada aplicável para o servidor Azure SQL Database seria: "O SQL Server deve usar um ponto final de serviço de rede virtual".

 

Use o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos de gerenciamento de recursos do Azure, controle de acesso baseado em função (RBAC) e políticas, em uma única definição de projeto. Aplique facilmente o projeto a novas assinaturas e ambientes, e ajuste o controle e o gerenciamento por meio de versionamentos.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para grupos de segurança de rede (NSG) e outros recursos relacionados à segurança da rede e ao fluxo de tráfego. Para regras individuais do NSG, use o campo "Descrição" para especificar a necessidade e/ou duração dos negócios (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Use qualquer uma das definições de política incorporadas do Azure relacionadas à marcação, como "Exigir tag e seu valor" para garantir que todos os recursos sejam criados com tags e notificá-lo dos recursos não marcados existentes.

Você pode usar o Azure PowerShell ou o Azure CLI para procurar ou executar ações com base em recursos com base em suas tags.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar configurações de recursos de rede e detectar alterações para recursos de rede relacionados às instâncias do servidor do Banco de Dados SQL do Azure. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos do Azure. Você pode atualizar a sincronização de tempo para suas implantações de computação.

Como configurar a sincronização de tempo para os recursos de computação do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Habilite a auditoria do Banco de Dados SQL do Azure para rastrear eventos de banco de dados e acscrevê-los em um registro de auditoria em sua conta de armazenamento Azure, espaço de trabalho do Log Analytics ou hubs de eventos.

Além disso, você pode transmitir a telemetria de diagnósticos Azure SQL no Azure SQL Analytics, uma solução em nuvem que monitora o desempenho de bancos de dados Azure SQL, pools elásticos e instâncias gerenciadas em escala e em várias assinaturas. Pode ajudá-lo a coletar e visualizar as métricas de desempenho do Banco de Dados SQL do Azure e tem uma inteligência interna para solução de problemas de desempenho.

Como configurar a auditoria para o seu banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Como coletar logs e métricas da plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Como transmitir diagnósticos no Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite a auditoria na instância do servidor do banco de dados Azure SQL e escolha um local de armazenamento para os logs de auditoria (Azure Storage, Log Analytics ou Event Hub).

Como habilitar a auditoria para o Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; este benchmark destina-se a recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: Ao armazenar seus logs de banco de dados SQL do Azure em um Espaço de Trabalho do Log Analytics, defina o período de retenção de log de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Analisar e monitorar registros para comportamentos anômalos e revisar regularmente os resultados. Use o Advanced Threat Protection do Azure Security Center para alertar sobre atividades incomuns relacionadas à sua instância de banco de dados SQL do Azure. Alternativamente, configure alertas com base em valores métricos ou entradas do Log de atividade do Azure relacionadas às instâncias do banco de dados SQL do Azure.

Entenda a Proteção avançada contra ameaças e alerta para o Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como configurar alertas personalizados para o Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Use o Azure Security Center Advanced Threat Protection para bancos de dados SQL Do Azure para monitorar e alertar sobre atividades anômalas. Habilite a segurança avançada de dados para seus bancos de dados SQL. O Advanced Data Security inclui funcionalidade para descobrir e classificar dados confidenciais, descobrir e mitigar potenciais vulnerabilidades de banco de dados e detectar atividades anômalas que possam indicar uma ameaça ao seu banco de dados.

Entenda a Proteção avançada contra ameaças e alerta para o Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como ativar a segurança avançada de dados para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Como gerenciar alertas no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; para o Azure SQL Server, a solução anti-malware é gerenciada pela Microsoft na plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável; O registro de DNS não é aplicável ao Azure SQL Server.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável; A auditoria da linha de comando não é aplicável ao Azure SQL Server.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: O Azure Active Directory (AAD) tem funções incorporadas que devem ser explicitamente atribuídas e quepodem ser consultadas. Use o módulo AAD PowerShell para executar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.

Como obter um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O Azure Active Directory não tem o conceito de senhas padrão. Ao provisionar uma instância de banco de dados SQL do Azure, é recomendável que você opte por integrar a autenticação com o Azure Active Directory.

Como configurar e gerenciar a autenticação do Azure Active Directory com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Criar políticas e procedimentos em torno do uso de contas administrativas dedicadas. Use o Azure Security Center Identity and Access Management para monitorar o número de contas administrativas.

Entenda a identidade e acesso do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: Não aplicável; enquanto você pode configurar a autenticação do Diretório Ativo do Azure para integrar-se ao Azure SQL Server, a ativação única não é suportada.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite a Autenticação Multifatorial (AAD) do Azure Active Directory (AAD) e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use uma Estação de Trabalho de Acesso Privilegiado (PAW) com MFA de autenticação multifatorial configurada para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use relatórios de segurança do Azure Active Directory para geração de registros e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Use o Advanced Threat Protection for Azure SQL Database para detectar atividades anômalas que indiquem tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Como identificar usuários do Azure AD sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Revise a Proteção Avançada contra Ameaças e alertas potenciais:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Use locais nomeados de acesso condicional para permitir o acesso ao Portal e ao Azure Resource Management a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Crie um administrador aad (AAD) do Azure Active Directory (AAD) para as instâncias do servidor de banco de dados SQL do Azure.

Como configurar e gerenciar a autenticação do Azure Active Directory com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory (AAD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as avaliações de acesso ao Azure Identity para gerenciar eficientemente membros do grupo, acesso a aplicativos corporativos e atribuições de função. O acesso dos usuários pode ser revisto regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Como usar as avaliações de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Configure a autenticação do Azure Active Directory (AAD) com o Azure SQL e crie configurações de diagnóstico para contas de usuários do Azure Active Directory, enviando os logs de auditoria e logs de login para um espaço de trabalho do Log Analytics. Configure os alertas desejados no espaço de trabalho do Log Analytics.

Como configurar e gerenciar a autenticação do Azure Active Directory com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Como integrar os Logs de Atividades do Azure no Monitor do Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use o Aad (Identity Protection, diretório ativo do Azure) e detecções de risco para configurar respostas automatizadas a ações suspeitas detectadas relacionadas às identidades dos usuários. Além disso, você pode ingerr dados no Azure Sentinel para mais investigações.

Como visualizar os logins de risco do Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Em cenários de suporte onde a Microsoft precisa acessar dados de clientes, o Azure Customer Lockbox fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente.

Entenda o Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os recursos devem ser separados pela Vnet/Subnet, marcados adequadamente e protegidos dentro de um FIREWALL NSG ou Azure. Os recursos de armazenamento ou processamento de dados confidenciais devem ser isolados. Use Private Link; implantar o Azure SQL Server dentro do Seu Vnet e conectar-se privadamente usando pontos finais privados.

Como criar assinaturas adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar o Private Link para o Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Para bancos de dados Azure SQL armazenando ou processando informações confidenciais, marque o banco de dados e os recursos relacionados como sensíveis usando tags. Configure o Private Link em conjunto com as tags de serviço do grupo de segurança de rede em suas instâncias de banco de dados SQL do Azure para evitar a exfiltração de informações confidenciais.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Como configurar o Private Link e os NSGs para evitar a exfiltração de dados nas instâncias do Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: O Banco de Dados SQL do Azure protege seus dados criptografando dados em movimento com o Transport Layer Security. O SQL Server impõe a criptografia (SSL/TLS) o tempo todo para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de Encrypt ou TrustServerCertificate na seqüência de conexão.

Entenda a criptografia SQL do Azure em trânsito:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Use o recurso de detecção e classificação de dados do Banco de Dados Azure SQL. A detecção e classificação de dados fornece recursos avançados incorporados ao &amp; Banco de Dados SQL do Azure para descobrir, classificar, rotular a proteção dos dados confidenciais em seus bancos de dados.

Como usar a detecção e classificação de dados para o Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Use o Azure Active Directory (AAD) para autenticar e controlar o acesso às instâncias do Banco de Dados SQL do Azure.

Como integrar o Azure SQL Server com o Azure Active Directory para autenticação:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Como controlar o acesso no Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: A Microsoft gerencia a infra-estrutura subjacente para o Banco de Dados SQL do Azure e implementou controles rigorosos para evitar a perda ou exposição dos dados dos clientes.

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: A criptografia de dados transparente (TDE) ajuda a proteger o Banco de Dados SQL do Azure, a instância gerenciada do Azure SQL e o Azure Data Warehouse contra a ameaça de atividade off-line maliciosa criptografando dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, a TDE está habilitada para todos os bancos de dados SQL do Azure recém-implantados. A chave de criptografia TDE pode ser gerenciada pela Microsoft ou pelo cliente.

Como gerenciar criptografia de dados transparente e usar suas próprias chaves de criptografia:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Do Azure com o Registro de Atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de produção dos bancos de dados SQL do Azure e outros recursos críticos ou relacionados.

Como criar alertas para eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Habilite a Segurança Avançada de Dados para o Banco de Dados SQL do Azure e siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade em seus servidores SQL Do Azure.

Como executar avaliações de vulnerabilidade sustais em seus bancos de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Como ativar a segurança avançada de dados:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Como implementar as recomendações de avaliação de vulnerabilidades do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Não aplicável; este benchmark destina-se a recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Habilite varreduras periódicas recorrentes para as instâncias do banco de dados SQL do Azure; isso configurará uma avaliação de vulnerabilidade para executar automaticamente uma varredura em seu banco de dados uma vez por semana. Um resumo dos resultados da verificação será enviado aos endereços de email fornecidos. Compare os resultados para verificar se as vulnerabilidades foram corrigidas.

Como exportar um relatório de avaliação de vulnerabilidades no Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Use as classificações de risco padrão (Secure Score) fornecidas pelo Azure Security Center.

Entenda a pontuação segura do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar e descobrir todos os recursos (incluindo as instâncias do Azure SQL Server) dentro de sua assinatura(s).  Certifique-se de ter permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags aos recursos do Azure que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Defina lista de recursos aprovados do Azure e software aprovado para seus recursos de computação

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

**Orientação**: Use a Diretiva Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s). Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Use a Diretiva Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s). Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações em execução em recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resources Manager através de scripts

**Orientação**: Use o Azure Conditional Access para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: Não aplicável; esta recomendação destina-se ao App Service ou aos recursos de computação que hospedam aplicativos desktop ou web.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Use as recomendações do Azure Policy ou do Azure Security Center para servidores/bancos de dados SQL do Azure para manter configurações de segurança para todos os Recursos do Azure.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destinada a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições de diretiva personalizadas do Azure, use OZure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva azure no namespace "Microsoft.SQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Aproveite o Azure Security Center para realizar varreduras de linha de base para seus servidores e bancos de dados SQL do Azure.

Como remediar recomendações no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Use o Azure Key Vault para armazenar chaves de criptografia para o TDE (Azure SQL Database Transparent Data Encryption, criptografia de dados transparente).

Como proteger dados confidenciais armazenados no Azure SQL Server e armazenar as chaves de criptografia no Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AAD). As Identidades Gerenciadas permitem que você se autentique em qualquer serviço que suporte a autenticação AAD, incluindo o Azure Key Vault, sem quaisquer credenciais em seu código.

Tutorial: Use uma identidade gerenciada atribuída ao sistema Windows VM para acessar o Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Como configurar identidades gerenciadas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Implemente o Scanner de Credencial para identificar credenciais dentro do seu código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner de Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Use software anti-malware gerenciado centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Serviço de Aplicativos Do Azure), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie qualquer conteúdo que seja carregado para recursos não computados do Azure, como Serviço de Aplicativo, Armazenamento de Data Lake, Armazenamento Blob, Servidor SQL do Azure, etc. A Microsoft não pode acessar seus dados nessas instâncias.

Entenda o Microsoft Antimalware for Azure Cloud Services and Virtual Machines:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Para proteger sua empresa contra perda de dados, o Azure SQL Database cria automaticamente backups completos do banco de dados semanalmente, backups diferenciais de banco de dados a cada 12 horas e backups de log de transações a cada 5 a 10 minutos. Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup configurável de suporte básico para restauração point-in-time, até 35 dias.

Para atender a diferentes requisitos de conformidade, você pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuales. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção.

Entenda backups e continuidade de negócios com o Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: O Banco de Dados Azure SQL cria automaticamente os backups do banco de dados mantidos entre 7 e 35 dias e usa o armazenamento geo-redundante de acesso de leitura do Azure (RA-GRS) para garantir que eles sejam preservados mesmo que o data center não esteja disponível. Esses backups são criados automaticamente. Se necessário, habilite backups georedundantes de longo prazo para seus bancos de dados SQL do Azure.

Se usar as chaves gerenciadas pelo cliente para criptografia de dados transparentes, certifique-se de que suas chaves estão sendo backup.

Entenda backups no Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Como fazer backup das chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Certifique-se de capacidade de executar periodicamente a restauração de dados de conteúdo dentro do Backup do Azure. Se necessário, teste o conteúdo de restauração para uma VLAN isolada. Teste a restauração de chaves gerenciadas pelo cliente.

Como restaurar as chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Como recuperar backups do Banco de Dados SQL do Azure usando restauração point-in-time:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Habilite a exclusão suave no Azure Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.

Como ativar a exclusão suave no Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Certifique-se de que existam planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gerenciamento de incidentes.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Security Center atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você atende a cada alerta, para que quando um recurso estiver comprometido, você possa chegar a ele imediatamente. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Alertas de segurança no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Você pode consultar a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e recursos de TI:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada.

Como definir o contato de segurança do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Do Azure usando o recurso Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas para o Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas e recomendações de segurança.

Como configurar aplicativos de automação e lógica de fluxo de trabalho:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta a remediação de todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução do Red Teaming e testes de penetração de sites ao vivo contra a infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
