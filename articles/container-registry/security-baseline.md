---
title: Linha de base de segurança do Azure para registro de contêiner do Azure
description: Linha de base de segurança do Azure para registro de contêiner do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 95864f932fe255d561eaeb2d803b5fcc79cb2802
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184088"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Linha de base de segurança do Azure para registro de contêiner do Azure

A linha de base de segurança do Azure para o registro de contêiner do Azure contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral de linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Diretrizes**: a rede virtual do Azure fornece uma rede segura e privada para seus recursos do Azure e locais. Ao limitar o acesso ao seu registro de contêiner do Azure privado de uma rede virtual do Azure, você garante que somente os recursos na rede virtual acessem o registro. Para cenários entre instalações, você também pode configurar regras de firewall para permitir o acesso ao registro somente de endereços IP específicos. Por trás de um firewall, configure as regras de acesso de firewall e as marcas de serviço para acessar o registro de contêiner.

Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego.

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Proteja seus recursos de rede:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável. O parâmetro de comparação destina-se a Azure App serviço ou recursos de computação que hospedam aplicativos Web.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilite a proteção padrão de DDoS em suas redes virtuais para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.  Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

Você pode usar o acesso à rede just in time da central de segurança do Azure para configurar o NSGs para limitar a exposição de pontos de extremidade a endereços IP aprovados por um período limitado. Além disso, use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

Como configurar a proteção contra DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar o Firewall do Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Entenda a inteligência de ameaças integrada da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Entender a proteção de rede adaptável da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Controle de acesso à rede just in time da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: habilitar os logs de fluxo do NSG (grupo de segurança de rede) para o NSG anexado à sub-rede que está sendo usada para proteger o registro de contêiner do Azure. Você pode registrar os logs de fluxo do NSG em uma conta de armazenamento do Azure para gerar registros de fluxo. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede do Azure.

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar o observador de rede:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo. Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for um requisito, o Firewall do Azure com inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência de ameaças do firewall do Azure pode alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Como implantar o Firewall do Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com o Firewall do Azure:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável. O parâmetro de comparação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Orientação**: para recursos que precisam de acesso ao seu registro de contêiner, use marcas de serviço de rede virtual para o serviço de registro de contêiner do Azure para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço "AzureContainerRegistry" no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

Permitir acesso por marca de serviço:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados a seus registros de contêiner do Azure com o Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. ContainerRegistry" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus registros de contêiner. 

Você pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controles RBAC e políticas, em uma única definição de Blueprint. Aplique facilmente o plano gráfico às novas assinaturas e ajuste o controle e o gerenciamento de controles por meio da versão.

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como criar um Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: o cliente pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controles RBAC e políticas, em uma única definição de Blueprint. Aplique facilmente o plano gráfico às novas assinaturas e ajuste o controle e o gerenciamento de controles por meio da versão.

Como criar um Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos seus registros de contêiner. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

Como configurar a sincronização de horário para recursos de computação do Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados por um registro de contêiner do Azure. Em Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: Azure monitor coleta logs de recursos (anteriormente chamados de logs de diagnóstico) para eventos controlados pelo usuário no registro. Colete e consuma esses dados para auditar eventos de autenticação de registro e fornecer uma trilha de atividade completa em artefatos de registro, como eventos de pull e Push, para que você possa diagnosticar problemas de segurança com o registro.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável. O parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para Log Analytics espaços de trabalho:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analise e monitore os logs do registro de contêiner do Azure para comportamento anormal e analise os resultados regularmente. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Entender Log Analytics espaço de trabalho:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Diretrizes**: Use o espaço de trabalho do Azure log Analytics para monitoramento e alertas em atividades anormais em logs de segurança e eventos relacionados ao registro de contêiner do Azure.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Como alertar sobre os dados de log do log Analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável. O registro de contêiner do Azure não processa nem produz logs relacionados a anti-malware.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável. O registro de contêiner do Azure é um ponto de extremidade e não inicia a comunicação, e o serviço não consulta o DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável. O parâmetro de comparação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para cada registro de contêiner do Azure, acompanhe se a conta interna do administrador está habilitada ou desabilitada. Desabilite a conta quando ela não estiver em uso.

Como obter uma função de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de uma função de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Conta de administrador do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

Se a conta de administrador padrão de um registro de contêiner do Azure estiver habilitada, as senhas complexas serão criadas automaticamente e deverão ser giradas. Desabilite a conta quando ela não estiver em uso.

Conta de administrador do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas.

Além disso, crie procedimentos para habilitar a conta de administrador interno de um registro de contêiner. Desabilite a conta quando ela não estiver em uso.

