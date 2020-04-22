---
title: Linha de base de segurança do Azure para HDInsight
description: Linha de base de segurança do Azure para HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2ddb9a9c205135707d7d96beb106074baa1ce324
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758270"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Linha de base de segurança do Azure para HDInsight

A linha de base de segurança do Azure para HDInsight contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: A segurança do perímetro no Azure HDInsight é alcançada através de redes virtuais. Um administrador corporativo pode criar um cluster dentro de uma rede virtual e usar um GRUPO de segurança de rede (NSG) para restringir o acesso à rede virtual. Somente os endereços IP permitidos nas regras do Grupo de Segurança de Rede de entrada poderão se comunicar com o cluster Azure HDInsight. Essa configuração fornece segurança de perímetro. Todos os clusters implantados em uma rede virtual também terão um ponto final privado que se resolve para um endereço IP privado dentro da Rede Virtual para acesso HTTP privado aos gateways de cluster.

Para reduzir o risco de perda de dados por meio de exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight usando o Azure Firewall.

Como implantar o Azure HDInsight dentro de uma rede virtual e seguro com um grupo de segurança de rede:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Como restringir o tráfego de saída para clusters Azure HDInsight com o Azure Firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e remediar as recomendações de proteção de rede para o grupo de segurança virtual, sub-rede e rede que está sendo usado para proteger o cluster Azure HDInsight. Habilite os logs de fluxo do Grupo de Segurança de Rede (NSG) e envie logs em uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um Espaço de Trabalho do Azure Log Analytics e usar o Azure Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Entenda a segurança de rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicativos web.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Para proteções contra ataques DDoS, habilite a proteção Padrão Azure DDoS na rede virtual onde o Azure HDInsight é implantado. Use o Azure Security Center integrado à inteligência de ameaças para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Habilite os registros de flog do grupo de segurança de rede (NSG) para o NSG conectado à sub-rede que está sendo usada para proteger o cluster Azure HDInsight. Registre os logs de fluxo do NSG em uma conta de armazenamento do Azure para gerar registros de fluxo. Se necessário para investigar atividades anômalas, habilite a captura de pacotes do Azure Network Watcher.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: O requisito pode ser cumprido ID de controle de segurança do Azure 1.1; Implantar o cluster Azure HDInsight em uma rede virtual e seguro com um grupo de segurança de rede (NSG).

Existem várias dependências para o Azure HDInsight que requerem tráfego de entrada. O tráfego de gerenciamento de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para tráfego de gerenciamento necessário são conhecidos e publicados. Crie regras do Network Security Group com essas informações para permitir o tráfego apenas de locais confiáveis, protegendo o tráfego de entrada para os clusters.

Para reduzir o risco de perda de dados por meio de exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight usando o Azure Firewall.

Como implantar o HDInsight dentro de uma rede virtual e seguro com um grupo de segurança de rede:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Entenda as dependências do HDInsight e o uso do firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Endereços IP de gerenciamento do HDInsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicativos web.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Use tags de serviço de rede virtual para definir controles de acesso de rede em grupos de segurança de rede (NSG) que estão conectados à sub-rede em que o cluster Azure HDInsight está implantado. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Entenda e use tags de serviço para o Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados ao seu cluster Azure HDInsight. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.HDInsight" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster Azure HDInsight.

Você também pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos do Azure Resource Manager, controles RBAC e políticas, em uma definição de projeto único. Aplique facilmente o projeto a novas assinaturas e ambientes, e ajuste o controle e o gerenciamento por meio de versionamentos.

