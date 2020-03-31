---
title: Linha de base de segurança do Azure para explorador de dados
description: Linha de base de segurança do Azure para explorador de dados
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289713"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Linha de base de segurança do Azure para explorador de dados

A linha de base de segurança do Azure para o Data Explorer contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: O Azure Data Explorer suporta a implantação de um cluster em uma sub-rede em sua rede virtual. Esse recurso permite que você imponha as regras do Grupo de Segurança de Rede (NSG) no tráfego de cluster do Azure Data Explorer, conecte sua rede local à sub-rede do cluster Azure Data Explorer e proteja suas fontes de conexão de dados (Event Hub e Event Grid) com o Cluster De dados com o pontos finais de serviço.

Como implantar seu cluster Azure Data Explorer em uma rede virtual:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICS

**Orientação**: Habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) e envie logs para uma conta de armazenamento para auditoria de tráfego.

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Entendendo a segurança da rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos críticos da Web

**Orientação**: Não aplicável; A recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite o Padrão de Proteção DDoS do Azure na rede virtual protegendo seus clusters do Data Explorer para proteção contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Habilite os logs de fluxo nos grupos de segurança de rede (NSG) que estão sendo usados para proteger o cluster Do Azure Data Explorer e enviar logs para uma conta de armazenamento para auditoria de tráfego.

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede

**Orientação**: Não aplicável; Este controle é feito no ponto final ou firewall.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerencie o tráfego para seus aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Use tags de serviço de rede virtual para definir controles de acesso à rede em Grupos de Segurança de Rede ou Firewalls Azure associados aos clusters do Azure Data Explorer. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Entendendo e usando tags de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Requisitos de configuração de tags de serviço para o Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: O cliente definir e implementar configurações padrão de segurança para recursos de rede com a Diretiva Azure.

O cliente também pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos ARM, controles RBAC e políticas, em uma definição de projeto único. Aplique facilmente o projeto a novas assinaturas e ambientes, e ajuste o controle e o gerenciamento por meio de versionamentos.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para grupos de segurança de rede (NSG) e outros recursos relacionados à segurança da rede e fluxo de tráfego para seus clusters do Data Explorer. Para regras individuais do NSG, use o campo "Descrição" para especificar a necessidade e/ou duração dos negócios (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use a diretiva Azure para validar (e/ou remediar) a configuração para recursos de rede.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos do Azure, os clientes podem atualizar a sincronização de tempo para implantações de computação de propriedade do cliente.

Como configurar a sincronização de tempo para os recursos de computação do Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento do registro de segurança central

**Orientação**: O Azure Data Explorer usa registros de diagnóstico para obter informações sobre sucessos e falhas de ingestão. Você pode exportar logs de operação para o Azure Storage, Event Hub ou Log Analytics para monitorar o status de ingestão.

Como monitorar as operações de ingestão do Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Como ingerir e consultar dados de monitoramento no Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico do Azure Data Explorer para acesso e registro para operações específicas de serviço e registro. Os logs de atividade do Azure no Azure Monitor, que inclui o registro de alto nível sobre o recurso, são habilitados por padrão.

Como monitorar as operações de ingestão do Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Como coletar logs e métricas da plataforma com o Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Visão geral dos logs da plataforma Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Coletar registros de segurança do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: No Monitor do Azure, defina o período de retenção do Espaço de Trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registros de monitores e avaliações

**Orientação**: Analisar e monitorar registros para comportamentos anômalos e revisar regularmente os resultados. Depois de habilitar as configurações de diagnóstico para o Azure Data Explorer, use o Log Analytics Workspace do Azure Monitor para revisar logs e executar consultas em dados de log.

Entendendo o espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Não aplicável; O Azure Data Explorer não tem essa capacidade.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; O Azure Data Explorer não processa o registro anti-malware.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação:** Não aplicável: a consulta DNS não é uma função do Azure Data Explorer.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter inventário de contas administrativas

**Orientação**: No Azure Data Explorer, as funções de segurança definem quais princípios de segurança (usuários e aplicativos) têm permissões para operar em um recurso seguro, como um banco de dados ou uma tabela, e quais operações são permitidas.  Você pode aproveitar a consulta do Kusto para listar princípios na função de administrador para os clusters e bancos de dados do Azure Data Explorer.
Gerenciamento de funções de segurança no Azure Data Explorer usando consulta Kusto:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, o que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços de marketplace que podem usar senhas padrão.


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Garantir o uso de contas administrativas dedicadas

**Orientação**: O cliente para criar procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas. Use o Azure Security Center Identity and Access Management para monitorar o número de contas administrativas.

Os clientes também podem habilitar um Just-In-Time / Just-Enough-Access usando funções privilegiadas de gerenciamento de identidade privilegiada do Azure AD para serviços Microsoft e ARM do Azure. 

O que é o Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize o Single Sign-On (SSO) com o Azure Active Directory

**Orientação**: Sempre que possível, o cliente deve usar o SSO com o Azure Active Directory (Azure AD) em vez de configurar credenciais individuais por serviço. Use as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Entendendo o SSO com o Ad do Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use a autenticação multifatorial para todos os acessos baseados no Azure Active Directory.

**Orientação**: Habilite a autenticação multifatorial do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Uso de máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho de acesso privilegiado) com autenticação multifatorial (MFA) configurada para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Log e Alerta sobre Atividades Suspeitas em Contas Administrativas

