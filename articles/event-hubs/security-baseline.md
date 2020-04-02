---
title: Linha de base de segurança do Azure para hubs de eventos
description: Linha de base de segurança do Azure para hubs de eventos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549038"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de base de segurança do Azure para hubs de eventos

A linha de base de segurança do Azure para hubs de eventos contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: A integração dos hubs de eventos com os pontos finais de serviço de rede virtual permite acesso seguro aos recursos de mensagens a partir de cargas de trabalho, como máquinas virtuais vinculadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez vinculado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respectivo namespace Event Hubs não aceita mais tráfego de qualquer lugar, mas sub-redes autorizadas em redes virtuais. Do ponto de vista da rede virtual, vincular o namespace do Event Hubs a um ponto final de serviço configura um túnel de rede isolado da sub-rede virtual ao serviço de mensagens. 

Você também pode criar um ponto final privado, que é uma interface de rede que o conecta de forma privada e segura ao serviço Azure Event Hubs usando o serviço Azure Private Link. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. 

Você também pode proteger seu namespace do Azure Event Hubs usando firewalls. O Azure Event Hubs suporta controles de acesso baseados em IP para suporte a firewall de entrada. Você pode definir regras de firewall usando o portal Azure, os modelos do Azure Resource Manager ou através do Azure CLI ou Do Azure PowerShell.

Como usar os pontos finais de serviço de rede virtual com o Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Para obter mais informações, consulte Integrar Hubs de Eventos https://docs.microsoft.com/azure/event-hubs/private-link-serviceDo Azure com o Azure Private Link: .

Habilite a integração de redes virtuais e firewalls no namespace do Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Como configurar regras de firewall IP para namespaces do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e siga as recomendações de proteção de rede para ajudar a proteger os recursos do Event Hubs no Azure. Se usar as máquinas virtuais do Azure para acessar seus hubs de eventos, habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) e envie logs para uma conta de armazenamento para auditoria de tráfego.

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Entendendo a segurança da rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite o Padrão de Proteção DDoS nas redes virtuais associadas aos seus hubs de eventos para se proteger contra ataques de negação de serviço distribuídos (DDoS). Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar a proteção DDoS:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Para obter mais informações sobre o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Se usar máquinas virtuais do Azure para acessar seus hubs de eventos, habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) e envie logs em uma conta de armazenamento para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Se necessário para investigar atividades anômalas, habilite a captura de pacotes do Network Watcher.

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Como ativar o Observador de Rede:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se usar máquinas virtuais do Azure para acessar seus hubs de eventos, selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com recursos de inspeção de carga. Se a detecção e/ou prevenção de intrusões com base na inspeção de carga não for necessária para sua organização, você poderá usar o recurso de firewall incorporado do Azure Event Hubs. Você pode limitar o acesso ao namespace do Event Hubs para uma gama limitada de endereços IP ou um endereço IP específico usando regras do Firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Como adicionar uma regra de firewall em Hubs de eventos para um endereço IP especificado:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoramento do Azure Security Center**: Ainda não está disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Não aplicável, esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus namespaces do Azure Event Hubs com a Política Do Azure. Use aliases de diretiva do Azure nos namespaces "Microsoft.EventHub" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos namespaces do Event Hubs. Você também pode fazer uso de definições de políticas incorporadas relacionadas aos Hubs de Eventos do Azure, tais como:

- O Event Hub deve usar um ponto final de serviço de rede virtual.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Política incorporada do Azure para namespace do Event Hubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Amostras de política do Azure para rede:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para redes virtuais e outros recursos relacionados à segurança da rede e fluxo de tráfego associados aos seus hubs de eventos.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar as configurações de recursos da rede e detectar alterações nos recursos de rede relacionados aos Hubs de Eventos do Azure. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo usada para os recursos do Azure, como o Azure Event Hubs, para carimbos de tempo nos registros.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: No Azure Monitor, configure logs relacionados a hubs de eventos dentro das configurações de diagnóstico do Activity Log e event Hub para enviar logs em um espaço de trabalho do Log Analytics para serem consultados ou em uma conta de armazenamento de armazenamento de arquivamento a longo prazo.

