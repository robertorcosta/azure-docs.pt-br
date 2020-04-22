---
title: Linha de base de segurança do Azure para cache azure para Redis
description: Linha de base de segurança do Azure para cache azure para Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172ac4e13201457f62d722236dff130a312cfdeb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755504"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Linha de base de segurança do Azure para cache azure para Redis

A linha de base de segurança do Azure para cache do Azure para Redis contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Implante o cache do Azure para a instância Redis dentro de uma rede virtual (VNet). Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçável publicamente e somente pode ser acessada de máquinas virtuais e aplicativos dentro da rede virtual.

Você também pode especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando regras de firewall são configuradas, apenas as conexões de cliente de intervalos de endereços IP especificados podem se conectar ao cache.

Como configurar o suporte à rede virtual para um cache Premium Azure para Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Como configurar o Cache do Azure para as regras de firewall redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Quando as máquinas virtuais são implantadas na mesma rede virtual que o cache do Azure para a instância Redis, você pode usar os grupos de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Habilite os logs de fluxo do NSG e envie logs em uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: A implantação da Rede Virtual Do Azure (VNet) oferece maior segurança e isolamento para o cache do Azure para Redis, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Quando implantado em um VNet, o Azure Cache for Redis não é endereçado publicamente e só pode ser acessado a partir de máquinas virtuais e aplicativos dentro do VNet.

Habilite o Padrão de Proteção DDoS nos VNets associados ao cache do Azure para instâncias Redis para se proteger contra ataques de negação de serviço distribuídos (DDoS). Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar o suporte à rede virtual para um cache Premium Azure para Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Gerenciar o Padrão de Proteção DDoS do Azure usando o portal Azure:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Quando as máquinas virtuais são implantadas na mesma rede virtual que o cache do Azure para a instância Redis, você pode usar os grupos de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Habilite os logs de fluxo do NSG e envie logs em uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Ao usar o Cache do Azure para Redis com seus aplicativos da Web em execução no Azure App Service ou instâncias de computação, implante todos os recursos dentro de uma Rede Virtual Do Azure (VNet) e proteja com um WAF (Azure Web Application Firewall) no Gateway de aplicação da Web. Configure o WAF para ser executado em "Modo de Prevenção". O modo prevenção bloqueia invasões e ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

Alternativamente, você pode selecionar uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com recursos de inspeção de carga e/ou detecção de anomalias.

Entenda os recursos do Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Como implantar o Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Use tags de serviço de rede virtuais para definir controles de acesso à rede em grupos de segurança de rede (NSG) ou Firewall Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Você também pode usar o ASG (Application Security Groups, grupos de segurança de aplicativos) para ajudar a simplificar a configuração de segurança complexa. Os ASGs permitem configurar a segurança da rede como uma extensão natural da estrutura de um aplicativo, permitindo que você agrupe máquinas virtuais e defina políticas de segurança de rede com base nesses grupos.

Tags de serviço de rede virtual:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Grupos de segurança de aplicativos:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados ao cache do Azure para instâncias Redis com a Diretiva Azure. Use alias de diretiva do Azure nos espaços de nome "Microsoft.Cache" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cache do Azure para instâncias Redis. Você também pode fazer uso de definições de políticas incorporadas, tais como:

Apenas conexões seguras com o Cache Redis devem ser habilitadas

A Proteção contra DDoS Standard deve ser habilitada

Você também pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos ARM (Azure Resource Manager, controle de acesso baseado em função) e políticas, em uma definição de projeto único. Aplique facilmente o projeto a novas assinaturas e ambientes, e ajuste o controle e o gerenciamento por meio de versionamentos.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para recursos de rede associados à implantação do Cache Do Azure para Redis, a fim de organizá-las logicamente em uma taxonomia.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o registro de atividade do Azure para monitorar as configurações dos recursos da rede e detectar alterações nos recursos de rede relacionados ao cache do Azure para instâncias Redis. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo usada para os recursos do Azure, como o Cache do Azure para Redis, para carimbos de tempo nos registros.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Habilite as configurações de diagnóstico do Azure Activity Log e envie os logs para um espaço de trabalho do Log Analytics, hub de eventos Do Zure ou conta de armazenamento Azure para arquivamento. Os registros de atividade fornecem informações sobre as operações realizadas no cache do Azure para instâncias Redis no nível do plano de controle. Usando dados do Azure Activity Log, você pode determinar o "o que, quem e quando" para quaisquer operações de gravação (PUT, POST, DELETE) realizadas no nível do plano de controle para o cache do Azure para instâncias Redis.