**Orientação**: Use os relatórios de segurança do Azure Active Directory (Azure AD) para geração de registros e alertas quando ocorre atividade suspeita ou insegura no ambiente. Use o Azure Security Center para monitorar a atividade de identidade e acesso

Como identificar usuários do Azure AD sinalizados para atividades de risco:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerenciar o recurso do Azure a partir de apenas locais aprovados

**Orientação**: O cliente deve usar locais nomeados pelo Acesso Condicional para permitir o acesso apenas de agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-utilize-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo do Azure

**Orientação**: O Azure Active Directory (Azure AD) é o método preferido para autenticação do Azure Data Explorer. Ele é compatível com vários cenários de autenticação:

Autenticação do usuário (logon interativo): Usado para autenticar princípios humanos.

Autenticação de aplicativos (logon não interativo): Usado para autenticar serviços e aplicativos que têm que executar/autenticar sem a presença de um usuário humano.

Visão geral do controle de acesso do Azure Data Explorer:https://docs.microsoft.com/azure/kusto/management/access-control

Autenticação com o Diretório Ativo do Azure:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo. 

Como autenticar com o Azure AD para acesso ao Azure Data Explorer:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Relatório sadia do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Você pode usar o Azure Active Directory (Azure AD) Sign in Activity, Audit and Risk Event log sources for monitoring, que permite que você se integre a qualquer ferramenta de gerenciamento de informações e eventos de segurança (SIEM) / Monitoramento.

 Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários do Azure Active Directory, enviando os logs de auditoria e logs de login para um Espaço de Trabalho do Log Analytics. Cliente para configurar os alertas desejados no Espaço de Trabalho do Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use o recurso Detecções de Risco e Proteção de Identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas às identidades dos usuários. Além disso, você pode ingerr dados no Azure Sentinel para mais investigações.

Como ver os logins arriscados do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Em cenários de suporte onde a Microsoft precisa acessar dados de clientes, o Customer Lockbox fornece uma interface para que os clientes revisem e aprovem ou rejeitem solicitações de acesso a dados de clientes.

Entendendo o Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoramento do Azure Security Center**: Atualmente não disponível

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

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os clusters do Azure Data Explorer devem ser separados de outros recursos por rede/sub-rede virtual, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou do Azure Firewall. Os clusters do Data Explorer que armazenam ou processam dados confidenciais devem ser suficientemente isolados.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como implantar seu cluster Azure Data Explorer em uma rede virtual:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Como criar um NSG com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Não aplicável; Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Orientação**: O cluster Azure Data Explorer negocia o TLS 1.2 por padrão. Certifique-se de que todos os clientes que se conectam aos seus recursos do Azure sejam capazes de negociar TLS 1.2 ou superior.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Use uma ferramenta de detecção ativa para identificar dados confidenciais</div>

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Data Explorer. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Use o Azure RBAC para controlar o acesso aos recursos</div>