Como visualizar aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para nsgs de grupo de segurança de rede (NSGs) e outros recursos relacionados à segurança da rede e fluxo de tráfego que estão associados ao seu cluster Azure HDInsight. Para regras individuais do NSG, use o campo "Descrição" para especificar a necessidade e/ou duração dos negócios (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Use qualquer uma das definições de política incorporadas do Azure relacionadas à marcação, como "Exigir tag e seu valor" para garantir que todos os recursos sejam criados com tags e notificá-lo dos recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a interface de linha de comando (CLI) do Azure para procurar ou executar ações com base em suas Tags.

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar configurações de recursos de rede e detectar alterações para recursos de rede relacionados às implantações do Azure HDInsight. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para componentes do cluster Azure HDInsight, você pode atualizar a sincronização de tempo para suas implantações de computação.

Como configurar a sincronização de tempo para os recursos de computação do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Você pode embarcar no cluster Azure HDInsight para o Azure Monitor para agregar dados de segurança gerados pelo cluster. Aproveite as consultas personalizadas para detectar e responder a ameaças no ambiente. 

Como embarcar em um cluster Azure HDInsight para o Monitor Do Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como criar consultas personalizadas para um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite o Monitor Azure para o cluster HDInsight, direcione-o para um espaço de trabalho do Log Analytics. Isso registrará informações relevantes de cluster e métricas de sO para todos os nós de cluster DoZure HDInsight.

Como habilitar o registro para o HDInsight Cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como consultar os logs do HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: A bordo do cluster Azure HDInsight para o Monitor Azure. Certifique-se de que o espaço de trabalho do Log Analytics usado tenha o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

Como embarcar em um cluster Azure HDInsight para o Monitor Do Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como configurar o período de retenção do espaço de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: A bordo do cluster Azure HDInsight para o Monitor Azure. Certifique-se de que o espaço de trabalho do Azure Log Analytics usado tenha o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

Como embarcar em um cluster Azure HDInsight para o Monitor Do Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como configurar o período de retenção do espaço de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Use as consultas do espaço de trabalho do Azure Log Analytics para consultar os logs do Azure HDInsight:

Como criar consultas personalizadas para clusters Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Use o espaço de trabalho do Azure Log Analytics para monitorar e alertar sobre atividades anômalas em registros de segurança e eventos relacionados ao seu cluster Azure HDInsight.

Como gerenciar alertas no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Como alertar sobre os dados de log analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: O Azure HDInsight vem com o Clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto, você deve gerenciar o software e agregá-lo/monitorar manualmente quaisquer logs que a Clamscan produz.

Entenda clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Implementar solução de terceiros para registro de DNs.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Configure manualmente o registro do console em uma base por nó.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: Manter o registro da conta administrativa local criada durante o provisionamento de clusters do cluster Azure HDInsight, bem como de quaisquer outras contas criadas. Além disso, se a integração do Azure AD for usada, o Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, que podem ser consultadas. Use o módulo Azure AD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, você pode usar as recomendações de identidade e gerenciamento de acesso do Azure Security Center.

Como obter um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Como monitorar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: Ao provisionar um cluster, o Azure exige que você crie novas senhas para o portal web e acesso ao Secure Shell (SSH). Não há senhas padrão para alterar, no entanto, você pode especificar diferentes senhas para acesso a SSH e portal web.

Como definir senhas ao provisionar um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Integrar autenticação para o cluster Azure HDInsight com o Azure Active Directory. Crie políticas e procedimentos em torno do uso de contas administrativas dedicadas.

Além disso, você pode usar as recomendações de identidade e gerenciamento de acesso do Azure Security Center.

Como integrar a autenticação do Azure HDInsight com o Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Como monitorar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: Use o Azure HDInsight ID Broker para fazer login nos clusters do Enterprise Security Package (ESP) usando autenticação multifatorial, sem fornecer senhas. Se você já fez login em outros serviços do Azure, como o portal Azure, você pode fazer login no seu cluster Azure HDInsight com uma única experiência de login (SSO).

Como ativar o Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite o Azure AD MFA e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center. Os clusters Do Azure HDInsight com o Pacote de Segurança Corporativa configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data. Ao autenticar com autenticação multifatorial (MFA) ativada, os usuários serão desafiados a fornecer um segundo fator de autenticação.

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho de acesso privilegiado) com autenticação multifatorial (MFA) configurada para fazer login e configurar seus clusters e recursos relacionados do Azure HDInsight.