Como ativar as configurações de diagnóstico para o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico do Azure Activity Log e envie os logs para um espaço de trabalho do Log Analytics, hub de eventos Do Zure ou conta de armazenamento Azure para arquivamento. Os registros de atividade fornecem informações sobre as operações realizadas no cache do Azure para instâncias Redis no nível do plano de controle. Usando dados do Azure Activity Log, você pode determinar o "o que, quem e quando" para quaisquer operações de gravação (PUT, POST, DELETE) realizadas no nível do plano de controle para o cache do Azure para instâncias Redis.

Embora as métricas estejam disponíveis habilitando configurações de diagnóstico, o registro de auditoria no plano de dados ainda não está disponível para o Cache do Azure para Redis.

Como ativar as configurações de diagnóstico para o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: No Azure Monitor, defina o período de retenção de log para espaços de trabalho do Log Analytics associados ao cache do Azure para instâncias Redis de acordo com os regulamentos de conformidade da sua organização.

Observe que o registro de auditoria no plano de dados ainda não está disponível para o Cache do Azure para Redis.

Como definir parâmetros de retenção de log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Habilite as configurações de diagnóstico do Log de atividadedo Azure e envie os logs para um espaço de trabalho do Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos Dados de Registro de Atividades que podem ter sido coletados para o Azure Cache for Redis.

Observe que o registro de auditoria no plano de dados ainda não está disponível para o Cache do Azure para Redis.

Como ativar as configurações de diagnóstico para o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Como coletar e analisar os logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Você pode configurar para receber alertas com base em métricas e registros de atividades relacionados ao cache do Azure para instâncias Redis. O Azure Monitor permite configurar um alerta para enviar uma notificação por e-mail, chamar um webhook ou invocar um aplicativo azure Logic.

Embora as métricas estejam disponíveis habilitando configurações de diagnóstico, o registro de auditoria no plano de dados ainda não está disponível para o Cache do Azure para Redis.

Como configurar alertas para o Cache do Azure para Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; O Azure Cache for Redis não processa ou produz logs relacionados a malware.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável; O Azure Cache for Redis não processa ou produz registros relacionados ao DNS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: O Azure Active Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo Azure AD PowerShell para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Como obter um papel de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure AD com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O acesso do avião de controle ao Cache Azure para Redis é controlado através do Azure Active Directory (AD). O Azure AD não tem o conceito de senhas padrão. 

O acesso do avião de dados ao Azure Cache for Redis é controlado através de chaves de acesso. Essas chaves são usadas pelos clientes que se conectam ao seu cache e podem ser regeneradas a qualquer momento.

Não é recomendável que você crie senhas padrão em seu aplicativo. Em vez disso, você pode armazenar suas senhas no Azure Key Vault e, em seguida, usar o Azure Active Directory para recuperá-las.

Como regenerar o cache do Azure para as teclas de acesso Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas. Use o Azure Security Center Identity and Access Management para monitorar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, você pode usar recomendações do Azure Security Center ou políticas incorporadas do Azure, tais como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Como usar o Azure Security Center para monitorar identidade e acesso (Visualização):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Como usar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: O Azure Cache for Redis usa chaves de acesso para autenticar usuários e não suporta logon único (SSO) no nível do plano de dados. O acesso ao plano de controle do Azure Cache for Redis está disponível via API REST e suporta SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações em um Token Web JSON que você obtém do Azure Active Directory.

Entenda o Cache do Azure para a API Redis REST:https://docs.microsoft.com/rest/api/redis/

Entenda o SSO com o Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite a Autenticação Multifatorial (AD) do Azure Active Directory (AD) e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use estações de trabalho de acesso privilegiado (PAW) com Afecção multifatorial (MFA) configuradas para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use o Azure Active Directory (AD) Privileged Identity Management (PIM) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Além disso, use detecções de risco Do Azure AD para visualizar alertas e relatórios sobre comportamentos de usuários de risco.