Entenda a identidade e o acesso da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Conta de administrador do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Diretrizes**: sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Para acesso individual ao registro de contêiner, use logon individual integrado com Azure Active Directory.

Entenda o SSO com o Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Logon individual para um registro de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Diretrizes**: habilite a MFA (autenticação multifator) do Azure AD (Azure Active Directory) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar a identidade e o acesso na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoramento da central de segurança do Azure**: N/A

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use a central de segurança do Azure para monitorar a atividade de identidade e acesso.

Como identificar usuários do Azure AD sinalizados para atividades arriscadas:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância do Azure AD:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Entender os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (Azure AD), auditoria e risco de log de eventos, que permitem a integração com qualquer ferramenta de/Monitoring de Siem (gerenciamento de informações e eventos de segurança).

Você pode simplificar esse processo criando configurações de diagnóstico para Azure Active Directory contas de usuário e enviando os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics.

Como integrar os logs de atividades do Azure ao Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. 

Como exibir entradas arriscadas do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível; No momento, não há suporte para Sistema de Proteção de Dados do Cliente no registro de contêiner do Azure.

Lista de serviços Sistema de Proteção de Dados do Cliente com suporte:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Orientação**: use marcas de recurso para auxiliar no rastreamento de registros de contêiner do Azure que armazenam ou processam informações confidenciais.

Imagens de contêiner de versão e de marca ou outros artefatos em um registro e bloqueio de imagens ou repositórios, para auxiliar no rastreamento de imagens que armazenam ou processam informações confidenciais.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Recomendações para marcação e controle de versão de imagens de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Bloquear uma imagem de contêiner em um registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Orientação**: implemente registros de contêiner separados, assinaturas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os recursos que armazenam ou processam dados confidenciais devem ser suficientemente isolados.

Os recursos devem ser separados por uma rede virtual ou sub-rede, marcados adequadamente e protegidos por um NSG (grupo de segurança de rede) ou firewall do Azure.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Como criar um NSG com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alerta ou alerta e negar com o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: implante uma ferramenta automatizada em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia tais transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Orientação**: Verifique se todos os clientes que se conectam ao seu registro de contêiner do Azure são capazes de negociar o TLS 1,2 ou superior. Microsoft Azure recursos negociam TLS 1,2 por padrão.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

Entenda a criptografia em trânsito com o Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o registro de contêiner do Azure. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Diretrizes**: Use o RBAC do Azure Active Directory (Azure AD) para controlar o acesso a dados e recursos em um registro de contêiner do Azure. 

Como configurar o RBAC no Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Funções e permissões do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: se necessário para a conformidade em recursos de computação, implemente uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso aos dados, mesmo quando os dados são copiados fora de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Diretrizes**: Use a criptografia em repouso em todos os recursos do Azure. Por padrão, todos os dados em um registro de contêiner do Azure são criptografados em repouso usando chaves gerenciadas pela Microsoft.

Entender a criptografia em repouso no Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Chaves gerenciadas pelo cliente no registro de contêiner do Azure:https://aka.ms/acr/cmk



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Azure monitor coleta logs de recursos (anteriormente chamados de logs de diagnóstico) para eventos controlados pelo usuário no registro. Colete e consuma esses dados para auditar eventos de autenticação de registro e fornecer uma trilha de atividade completa em artefatos de registro, como eventos pull e pull para que você possa diagnosticar problemas operacionais com o registro.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em suas imagens de contêiner. Opcionalmente, implante soluções de terceiros do Azure Marketplace para executar avaliações de vulnerabilidade de imagem.

Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integração do registro de contêiner do Azure com a central de segurança (versão prévia):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Diretrizes**: a Microsoft executa o gerenciamento de patches nos sistemas subjacentes que dão suporte ao registro de contêiner do Azure.

Automatize as atualizações de imagem de contêiner quando atualizações de imagens base do sistema operacional e de outros patches forem detectadas.

Sobre as atualizações de imagem de base para tarefas do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Orientação**: você pode usar a solução de terceiros para corrigir imagens de aplicativos.  Além disso, você pode executar tarefas de registro de contêiner do Azure para automatizar atualizações para imagens de aplicativo em um registro de contêiner com base em patches de segurança ou outras atualizações em imagens de base.

Sobre as atualizações de imagem base para tarefas ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Orientação**: integre o registro de contêiner do Azure (ACR) com a central de segurança do Azure para habilitar a verificação periódica de imagens de contêiner para vulnerabilidades. Opcionalmente, implante soluções de terceiros do Azure Marketplace para executar verificações de vulnerabilidade de imagem periódica.

Integração do registro de contêiner do Azure com a central de segurança (versão prévia):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usar um processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas

**Orientação**: integre o registro de contêiner do Azure (ACR) com a central de segurança do Azure para habilitar a verificação periódica de imagens de contêiner para vulnerabilidades e classificar riscos. Opcionalmente, implante soluções de terceiros do Azure Marketplace para executar verificações de vulnerabilidade de imagem periódica e classificação de risco.

Integração do registro de contêiner do Azure com a central de segurança (versão prévia):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoramento da central de segurança do Azure**: N/A

**Responsabilidade**: cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

Como criar consultas com o grafo de recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Entender o RBAC do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: o registro de contêiner do Azure mantém metadados, incluindo marcas e manifestos para imagens em um registro. Siga as práticas recomendadas para a marcação de artefatos.

Sobre registros, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para marcação e controle de versão de imagens de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Diretrizes**: o registro de contêiner do Azure mantém metadados, incluindo marcas e manifestos para imagens em um registro. Siga as práticas recomendadas para a marcação de artefatos.

Sobre registros, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para marcação e controle de versão de imagens de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

**Orientação**: você precisará criar um inventário de recursos aprovados do Azure de acordo com suas necessidades organizacionais.  

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o grafo do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: analise e monitore os logs do registro de contêiner do Azure para comportamento anormal e analise os resultados regularmente. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log.

Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Entender Log Analytics espaço de trabalho:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: a automação do Azure fornece controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos.  Você pode implementar sua própria solução para remover recursos não autorizados do Azure. Uma introdução à automação do Azure:https://docs.microsoft.com/azure/automation/automation-intro


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável. O parâmetro de comparação foi projetado para recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Orientação**: Aproveite Azure Policy para restringir quais serviços você pode provisionar em seu ambiente.

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável. O parâmetro de comparação foi projetado para recursos de computação.



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com o AzureResources Manager por meio de scripts

**Orientação**: usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.

Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: Use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.

Por exemplo, como controlar a execução de script do PowerShell em ambientes Windows:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: software necessário para operações de negócios, mas pode incorrer em um risco maior para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e é suficientemente protegido com um firewall do Azure ou grupo de segurança de rede.

Como criar uma rede virtual:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: Use Azure Policy ou a central de segurança do Azure para manter as configurações de segurança para todos os recursos do Azure.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Diretrizes**: utilize a recomendação da central de segurança do Azure "corrigir vulnerabilidades nas configurações de segurança em suas máquinas virtuais" para manter as configurações de segurança em todos os recursos de computação.

Como monitorar as recomendações da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como corrigir as recomendações da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entender Azure Policy efeitos:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável. O parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável. O parâmetro de comparação se aplica aos recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: Use Azure Policy para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável. O parâmetro de comparação se aplica aos recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure.

Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Como corrigir recomendações na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável. O parâmetro de comparação se aplica aos recursos de computação.


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

Como integrar com identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer Key Vault autenticação com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

Usar uma identidade gerenciada pelo Azure nas tarefas do registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

Como configurar identidades gerenciadas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Use uma identidade gerenciada para autenticar em um registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

**Diretrizes**: implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.

Como configurar o verificador de credenciais:https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [controle de segurança: defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: Use o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais para monitorar e defender continuamente seus recursos. Para o Linux, use a solução antimalware de terceiros.

Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, registro de contêiner do Azure), no entanto, ele não é executado no conteúdo do cliente.

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável. O parâmetro de comparação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: os dados em seu registro de contêiner de Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e alta disponibilidade. O registro de contêiner do Azure copia seus dados para que eles sejam protegidos contra eventos planejados e não planejados

Opcionalmente, replique geograficamente um registro de contêiner para manter as réplicas de registro em várias regiões do Azure. 

Replicação geográfica no registro de contêiner do Azure:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: opcionalmente, faça backup de imagens de contêiner importando de um registro para outro.

Fazer backup de chaves gerenciadas pelo cliente no Azure Key Vault usando ferramentas de linha de comando do Azure ou SDKs.

Importar imagens de contêiner para um registro de contêiner:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Como fazer backup de chaves do Key Vault no Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar a restauração de chaves de backup gerenciadas pelo cliente no Azure Key Vault usando ferramentas de linha de comando do Azure ou SDKs.

Como restaurar chaves de Azure Key Vault no Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: você pode habilitar a exclusão reversível em Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

Como habilitar a exclusão reversível no Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

Como configurar Automaçãos de fluxo de trabalho na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

Consulte a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato do incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato da segurança da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas sentinela.

Como configurar a exportação contínua:https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os aplicativos lógicos:https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [controle de segurança: testes de penetração e exercícios de equipe vermelhos](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas dentro de 60 dias

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