Saiba mais sobre estações de trabalho de acesso privilegiado:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Corporativa configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar. Você pode usar relatórios de segurança do Azure Active Directory (AAD) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente AAD. Use o Azure Security Center para monitorar a atividade de identidade e acesso.

Como identificar usuários aAD sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Corporativa configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar. Use locais nomeados de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AAD) como sistema central de autenticação e autorização. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

Os clusters Azure HDInsight com o Enterprise Security Package (ESP) configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters.

Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Como configurar o Pacote de Segurança Corporativa com os Serviços de Domínio do Diretório Ativo do Azure no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: Use a autenticação do Azure Active Directory (AAD) com o cluster Azure HDInsight. O AAD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo. 

Como usar as avaliações de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Use os logs de login e auditoria do Azure Active Directory (AAD) para monitorar tentativas de acessar contas desativadas; esses logs podem ser integrados em qualquer ferramenta de monitoramento/SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários AAD, enviando os logs de auditoria e logs de login para um espaço de trabalho do Azure Log Analytics. Configure os alertas desejados no espaço de trabalho do Azure Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Os clusters Azure HDInsight com o Enterprise Security Package (ESP) configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters.  Use o recurso AaD (Risk Detections and Identity Protection) do Azure Active Directory (AAD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas às identidades dos usuários. Além disso, você pode ingerr dados no Azure Sentinel para mais investigações.

Como visualizar os logins arriscados do AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Não disponível; O Customer Lockbox ainda não foi suportado para o Azure HDInsight.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags em recursos relacionados às implantações do Azure HDInsight para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os clusters Azure HDInsight e quaisquer contas de armazenamento associadas devem ser separados por rede/sub-rede virtual, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou firewall Do Azure. Os dados de cluster devem ser contidos em uma conta de armazenamento azure segura ou no Azure Data Lake Storage (Gen1 ou Gen2).

Escolha opções de armazenamento para o cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Como garantir o armazenamento do Lago de Dados Do Azure:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Como proteger contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Para os clusters Azure HDInsight armazenando ou processando informações confidenciais, marque o cluster e os recursos relacionados como sensíveis usando tags. Para reduzir o risco de perda de dados por meio de exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight usando o Azure Firewall.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Como restringir o tráfego de saída para clusters Azure HDInsight com o Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: Criptografe todas as informações confidenciais em trânsito. Certifique-se de que todos os clientes que se conectam ao cluster Azure HDInsight ou aos armazenamentos de dados de cluster (Azure Storage Accounts ou Azure Data Lake Storage Gen1/Gen2) sejam capazes de negociar TLS 1.2 ou superior. Os recursos do Microsoft Azure negociarão o TLS 1.2 por padrão. 

Entenda a criptografia de armazenamento do Lago de Dados do Azure em trânsito:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Entenda a criptografia da conta de armazenamento do Azure em trânsito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para recursos de armazenamento ou computação do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Com o Azure HDInsight Enterprise Security Package (ESP), você pode usar o Apache Ranger para criar e gerenciar políticas de controle de acesso e ofuscação de dados para seus dados armazenados em arquivos, pastas, bancos de dados, tabelas e linhas/colunas. O administrador hadoop pode configurar o controle de acesso baseado em função (RBAC) para proteger apache hive, HBase, Kafka e Spark usando esses plugins no Apache Ranger.

A configuração de políticas RBAC com o Apache Ranger permite associar permissões a um papel na organização. Essa camada de abstração facilita garantir que as pessoas tenham apenas as permissões necessárias para realizar suas responsabilidades de trabalho.

Configurações do pacote de segurança corporativa com serviços de domínio de diretório ativo do Azure no HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Visão geral da segurança corporativa no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: Para os clusters Azure HDInsight armazenando ou processando informações confidenciais, marque o cluster e os recursos relacionados como sensíveis usando tags. Os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para recursos de armazenamento ou computação do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: Se usar o Azure SQL Database para armazenar metadados Apache Hive e Apache Oozie, certifique-se de que os dados SQL permaneçam criptografados o tempo todo. Para contas de armazenamento do Azure e armazenamento de datalake (Gen1 ou Gen2), recomenda-se permitir que a Microsoft gerencie suas chaves de criptografia, no entanto, você tem a opção de gerenciar suas próprias chaves.

Como gerenciar chaves de criptografia para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Como criar o Azure Data Lake Storage usando chaves de criptografia gerenciadas pelo cliente:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Entenda a criptografia para o Banco de Dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Como configurar a criptografia de dados transparente sql banco de dados usando chaves gerenciadas pelo cliente:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Configure configurações de diagnóstico para contas de armazenamento do Azure associadas aos clusters Azure HDInsight para monitorar e registrar todas as operações CRUD contra dados de cluster. Habilite a auditoria para quaisquer contas de armazenamento ou data lake stores associadas ao cluster Azure HDInsight.

Como habilitar o registro/auditoria adicional para uma conta de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Como habilitar registro/auditoria adicional para o Armazenamento do Lago de Dados do Azure:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Implementar uma solução de gerenciamento de vulnerabilidades de terceiros.

Opcionalmente, se você tiver uma assinatura de plataforma de gerenciamento de vulnerabilidades Rapid7, Qualys ou qualquer outra plataforma de gerenciamento de vulnerabilidades, você pode usar ações de script para instalar agentes de avaliação de vulnerabilidades em seus ates de cluster Do Zure HDInsight e gerenciar os nós através do respectivo portal.

Como instalar o Agente Rapid7 manualmente:

https://insightvm.help.rapid7.com/docs/install

Como instalar o Qualys Agent manualmente:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Como usar ações de script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: As atualizações automáticas do sistema foram habilitadas para imagens de nó de cluster, no entanto, você deve reinicializar periodicamente nós de cluster para garantir que as atualizações sejam aplicadas.

Microsoft para manter e atualizar imagens de nó base Azure HDInsight.

Como configurar o cronograma de patches do sistema operacional para clusters HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Use ações de script ou outros mecanismos para corrigir seus clusters Azure HDInsight. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

Como configurar o cronograma de patches do Sistema Operacional para clusters Azure HDInsight baseados em Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Como usar ações de script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Implementar uma solução de gerenciamento de vulnerabilidades de terceiros que tenha a capacidade de comparar varreduras de vulnerabilidade ao longo do tempo. Se você tiver uma assinatura Rapid7 ou Qualys, você pode usar o portal desse fornecedor para visualizar e comparar varreduras de vulnerabilidades back-to-back.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Use um programa comum de pontuação de risco (por exemplo, Sistema de Pontuação de Vulnerabilidade Comum) ou as classificações de risco padrão fornecidas pela ferramenta de varredura de terceiros.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos etc.), incluindo clusters Azure HDInsight, dentro de sua assinatura(s).  Certifique-se de ter permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