Como configurar configurações de diagnóstico para hubs de eventos do Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Entendendo o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico para o espaço de nome do Azure Event Hubs. Existem três categorias de configurações de diagnóstico para hubs de eventos do Azure: Logs de arquivamento, logs operacionais e logs de autoescala. Habilite os Logs Operacionais para capturar informações sobre o que está acontecendo durante as operações do Event Hubs, especificamente, o tipo de operação, incluindo a criação de hubs de eventos, os recursos usados e o status da operação.

Além disso, você pode habilitar as configurações de diagnóstico do log de atividade do Azure e enviá-las para uma conta de armazenamento do Azure, um hub de eventos ou um espaço de trabalho do Log Analytics. Os registros de atividades fornecem informações sobre as operações realizadas em seus Hubs de Eventos Do Azure e outros recursos. Usando registros de atividades, você pode determinar o "o que, quem e quando" para quaisquer operações de gravação (PUT, POST, DELETE) tomadas em seus namespaces do Azure Event Hubs.

Como ativar as configurações de diagnóstico para hubs de eventos do Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como ativar as configurações de diagnóstico para o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: No Monitor do Azure, defina o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e revisar incidentes relacionados ao hub de eventos.

Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Analise e monitore registros para comportamento anômalo e revise regularmente os resultados relacionados aos seus hubs de eventos. Use o Log Analytics do Azure Monitor para revisar logs e executar consultas em dados de log. Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros.
 

Para obter mais informações sobre o espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: No Monitor do Azure, configure logs relacionados aos Hubs de Eventos do Azure dentro do Registro de Atividades e das configurações de diagnóstico do Event Hubs para enviar logs em um espaço de trabalho do Log Analytics para serem consultados ou em uma conta de armazenamento de arquivamento de longo prazo. Use o espaço de trabalho do Log Analytics para criar alertas para atividades anômalas encontradas em registros de segurança e eventos.

Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel. 

Entenda o Registro de Atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Como configurar configurações de diagnóstico para hubs de eventos do Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como alertar sobre os dados de log do log do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Ainda não está disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; O Event Hub não processa o registro anti-malware.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável; O Event Hubs não processa ou produz registros relacionados ao DNS.

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

**Orientação**: O acesso do avião de controle aos Hubs de Eventos é controlado através do Azure Active Directory (AD). O Azure AD não tem o conceito de senhas padrão.

O acesso de data plane aos Event Hubs é controlado através do Azure AD com identidades gerenciadas ou registros de aplicativos, bem como assinaturas de acesso compartilhado. As assinaturas de acesso compartilhado são usadas pelos clientes que se conectam aos seus hubs de eventos e podem ser regeneradas a qualquer momento.

Entenda as assinaturas de acesso compartilhadas para hubs de eventos:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

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

**Orientação**: O Microsoft Azure fornece gerenciamento integrado de controle de acesso para recursos e aplicativos com base no Azure Active Directory (AD). Uma vantagem fundamental de usar o Azure AD com o Azure Event Hubs é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2.0 da plataforma Microsoft Identity. O nome do recurso para https://eventhubs.azure.net/solicitar um token é . O Azure AD autentica o principal de segurança (um usuário, grupo ou diretor de serviço) executando o aplicativo. Se a autenticação for bem-sucedida, o Azure AD retorna um token de acesso ao aplicativo e, em seguida, o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos do Azure Event Hubs.

Como autenticar um aplicativo com o Azure AD para acessar os recursos do Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Entendendo o SSO com o Ad do Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite o MFA (Azure Active Directory Multi-FactorAuthentication, autenticação de vários fatores) do Azure Active Directory e siga as recomendações de gerenciamento de acesso e identidade do Centro de Segurança do Azure para ajudar a proteger os recursos habilitados para o Hub de Eventos.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use estações de trabalho de acesso privilegiado (PAW) com MFA (Multi-Factor Authentication, autenticação multifatorial) configuradas para fazer login e configurar recursos habilitados para o Event Hub.

Saiba mais sobre estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use o Azure Active Directory (AD) Privileged Identity Management (PIM) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente. Use detecções de risco Azure AD para visualizar alertas e relatórios sobre comportamentos de usuários de risco. Para registro adicional, envie alertas de detecção de risco do Azure Security Center para o Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