**Orientação**: O Azure Data Explorer permite controlar o acesso a bancos de dados e tabelas, usando um modelo rbac (role-based access control). Nesse modelo, as entidades de segurança (usuários, grupos e aplicativos) são mapeadas para funções. As entidades de segurança podem acessar os recursos de acordo com as funções atribuídas.

Lista de funções e permissões e instruções sobre como configurar o RBAC para o Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a Prevenção de Perda de Dados baseada em host para reforçar o controle de acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

A Microsoft gerencia a infra-estrutura subjacente ao Azure Data Explorer e implementou controles rigorosos para evitar a perda ou exposição de dados de clientes.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Orientação**: A Criptografia de Disco do Azure ajuda a proteger e proteger seus dados para atender aos seus compromissos de segurança organizacional e conformidade. Ele fornece criptografia de volume para o Sistema Operacional e discos de dados de suas máquinas virtuais de cluster. Ele também se integra ao Azure Key Vault, que nos permite controlar e gerenciar as chaves e segredos de criptografia de disco, e garantir que todos os dados nos discos VM sejam criptografados em repouso enquanto estiver no Azure Storage.

Como ativar a criptografia em repouso para clusters do Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Azure com o Registro de Atividades do Azure para criar alertas para quando as alterações no nível de recursos ocorrerem nos clusters do Azure Data Explorer.

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre como proteger os recursos do Azure Data Explorer.

A Microsoft também executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Azure Data Explorer.

Entenda as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare varreduras de vulnerabilidades back-to-back

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizar um processo de classificação de risco para priorizar a remediação das vulnerabilidades descobertas.

**Orientação**: Use as classificações de risco padrão (Secure Score) fornecidas pelo Azure Security Center.
Entenda a pontuação segura do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Utilizar a descoberta de ativos do Azure

**Orientação**: Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos etc.) dentro de sua assinatura(s). Garanta permissões apropriadas (leia) em seu inquilino e enumeratodas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags aos recursos do Azure que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Você pode usar convenções de nomeação apropriadas, marcação, grupos de gerenciamento ou assinaturas separadas, quando apropriado, para organizar e rastrear ativos. Você pode usar o Azure Resource Graph para conciliar o inventário regularmente e garantir que os recursos não autorizados sejam excluídos da assinatura em tempo hábil. 

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Você precisará criar um inventário de recursos azure aprovados e software aprovado para recursos de computação de acordo com suas necessidades organizacionais.  


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos azure não aprovados

**Orientação**: Você pode usar as políticas do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

    - Tipos de recursos não permitidos

    - Tipos de recursos permitidos

Você será capaz de monitorar os eventos gerados pela política usando o 

Registros de atividades que podem ser monitorados usando o Monitor Azure.

Além disso, você pode usar o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro da assinatura(s).

Tutorial: Crie e gerencie políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Partida rápida: Execute sua primeira consulta de gráfico de recursos usando o Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Criar, visualizar e gerenciar alertas de registro de atividades usando o Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro de recursos de computação

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software do Azure não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais e Azure como um todo.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-utilize-only-approved-applications"></a>6.8: Utilize apenas aplicativos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Utilize apenas serviços azure aprovados

**Orientação**: Você pode usar as políticas do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

    - Tipos de recursos não permitidos

    - Tipos de recursos permitidos

Tutorial: Crie e gerencie políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resources Manager através de scripts

**Orientação**: Use o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management". Isso impedirá a criação e alterações nos recursos dentro de suas assinaturas do Azure. 

Gerenciar o acesso ao gerenciamento do Azure com acesso condicional:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro dos recursos de computação

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

**Orientação**: Use aliases de diretiva do Azure para criar políticas personalizadas para auditar ou impor a configuração de seus recursos do Azure. Você também pode usar definições de diretiva azure incorporadas.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam / excedam os requisitos de segurança para sua organização.

Você também pode usar recomendações do Azure Security Center como uma linha de base de configuração segura para seus recursos do Azure.