Como implantar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Configure locais nomeados no Azure Active Directory (AD) Acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AD) como sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

A autenticação do Azure AD não pode ser usada para acesso direto ao Cache do Azure para o plano de dados redis, no entanto, as credenciais Azure AD podem ser usadas para administração no nível do plano de controle (ou seja, o portal Azure) para controlar o Cache Do Azure para chaves de acesso Redis.


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo. 

Entenda os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Você tem acesso às fontes de login do Azure Active Directory (AD), auditando e de eventos de risco, que permitem que você se integre ao Azure Sentinel ou a um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários do Azure AD e enviando os registros de auditoria e logs de login para um espaço de trabalho do Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Para o desvio de comportamento de login da conta no plano de controle, use os recursos de proteção de identidade do Azure Active Directory (AD) e de detecção de risco para configurar respostas automatizadas para detectar ações suspeitas relacionadas às identidades dos usuários. Você também pode ingerr dados no Azure Sentinel para mais investigações.

Como ver os logins arriscados do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Ainda não disponível; O Customer Lockbox ainda não tem suporte para o Azure Cache for Redis.

Lista de serviços suportados pelo Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. O Cache do Azure para instâncias Redis deve ser separado por rede/sub-rede virtual e marcado adequadamente. Opcionalmente, use o firewall Azure Cache for Redis para definir regras para que apenas conexões do cliente de intervalos de endereços IP especificados possam se conectar ao cache.

Como criar assinaturas adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como implantar o Cache Azure para Redis em um Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Como configurar o Cache do Azure para as regras de firewall redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Ainda não disponível; os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para o Azure Cache for Redis.

A Microsoft gerencia a infra-estrutura subjacente ao Azure Cache for Redis e implementou controles rigorosos para evitar a perda ou exposição de dados do cliente.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: O Cache do Azure para Redis requer comunicações criptografadas por TLS por padrão. As versões TLS 1.0, 1.1 e 1.2 são suportadas atualmente. No entanto, o TLS 1.0 e o 1.1 estão no caminho para a depreciação em todo o setor, por isso use o TLS 1.2, se possível. Se a sua biblioteca ou ferramenta do cliente não suportar o TLS, então a ativação de conexões não criptografadas pode ser feita através do portal do Azure ou das APIs de gerenciamento. Nesses casos em que conexões criptografadas não são possíveis, seria recomendado colocar seu cache e aplicativo cliente em uma rede virtual.

Entenda a criptografia em trânsito para o Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Entenda as portas necessárias usadas em cenários de cache Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Cache Do Azure para Redis. Marque instâncias contendo informações confidenciais como tal e implemente uma solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Use o Controle de Acesso baseado em função Abluee Active Directory (AAD) para controlar o acesso ao plano de controle Azure cache for Redis (ou seja, portal Azure). 

Como configurar o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

A Microsoft gerencia a infra-estrutura subjacente ao Azure Cache for Redis e implementou controles rigorosos para evitar a perda ou exposição de dados do cliente.

Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: O Cache do Azure para Redis armazena dados do cliente na memória e, embora fortemente protegido por muitos controles implementados pela Microsoft, a memória não é criptografada por padrão. Se necessário por sua organização, criptografe o conteúdo antes de armazenar no Cache Do Zure para Redis.

Se usar o recurso "Redis Data Persistence" do Azure Cache for Redis, os dados serão enviados para uma conta de armazenamento do Azure que você possui e gerencia. Você pode configurar a persistência da lâmina "New Azure Cache for Redis" durante a criação de cache e no menu Recurso para caches premium existentes.

Os dados no Azure Storage são criptografados e descriptografados de forma transparente usando criptografia AES de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2. A criptografia de armazenamento do Azure não pode ser desativada. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de sua conta de armazenamento ou gerenciar criptografia com suas próprias chaves.

Como configurar a persistência no Cache Do Azure para Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Entenda a criptografia para contas do Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Entenda a proteção de dados do cliente do Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Do Azure com o registro de atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de produção do Cache do Azure para Redis e outros recursos críticos ou relacionados.

Como criar alertas para eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre como proteger o cache do Azure para instâncias redis e recursos relacionados.

