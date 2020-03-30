---
title: Linha de base de segurança do Azure para registro de contêineres do Azure
description: Linha de base de segurança do Azure para registro de contêineres do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244283"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Linha de base de segurança do Azure para registro de contêineres do Azure

A linha de base de segurança do Azure para o Registro de Contêineres Do Azure contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: A Rede Virtual Azure fornece rede privada e segura para seus recursos azure e no local. Ao limitar o acesso ao seu registro privado de contêineres do Azure a partir de uma rede virtual do Azure, você garante que apenas os recursos na rede virtual acessem o registro. Para cenários entre premissas, você também pode configurar regras de firewall para permitir o acesso ao registro apenas a partir de endereços IP específicos. Atrás de um firewall, configure regras de acesso ao firewall e tags de serviço para acessar seu registro de contêineres.

Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Configure regras para acessar um registro de contêiner do Azure atrás de um firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e remediar recomendações de proteção de rede para ajudar a proteger os recursos da rede no Azure. Habilite os registros de fluxo do NSG e envie logs em uma conta de armazenamento para auditoria de tráfego.

Como ativar registros de fluxo de NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Proteja os recursos da rede:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável. O benchmark destina-se ao Azure App Service ou aos recursos de computação que hospedam aplicativos web.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite a proteção padrão DDoS em suas redes virtuais para proteções contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.  Implante o Firewall Azure em cada um dos limites de rede da organização com o Threat Intelligence ativado e configurado para "Alertar e negar" para tráfego de rede malicioso.

Você pode usar o acesso ao Azure Security Center Just In Time Network para configurar NSGs para limitar a exposição de pontos finais a endereços IP aprovados por um período limitado. Além disso, use o Azure Security Center Adaptive Network Hardening para recomendar configurações DE NSG que limitam portas e IPs de origem com base em informações reais de tráfego e ameaças.

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Como implantar o Firewall Do Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Entenda o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Entenda o endurecimento de rede adaptativa do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Centro de segurança do Azure apenas no tempo controle de acesso à rede:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Habilite os registros de fluxo do grupo de segurança de rede (NSG) para o NSG conectado à sub-rede que está sendo usada para proteger o registro de contêineres do Azure. Você pode gravar os logs de fluxo do NSG em uma conta de armazenamento do Azure para gerar registros de fluxo. Se necessário para investigar atividades anômalas, habilite a captura de pacotes do Azure Network Watcher.

Como ativar registros de fluxo de NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Selecione uma oferta do Azure Marketplace que suporte a funcionalidade IDS/IPS com recursos de inspeção de carga útil. Se a detecção e/ou prevenção de intrusões com base na inspeção de carga não for um requisito, o Firewall Azure com Inteligência de Ameaças pode ser usado. A filtragem baseada em inteligência do Azure Firewall Threat pode alertar e negar tráfego de e para endereços e domínios IP maliciosos conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego malicioso.

Mercado Azure:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Como implantar o Firewall Do Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com o Firewall Do Azure:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável. O Benchmark destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que precisam de acesso ao seu registro de contêineres, use tags de serviço de rede virtuais para o serviço de registro de contêineres do Azure para definir controles de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço "AzureContainerRegistry" no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Permitir o acesso por tag de serviço:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos registros de contêineres do Azure com a Política Do Azure. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.ContainerRegistry" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus registros de contêineres. 

Você pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos do Azure Resource Manager, controles RBAC e políticas, em uma definição única de projeto. Aplique facilmente o projeto a novas assinaturas e ajuste o controle e o gerenciamento por meio de versionamentos.

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: O cliente pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos do Azure Resource Manager, controles RBAC e políticas, em uma definição de projeto único. Aplique facilmente o projeto a novas assinaturas e ajuste o controle e o gerenciamento por meio de versionamentos.

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar as configurações de recursos da rede e detectar alterações nos recursos de rede relacionados aos registros de contêineres. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