Como criar consultas com o Gráfico de Recursos do Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags aos recursos do Azure que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Defina lista de recursos aprovados do Azure e software aprovado para seus recursos de computação

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

**Orientação**: Use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s). Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

**Orientação**: Implementar uma solução de terceiros para monitorar os álos de cluster para aplicativos de software não aprovados.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos etc.), incluindo clusters Azure HDInsight, dentro de sua assinatura(s).  Remova todos os recursos não aprovados do Azure que você descobrir. Para os ates de cluster Do Azure HDInsight, implemente uma solução de terceiros para remover ou alertar em software não aprovado.

Como criar consultas com o Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Para os ates de cluster Do Azure HDInsight, implemente uma solução de terceiros para impedir a execução de software não autorizado.


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Para os contratos de cluster Do Azure HDInsight, implemente uma solução de terceiros para impedir a execução de tipos de arquivos não autorizados.


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resources Manager através de scripts

**Orientação**: Use o Azure Conditional Access para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

**Orientação**: Não aplicável; Isso não se aplica ao Azure HDInsight, pois os usuários (não administradores) do cluster não precisam ter acesso aos álos individuais para executar trabalhos. O administrador de cluster tem acesso raiz a todos os nós de cluster.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicativos web.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Use aliases de diretiva do Azure no namespace "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster HDInsight.