A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Cache Do Azure para Redis.

Entenda as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Cache Do Azure para Redis.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

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

**Orientação**: Aplique tags aos recursos do Azure que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use tagging, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear o Cache do Azure para instâncias redis e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais e Azure como um todo.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

**Orientação**: Use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Além disso, use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro da assinatura(s).

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais e Azure como um todo.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Use a Diretiva Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resources Manager através de scripts

**Orientação**: Configure o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Arm (Azure Resource Manager, gerente de recursos do Azure) configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o cache do Azure para instâncias Redis com a Diretiva Azure. Use alias de diretiva do Azure no namespace "Microsoft.Cache" para criar políticas personalizadas para auditar ou impor a configuração do cache do Azure para instâncias Redis. Você também pode fazer uso de definições de diretiva incorporadas relacionadas ao cache do Azure para instâncias Redis, tais como:

Apenas conexões seguras com o Cache Redis devem ser habilitadas

Como visualizar aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições personalizadas de diretiva do Azure ou modelos do Azure Resource Manager para o cache do Azure para instâncias redis e recursos relacionados, use os Repos do Azure para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.Cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.Cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a diretiva do Azure [auditoria], [negue], e [implantar se não existir] para aplicar automaticamente configurações para o cache do Azure para instâncias redis e recursos relacionados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Para que máquinas virtuais do Azure ou aplicativos da Web em execução no Azure App Service sejam usados para acessar seu Cache Azure para instâncias Redis, use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o Cache Do Azure para gerenciamento secreto Redis. Certifique-se de que a exclusão suave do Key Vault está ativada.

Como se integrar com as identidades gerenciadas do Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Para que máquinas virtuais do Azure ou aplicativos da Web em execução no Azure App Service sejam usados para acessar seu Cache Azure para instâncias Redis, use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o Cache Do Azure para gerenciamento secreto Redis. Certifique-se de que o Key Vault Soft Delete esteja ativado.

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. As Identidades Gerenciadas permitem que você se autentique em qualquer serviço que suporte a autenticação AAD, incluindo o Azure Key Vault, sem quaisquer credenciais em seu código.

Como configurar identidades gerenciadas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como se integrar com as identidades gerenciadas do Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o Scanner de Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Use software anti-malware gerenciado centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

O anti-malware da Microsoft está habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Azure App Service), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Cache Do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie qualquer conteúdo que seja carregado para recursos não computados do Azure, como Serviço de Aplicativo, Armazenamento de Data Lake, Armazenamento Blob, Banco de Dados Azure para PostgreSQL, etc. A Microsoft não pode acessar seus dados nessas instâncias.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

O anti-malware da Microsoft está habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Cache Azure for Redis), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Habilite a persistência redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar o Cache Azure para exportação Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor Azure cache for Redis, e o arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Como ativar a persistência do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Como usar o Cache Do Azure para exportação redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: Habilite a persistência redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar o Cache Azure para exportação Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor Azure cache for Redis, e o arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Se usar o Azure Key Vault para armazenar credenciais para o cache do Azure para instâncias Redis, certifique backups automatizados regulares de suas chaves.

Como ativar a persistência do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Como usar o Cache Do Azure para exportação redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Como fazer backup das chaves do cofre:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Use o cache azure para importação redis. A importação pode ser usada para trazer arquivos RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

Teste periodicamente a restauração de dados de seus segredos do Azure Key Vault.

Como usar o Cache Do Azure para importação redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Como restaurar os segredos do Cofre chave:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Os backups do Azure Cache para Redis da Redis Export e da persistência redis são armazenados em sua conta selecionada do Azure Storage. Os dados no Azure Storage são criptografados e descriptografados de forma transparente usando criptografia AES de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2. A criptografia de armazenamento do Azure não pode ser desativada. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de sua conta de armazenamento ou gerenciar criptografia com suas próprias chaves.

Entenda a criptografia para contas do Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Microsoft

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança de Computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marca claramente assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta de segurança) descobrir que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.

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

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas e recomendações de segurança.

Como configurar aplicativos de automação e lógica de fluxo de trabalho:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta a remediação de todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia e execução do Red Teaming e testes de penetração de sites ao vivo contra infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