Como configurar a sincronização de tempo para os recursos de computação do Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Ingerir logs via Azure Monitor para agregar dados de segurança gerados por um registro de contêiner do Azure. No Azure Monitor, use o Log Analytics Workspace(s) para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: O Azure Monitor coleta registros de recursos (anteriormente chamados de registros de diagnóstico) para eventos orientados pelo usuário em seu registro. Coletar e consumir esses dados para auditar eventos de autenticação de registro e fornecer uma trilha completa de atividade em artefatos de registro, como eventos de puxar e empurrar para que você possa diagnosticar problemas de segurança com seu registro.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável. O benchmark destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: No Monitor do Azure, defina o período de retenção do Espaço de Trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Analisar e monitorar os registros do Registro de Contêineres do Azure para obter comportamentos anômalos e revisar regularmente os resultados. Use o Espaço de Trabalho log analytics do Azure Monitor para revisar logs e executar consultas em dados de log.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Entenda o espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Use o espaço de trabalho do Azure Log Analytics para monitorar e alertar sobre atividades anômalas em registros de segurança e eventos relacionados ao seu registro de contêineres Do Azure.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Como alertar sobre os dados de log analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável. O Registro de Contêineres do Azure não processa ou produz registros relacionados a malware.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável. O Registro de Contêineres do Azure é um ponto final e não inicia a comunicação, e o serviço não consulta o DNS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável. O benchmark destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: O Azure Active Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e quepodem ser consultadas. Use o módulo Azure AD PowerShell para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para cada registro de contêiner do Azure, rastreie se a conta de administração incorporada está ativada ou desativada. Desabilite a conta quando não estiver em uso.

Como obter um papel de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Conta de administração do Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam uma senha a ser criada com requisitos de complexidade e um comprimento mínimo de senha, que diferem dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços de Marketplace que podem usar senhas padrão.

Se a conta de administrador padrão de um registro de contêiner do Azure estiver ativada, senhas complexas serão criadas automaticamente e devem ser giradas. Desabilite a conta quando não estiver em uso.

Conta de administração do Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas. Use o Azure Security Center Identity and Access Management para monitorar o número de contas administrativas.

Além disso, crie procedimentos para habilitar a conta de administração incorporada de um registro de contêiner. Desabilite a conta quando não estiver em uso.

Entenda a identidade e acesso do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Conta de administração do Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: Sempre que possível, use o SSO do Azure Active Directory em vez de configurar credenciais individuais autônomas por serviço. Use as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Para acesso individual ao registro de contêineres, use login individual integrado ao Azure Active Directory.

Entenda o SSO com o Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Login individual em um registro de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite a autenticação multifatorial do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho de acesso privilegiado) com MFA configurado para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoramento do Azure Security Center**: N/A

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use os relatórios de segurança do Azure Active Directory (Azure AD) para geração de registros e alertas quando ocorre atividade suspeita ou insegura no ambiente. Use o Azure Security Center para monitorar a atividade de identidade e acesso.

Como identificar usuários do Azure AD sinalizados para atividades de risco:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Use locais nomeados de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (Azure AD) como sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância AD do Azure:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo.

Entenda os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Você tem acesso às fontes de registro de atividade, auditoria e monitoramento do Azure Active Directory (Azure AD), que permitem que você se integre a qualquer ferramenta de gerenciamento de informações e eventos de segurança (SIEM) /Monitoring.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários do Azure Active Directory e enviando os logs de auditoria e logs de login para um Espaço de Trabalho do Log Analytics. Você pode configurar os alertas desejados no Espaço de Trabalho do Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use os recursos de Proteção de Risco e Identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas a ações suspeitas detectadas relacionadas às identidades dos usuários. 

Como ver os logins arriscados do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Não disponível; O Customer Lockbox não tem suporte para registro de contêineres do Azure.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use etiquetas de recursos para ajudar no rastreamento de registros de contêineres do Azure que armazenam ou processam informações confidenciais.

Marcar e verver imagens de contêiner ou outros artefatos em um registro, e bloquear imagens ou repositórios, para ajudar no rastreamento de imagens que armazenam ou processam informações confidenciais.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Recomendações para marcar e versia imagens de contêineres:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Bloquear uma imagem de contêiner em um registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar registros separados de contêineres, assinaturas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os recursos de armazenamento ou processamento de dados confidenciais devem ser suficientemente isolados.