Como visualizar aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Imagens do sistema operacional Azure HDInsight gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operacional de seus nós de cluster. 


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras para seus clusters Azure HDInsight e recursos relacionados.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Imagens do sistema operacional Azure HDInsight gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração de estado em nível de SO.


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições de diretiva personalizadas do Azure, use OZure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; imagens personalizadas não aplicáveis ao Azure HDInsight.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.HDInsight" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Implemente uma solução de terceiros para manter o estado desejado para os sistemas operacionais do nó de cluster.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use aliases de diretiva do Azure no namespace "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração do seu cluster HDInsight.

Como visualizar aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Implemente uma solução de terceiros para monitorar o estado dos sistemas operacionais do nó de cluster.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: O Azure HDInsight inclui o suporte bring your own key (BYOK) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso.

Todos os discos gerenciados no Azure HDInsight são protegidos com o Azure Storage Service Encryption (SSE). Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar byok, você fornecerá a chave de criptografia para o Azure HDInsight usá-la e gerenciá-la usando o Azure Key Vault.

O Key Vault também pode ser usado com implantações do Azure HDInsight para gerenciar chaves para armazenamento em cluster (Contas de armazenamento Azure e Armazenamento do Lago de Dados do Azure)

Como trazer sua própria chave para Apache Kafka no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Como gerenciar chaves de criptografia para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: As identidades gerenciadas podem ser usadas no Azure HDInsight para permitir que seus clusters acessem serviços de domínio do Azure Active Directory, acessem o Azure Key Vault ou acessem arquivos no Azure Data Lake Storage Gen2.

Entenda identidades gerenciadas com o Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Se usar qualquer código relacionado à sua implantação do Azure HDInsight, você pode implementar o Credential Scanner para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner de Credencial:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Use software anti-malware gerenciado centralmente

**Orientação**: O Azure HDInsight vem com o Clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto, você deve gerenciar o software e agregá-lo/monitorar manualmente quaisquer logs que a Clamscan produz.

Entenda clamscan para Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie todos os arquivos que estão sendo carregados em quaisquer recursos do Azure relacionados à implantação do cluster Azure HDInsight, como Data Lake Storage, Blob Storage, etc. A Microsoft não pode acessar os dados do cliente nessas instâncias.

Entenda o Microsoft Antimalware for Azure Cloud Services and Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: O Azure HDInsight vem com o Clamscan pré-instalado e habilitado para as imagens do nó de cluster. O Clamscan executará atualizações de motor e definição automaticamente, no entanto, a agregação e o gerenciamento de logs precisarão ser realizados manualmente.

Entenda clamscan para Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Ao usar uma conta de armazenamento Azure para o armazenamento de dados do cluster HDInsight, escolha a opção de redundância apropriada (LRS, ZRS, GRS, RA-GRS).  Ao usar um banco de dados SQL do Azure HDInsight, configure a replicação active geo..

Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como configurar a redundância para bancos de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: Ao usar uma conta de armazenamento Azure para o armazenamento de dados do cluster Azure HDInsight, escolha a opção de redundância apropriada (LRS,ZRS, GRS, RA-GRS). Se usar o Azure Key Vault para qualquer parte da implantação do Azure HDInsight, certifique-se de que suas chaves estão em backup.

Escolha opções de armazenamento para o cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como fazer backup das chaves do Cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Se o Azure Key Vault estiver sendo usado com a implantação do Azure HDInsight, teste a restauração das chaves gerenciadas pelo cliente com backup.

Como trazer sua própria chave para Apache Kafka no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Como restaurar as chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Se o Azure Key Vault estiver sendo usado com a implantação do Azure HDInsight, habilite o Soft-Delete no Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.

Como ativar o Soft-Delete no Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

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

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário. Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada.

Como definir o contato de segurança do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Do Azure usando o recurso Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas sentinelas.

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