Como visualizar aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Crie e gerencie políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exportação única e multi-recursos para um modelo no portal Azure:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Recomendações de segurança - um guia de referência:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabeleça configurações seguras para o seu sistema operacional

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.  Você pode usar soluções como rastreamento de alterações, painel de conformidade de políticas ou uma solução personalizada para identificar facilmente mudanças de segurança em seu ambiente.

Entenda os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Criar e gerenciar políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Acompanhe as alterações no ambiente com a solução De rastreamento de alterações:https://docs.microsoft.com/azure/automation/change-tracking

Obtenha dados de conformidade dos recursos do Azure:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operacionais

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Use os Repos do Azure para armazenar e gerenciar seu código com segurança, como políticas personalizadas do Azure, modelos do Azure Resource Manager, scripts de configuração de estado desejados etc. Para acessar os recursos gerenciados no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança incorporados ou grupos definidos no Azure Active Directory (Azure AD) se integrado com OZure DevOps ou Active Directory se integrado ao TFS.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Sobre permissões e grupos no Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos do Azure usando a Diretiva Azure. Use aliases de diretiva do Azure para criar políticas personalizadas para auditar ou impor a configuração de rede de seus recursos do Azure. Você também pode fazer uso de definições de políticas incorporadas relacionadas aos seus recursos específicos.  Além disso, você pode usar o Azure Automation para implantar alterações de configuração.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como usar aliases:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use aliases de diretiva do Azure para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negue], e [implantar se não existir] para aplicar automaticamente configurações para seus recursos do Azure.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerencie com segurança os segredos do Azure

**Orientação**: O Azure Disk Encryption fornece criptografia de volume para o sistema operacional e discos de dados de suas máquinas virtuais de cluster Azure Data Explorer. Ele também se integra ao Azure Key Vault, que permite controlar e gerenciar as chaves e segredos de criptografia de disco, e garantir que todos os dados nos discos VM sejam criptografados em repouso enquanto estiver no Azure Storage.

Como proteger seu cluster no Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código. Como configurar identidades gerenciadas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Configure identidades gerenciadas para o cluster Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner de Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerenciado centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

O anti-malware da Microsoft está habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Azure Data Explorer), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Azure Data Explorer), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie qualquer conteúdo que seja carregado para recursos não computados do Azure, como Azure Data Explorer, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode acessar seus dados nessas instâncias.



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

O anti-malware da Microsoft está habilitado no host subjacente que suporta serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Orientação**: Os dados em sua conta de armazenamento Microsoft Azure usado pelo cluster Data Explorer são sempre replicados para garantir durabilidade e alta disponibilidade. A redundância do Armazenamento do Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região ou entre regiões separadas geograficamente.

Entendendo a redundância de armazenamento do Azure e os contratos de nível de serviço:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Exportar dados para armazenamento:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Realize backups completos do sistema e faça backup de quaisquer chaves gerenciadas pelo cliente

**Orientação**: O Azure Data Explorer criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados. As chaves gerenciadas pelo cliente devem ser armazenadas em um Cofre de Chaves Azure. 

Configure as chaves gerenciadas pelo cliente usando C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Como fazer backup dos certificados do Azure Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Teste periodicamente a restauração de dados de seus segredos do Azure Key Vault.

Como restaurar os certificados do Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Configure as chaves gerenciadas pelo cliente usando C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: O cliente habilitar o Soft-Delete no Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.  Você também pode habilitar a proteção contra expurgo para que, se um cofre ou um objeto no estado excluído, ele não possa ser purgado até que o período de retenção tenha passado.  

Como ativar a proteção Soft-Delete e Expurgo no Cofre de Chaves:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Configure as chaves gerenciadas pelo cliente usando C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realize exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornecer detalhes de contato &nbsp;com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada. Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.
    

Como definir o contato de segurança do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Azure Security Center usando o recurso Exportação Contínua para ajudar a identificar riscos aos recursos do Azure. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas para o Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas de segurança e recomendações para proteger seus recursos do Azure.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta remediar todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Siga as regras de engajamento da Microsoft para garantir https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1que seus testes de penetração não estejam violando as políticas da Microsoft: .

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução do Red Teaming e testes de penetração de sites ao vivo contra a infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