Como implantar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Como configurar grupos de ação para alertae notificação personalizada:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Use locais nomeados de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.



Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AD) como o sistema central de autenticação e autorização para recursos do Azure, como hubs de eventos. Isso permite que o Controle de Acesso Baseado em Papéis (RBAC) a recursos confidenciais administrativos.

 Como criar e configurar uma instância AD do Azure:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Para saber como o Azure Event Hubs se integra ao AAD (AAD), consulte Autorizar o acesso aos recursos do Event Hubs usando o Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo.

Além disso, gire regularmente as assinaturas de acesso compartilhadas do Event Hubs.

Entenda os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Entendendo assinaturas de acesso compartilhadas para Hubs de Eventos:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Você tem acesso à atividade de login do Azure Active Directory (AD), às fontes de registro de eventos de auditoria e risco, que permitem que você se integre a qualquer ferramenta De siem/monitoramento.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários do Azure AD e enviando os registros de auditoria e logs de login para um espaço de trabalho do Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use os recursos de proteção de identidade e detecção de risco do Azure Active Directory para configurar respostas automatizadas a ações suspeitas detectadas relacionadas aos recursos habilitados para o Event Hubs. Você deve habilitar respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como ver os logins arriscados do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Atualmente não disponível; O Customer Lockbox ainda não tem suporte para hubs de eventos.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags em recursos relacionados aos seus Hubs de Eventos para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os namespaces do Event Hubs devem ser separados por rede virtual com pontos finais de serviço ativados e marcados adequadamente.

Você também pode proteger seu namespace do Azure Event Hubs usando firewalls. O Azure Event Hubs suporta controles de acesso baseados em IP para suporte a firewall de entrada. Você pode definir regras de firewall usando o portal Azure, os modelos do Azure Resource Manager ou através do Azure CLI ou Do Azure PowerShell.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Configure as regras de firewall IP para os namespaces do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Como criar e utilizar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Ao usar máquinas virtuais para acessar seus hubs de eventos, faça uso de redes virtuais, pontos finais de serviço, firewall do Event Hubs, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gerencia a infra-estrutura subjacente para o Azure Event Hubs e implementou controles rigorosos para evitar a perda ou exposição de dados de clientes.

Configure as regras de firewall IP para os namespaces do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Entenda os pontos finais do serviço de rede virtual com hubs de eventos do Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integre hubs de eventos do Azure com o Azure Private Link:https://docs.microsoft.com/azure/event-hubs/private-link-service

Entenda grupos de segurança de rede e tags de serviço:https://docs.microsoft.com/azure/virtual-network/security-overview

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: O Azure Event Hubs impõe as comunicações criptografadas por TLS por padrão. As versões TLS 1.0, 1.1 e 1.2 são suportadas atualmente. No entanto, o TLS 1.0 e o 1.1 estão no caminho para a depreciação em todo o setor, por isso use o TLS 1.2, se possível.

Para entender os recursos de segurança dos Hubs de eventos, consulte segurança da rede:https://docs.microsoft.com/azure/event-hubs/network-security

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Event Hubs. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Azure Event Hubs suporta o uso do Azure Active Directory (AD) para autorizar solicitações aos recursos do Event Hubs. Com o Azure AD, você pode usar o RBAC (Role-Based Access Control, controle de acesso baseado em função) para conceder permissões a um diretor de segurança, que pode ser um usuário ou um diretor de serviço de aplicativo.

Entenda o Azure AD RBAC e as funções disponíveis para o Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

A Microsoft gerencia a infra-estrutura subjacente para hubs de eventos e implementou controles rigorosos para evitar a perda ou exposição de dados de clientes.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: O Azure Event Hubs suporta a opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente. Esse recurso permite que você crie, gire, desative e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar dados do Azure Event Hubs em repouso.

Como configurar chaves gerenciadas pelo cliente para criptografar hubs de eventos do Azure:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Do Azure com o registro de atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de produção dos Hubs de Eventos Do Azure e outros recursos críticos ou relacionados.

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam hubs de eventos.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Não aplicável; A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que suportam hubs de eventos.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Não aplicável; benchmark destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam hubs de eventos.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam hubs de eventos.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar e descobrir todos os recursos (incluindo os namespaces do Azure Event Hubs) em sua assinatura(s). Certifique-se de ter permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Orientação**: Use tagging, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear os namespaces do Azure Event Hubs e os recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

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

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro da assinatura(s).

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Orientação**: Use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com o Azure Resource Manager através de scripts</div>