Os recursos devem ser separados por rede virtual ou sub-rede, marcados adequadamente e protegidos por um grupo de segurança de rede (NSG) ou Firewall Azure.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Como criar um NSG com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar o Firewall Do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alerta ou alerta e negar com o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Implante uma ferramenta automatizada em perímetros de rede que monitore a transferência não autorizada de informações confidenciais e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: Certifique-se de que todos os clientes que se conectam ao seu Registro de Contêineres Do Azure sejam capazes de negociar TLS 1.2 ou superior. Os recursos do Microsoft Azure negociam o TLS 1.2 por padrão.

Siga as recomendações do Azure Security Center para criptografia em repouso e criptografia em trânsito, quando aplicável.

Entenda a criptografia em trânsito com o Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Registro de Contêineres do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Use o RBAC do Azure Active Directory (Azure AD) para controlar o acesso a dados e recursos em um registro de contêiner do Azure. 

Como configurar o RBAC no Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Funções e permissões do Registro de Contêineres do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: Se necessário para a conformidade com os recursos computacionais, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perda de dados baseada em host, para impor controles de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: Use criptografia em repouso em todos os recursos do Azure. Por padrão, todos os dados em um registro de contêiner do Azure são criptografados em repouso usando chaves gerenciadas pela Microsoft.

Entenda a criptografia em repouso no Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Chaves gerenciadas pelo cliente no Registro de Contêineres do Azure:https://aka.ms/acr/cmk



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: O Azure Monitor coleta registros de recursos (anteriormente chamados de registros de diagnóstico) para eventos orientados pelo usuário em seu registro. Coletar e consumir esses dados para auditar eventos de autenticação de registro e fornecer uma trilha completa de atividade em artefatos de registro, como eventos de puxar e puxar para que você possa diagnosticar problemas operacionais com seu registro.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas imagens do contêiner. Opcionalmente, implante soluções de terceiros do Azure Marketplace para realizar avaliações de vulnerabilidade de imagem.

Como implementar as recomendações de avaliação de vulnerabilidades do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integração do Registro de Contêineres do Azure com o Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que suportam o Registro de Contêineres do Azure.

Automatize as atualizações de imagens de contêiner quando atualizações para basear imagens do sistema operacional e outros patches são detectadas.

Sobre as atualizações de imagem base para tarefas do Registro de Contêineres do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Você pode usar a solução de terceiros para corrigir imagens de aplicativos.  Além disso, você pode executar tarefas do Azure Container Registry para automatizar atualizações de imagens de aplicativos em um registro de contêiner com base em patches de segurança ou outras atualizações em imagens base.

Sobre atualizações de imagem base para tarefas ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Integre o Acr (ACR) do Azure Container Com o Azure Security Center para permitir a varredura periódica de imagens de contêineres para vulnerabilidades. Opcionalmente, implante soluções de terceiros do Azure Marketplace para executar varreduras periódicas de vulnerabilidade de imagem.

Integração do Registro de Contêineres do Azure com o Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Integre o Acr (Acr) do Azure Container Center para permitir a varredura periódica de imagens de contêineres para vulnerabilidades e classificar riscos. Opcionalmente, implante soluções de terceiros do Azure Marketplace para realizar varreduras periódicas de vulnerabilidade de imagem e classificação de riscos.

Integração do Registro de Contêineres do Azure com o Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoramento do Azure Security Center**: N/A

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos etc.) dentro de sua assinatura(s).  Garanta permissões apropriadas (leia) em seu inquilino e enumeratodas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Entenda o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: O Registro de Contêineres do Azure mantém metadados, incluindo tags e manifestos para imagens em um registro. Siga as práticas recomendadas para marcar artefatos.

Sobre registros, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para marcar e versia imagens de contêineres:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: O Registro de Contêineres do Azure mantém metadados, incluindo tags e manifestos para imagens em um registro. Siga as práticas recomendadas para marcar artefatos.

Sobre registros, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para marcar e versia imagens de contêineres:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Você precisará criar um inventário de recursos azure aprovados de acordo com suas necessidades organizacionais.  

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