**Orientação**: Configure o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Orientação**: Defina e implemente configurações de segurança padrão para as implantações do Azure Event Hubs. Use alias de diretiva do Azure no namespace "Microsoft.EventHub" para criar políticas personalizadas para auditar ou impor configurações. Você também pode fazer uso de definições de políticas incorporadas para hubs de eventos do Azure, tais como:

- Os logs de diagnóstico no Hub de eventos devem ser habilitados

- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual

Política incorporada do Azure para namespace do Event Hubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Como visualizar aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos habilitados para Hubs de eventos. 

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Para obter mais informações sobre os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições personalizadas de diretiva do Azure para seus Hubs de Eventos ou recursos relacionados, use os Repos do Azure para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negue], e [implantar se não existir] para aplicar automaticamente configurações para suas implantações do Azure Event Hubs e recursos relacionados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Para que máquinas virtuais do Azure ou aplicativos web em execução no Azure App Service sejam usados para acessar seus hubs de eventos, use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento de assinaturas de acesso compartilhado para suas implantações do Azure Event Hubs. Certifique-se de que o soft-delete do Key Vault está ativado.

Autenticar uma identidade gerenciada com o Azure Active Directory para acessar os recursos do Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configure as chaves gerenciadas pelo cliente para hubs de eventos:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como se integrar com as identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Para máquinas virtuais do Azure ou aplicativos web em execução no Azure App Service sendo usados para acessar seus hubs de eventos, use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger os Hubs de Eventos Do Azure. Certifique-se de que o soft-delete do Key Vault está ativado.

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD). As Identidades Gerenciadas permitem que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo o Azure Key Vault, sem quaisquer credenciais em seu código.

Autenticar uma identidade gerenciada com o Azure Active Directory para acessar os recursos do Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configure as chaves gerenciadas pelo cliente para hubs de eventos:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como configurar identidades gerenciadas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como se integrar com as identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Orientação**: Pré-scanear qualquer conteúdo que está sendo carregado para recursos não computados do Azure, como Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode acessar seus dados nessas instâncias.

O anti-malware da Microsoft está habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Cache Azure for Redis), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Configure a recuperação de desastres geográficos para hubs de eventos do Azure. Quando datacenters ou regiões inteiras do Azure (se nenhuma zona de disponibilidade for usada) enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Como tal, a recuperação geo-desastre e a georeplicação são características importantes para qualquer empresa. Os Hubs de Eventos do Azure dão suporte à recuperação de desastre de área geográfica e à replicação geográfica no nível do namespace. 

Entenda a recuperação de desastres geográficos para o Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: O Azure Event Hubs fornece criptografia de dados em repouso com o Azure Storage Service Encryption (Azure SSE). O Event Hubs conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados armazenados com o Azure Storage são criptografados usando chaves gerenciadas pela Microsoft. Se você usar o Azure Key Vault para armazenar chaves gerenciadas pelo cliente, certifique-se de backups automatizados regulares de suas chaves.

Garanta backups automatizados regulares de seus segredos do Cofre chave com o seguinte comando PowerShell: Backup-AzKeyVaultSecret

Como configurar chaves gerenciadas pelo cliente para criptografar dados do Azure Event Hubs em repouso:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Como fazer backup dos segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Teste a restauração das chaves gerenciadas pelo cliente com backup.

 

Como restaurar as chaves do cofre no Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Habilite a exclusão suave no Cofre de Chaves para proteger as chaves contra exclusão acidental ou maliciosa. O Azure Event Hubs requer que as chaves gerenciadas pelo cliente tenham o Soft Delete e o Não Purgar configurados.

Configure a exclusão suave para a conta de armazenamento Azure que é usada para capturar dados do Event Hubs. Observe que esse recurso ainda não é suportado para o Azure Data Lake Storage Gen 2.

Como ativar a exclusão suave no Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Configure um cofre de chaves com chaves:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Exclusão suave para blobs de armazenamento Azure:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Certifique-se de que existam planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gerenciamento de incidentes.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Security Center atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você atende a cada alerta, para que quando um recurso estiver comprometido, você possa chegar a ele imediatamente. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

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

**Monitoramento do Azure Security Center**: Sim

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

**Orientação**: Siga as regras de engajamento da Microsoft para garantir https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1que seus testes de penetração não estejam violando as políticas da Microsoft: .
Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução do Red Teaming e testes de penetração de sites ao vivo contra a infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