**Orientação**: Use a Política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados em sua assinatura(s).

Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s).  Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

**Orientação**: Analisar e monitorar os registros do Registro de Contêineres do Azure para obter comportamentos anômalos e revisar regularmente os resultados. Use o Espaço de Trabalho log analytics do Azure Monitor para revisar logs e executar consultas em dados de log.

Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Entenda o espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: A Azure Automation fornece controle completo durante a implantação, operações e descomissionamento de cargas de trabalho e recursos.  Você pode implementar sua própria solução para remover recursos não autorizados do Azure. Uma introdução ao Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Não aplicável. Benchmark é projetado para recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Aproveite a Política do Azure para restringir quais serviços você pode prover em seu ambiente.

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável. Benchmark é projetado para recursos de computação.



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o AzureResources Manager através de scripts

**Orientação**: Use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts dentro dos recursos de computação do Azure.

Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resources Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

**Orientação**: Use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts dentro dos recursos de computação do Azure.

Por exemplo, como controlar a execução de script susceptino do PowerShell em ambientes windows:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: O software necessário para operações de negócios, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro de sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Firewall Azure ou Network Security Group.

Como criar uma rede virtual:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Use a Política Do Azure ou o Centro de Segurança Do Azure para manter as configurações de segurança para todos os Recursos Do Azure.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Utilize a recomendação do Azure Security Center "Remediar vulnerabilidades em configurações de segurança em suas Máquinas Virtuais" para manter configurações de segurança em todos os recursos de computação.

Como monitorar as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como remediar as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável. O benchmark destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições de diretiva personalizadas do Azure, use os Repos do Azure para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável. O benchmark se aplica aos recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use a política do Azure para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável. O benchmark se aplica aos recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use o Azure Security Center para realizar varreduras de linha de base para seus recursos do Azure.

Use a Política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados em sua assinatura(s).

Como remediar recomendações no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável. O benchmark se aplica aos recursos de computação.


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto para seus aplicativos na nuvem.

Como se integrar com as identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

Use uma identidade gerenciada pelo Azure nas tarefas do Registro de Contêineres do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código.

Como configurar identidades gerenciadas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Use uma identidade gerenciada para autenticar em um registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o Scanner de Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Use software anti-malware gerenciado centralmente

**Orientação**: Use o Microsoft Antimalware para serviços de nuvem do Azure e máquinas virtuais para monitorar e defender continuamente seus recursos. Para Linux, use solução antimalware de terceiros.

Como configurar o Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Registro de Contêineres do Azure), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie quaisquer arquivos que estão sendo carregados para recursos azure não computados, como Serviço de Aplicativo, Armazenamento de Data Lake, Armazenamento Blob, etc.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável. O benchmark destina-se a recursos computacionais. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Os dados do registro de contêineres do Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e alta disponibilidade. O Azure Container Registry copia seus dados para que eles sejam protegidos contra eventos planejados e não planejados

Opcionalmente, reproduza georeplicar um registro de contêiner para manter réplicas de registro em várias regiões do Azure. 

Geo-replicação no Registro de Contêineres do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação:** Opcionalmente, fazer backup de imagens de contêineres importando de um registro para outro.

Fazer backup de chaves gerenciadas pelo cliente no Azure Key Vault usando ferramentas de linha de comando do Azure ou SDKs.

Importar imagens de contêiner para um registro de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Como fazer backup das chaves do cofre no Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Teste a restauração de chaves gerenciadas pelo cliente em Azure Key Vault usando ferramentas de linha de comando do Azure ou SDKs.

Como restaurar as chaves do Azure Key Vault no Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Você pode habilitar o Soft-Delete no Azure Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.

Como ativar o Soft-Delete no Cofre de Chaves:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança de Computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Azure Security Center atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marca claramente assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta de segurança) descobrir que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Do Azure usando o recurso Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas sentinelas.

Como configurar a exportação contínua:https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas e recomendações de segurança.

Como configurar aplicativos de automação e lógica de fluxo de trabalho:https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta a remediação de todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia e execução do Red Teaming e testes de penetração de sites ao vivo contra infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
