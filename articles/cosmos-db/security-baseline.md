---
title: Linha de base de segurança do Azure para Cosmos DB
description: Linha de base de segurança do Azure para Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7e927b398bd95160e02ee915eb98ea3cb78d68fe
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758697"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Linha de base de segurança do Azure para Cosmos DB

A linha de base de segurança do Azure para Cosmos DB contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Usando o Azure Private Link, você pode se conectar a uma conta do Azure Cosmos através de um Ponto Final Privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode reduzir o risco de exfiltração de dados configurando um conjunto rigoroso de regras de saída em um grupo de segurança de rede (NSG) e associando esse NSG com sua sub-rede.

Você também pode usar pontos finais de serviço para proteger sua conta do Azure Cosmos. Ao ativar um Ponto Final de Serviço, você pode configurar contas do Azure Cosmos para permitir o acesso a partir de apenas uma sub-rede específica de uma rede virtual Do Azure. Uma vez que o Ponto Final de Serviço do Azure Cosmos DB esteja ativado, você pode limitar o acesso a uma conta do Azure Cosmos com conexões de uma sub-rede em uma rede virtual.

Você também pode proteger os dados armazenados em sua conta do Azure Cosmos usando firewalls IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta do Azure Cosmos usando o portal Azure, modelos do Azure Resource Manager ou através do Azure CLI ou Do Azure PowerShell.

Visão geral do Azure Private Link:https://docs.microsoft.com/azure/private-link/private-link-overview

Como configurar um Ponto Final Privado para O Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Como criar um grupo de segurança de rede com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como configurar firewall IP no Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede relacionados à sua conta do Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que sua conta Do Azure Cosmos, você pode usar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Habilite os registros de fluxo do NSG e envie logs em uma conta de armazenamento do Azure para auditorias de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Entenda a segurança de rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Use o recurso Cors (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada) para permitir que um aplicativo web em execução em um domínio acesse recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

Configurar o compartilhamento de recursos cross-origin:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Use o Advanced Threat Protection (ATP) para o Azure Cosmos DB . O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você aborde ameaças e as integre a sistemas centrais de monitoramento de segurança.

Habilite o Padrão de Proteção DDoS nas Redes Virtuais associadas às instâncias do Azure Cosmos DB para se proteger contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar o Azure Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Habilite os registros de fluxo do Grupo de Segurança de Rede (NSG) e envie logs para uma conta de armazenamento para auditoria de tráfego. Você pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e usar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Use o Advanced Threat Protection (ATP) para o Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você aborde ameaças e as integre a sistemas centrais de monitoramento de segurança. 

Como configurar o Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; a recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que precisam de acesso à sua conta do Azure Cosmos, use tags de serviço da Rede Virtual para definir controles de acesso à rede em networksecuritygGroups ou Azure Firewall. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Para obter mais informações sobre o uso de tags de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede com a Diretiva Azure. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.DocumentDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do Azure Cosmos DB. Você também pode fazer uso de definições de políticas incorporadas para o Azure Cosmos DB, tais como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

Você também pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos do Azure Resource Manager, RBAC (Role-Based Access Control, controle de acesso baseado em função) e políticas em uma definição de projeto único. Você pode aplicar facilmente o projeto a novas assinaturas, ambientes e controle e gerenciamento de ajuste fino através de versões.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para recursos de rede associados à sua implantação do Azure Cosmos DB, a fim de organizá-las logicamente em uma taxonomia.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar as configurações de recursos da rede e detectar alterações nos recursos de rede relacionados às instâncias do Azure Cosmos DB. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede. 

Como visualizar e recuperar eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo usada para os recursos do Azure, como o Azure Cosmos DB, para carimbos de tempo nos registros.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Ingerir logs via Azure Monitor para agregar dados de segurança gerados pelo Azure Cosmos DB. No Monitor do Azure, use o espaço de trabalho log analytics para consultar e executar análises e usar contas de armazenamento de armazenamento a longo prazo/arquivamento. Alternativamente, você pode habilitar e dados a bordo para o Azure Sentinel ou um SIEM (Security Incident and Event Management, gerenciamento de incidentes e eventos de terceiros). 

Como habilitar registros de diagnóstico para O Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico do Azure Cosmos DB e envie os logs para um espaço de trabalho ou conta de armazenamento do Log Analytics. As configurações de diagnóstico no Azure Cosmos DB são usadas para coletar registros de recursos. Esses registros são capturados por solicitação e também são chamados de "registros de data plane". Alguns exemplos das operações do plano de dados incluem excluir, inserir e ler. Você também pode habilitar as configurações de diagnóstico do Log de atividade do Azure e enviá-las para o mesmo espaço de trabalho do Log Analytics.

Como ativar as configurações de diagnóstico do Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Como ativar as configurações de diagnóstico para o registro de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: No Monitor do Azure, defina o período de retenção de log para espaços de trabalho do Log Analytics associados às instâncias do Azure Cosmos DB de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de log:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Você pode executar consultas no Log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos registros do Azure Cosmos DB que você reuniu.

Como executar consultas para O Azure Cosmos DB no Log Analytics Workspaces:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: No Azure Security Center, habilite o Advanced Threat Protection for Azure Cosmos DB para monitorar atividades anômalas em registros de segurança e eventos. Habilite as configurações de diagnóstico no Azure Cosmos DB e envie logs para um espaço de trabalho do Log Analytics.

 

Você também pode embarcar no seu espaço de trabalho do Log Analytics para o Azure Sentinel, pois ele fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isso permite que as cartilhas (soluções automatizadas) sejam criadas e usadas para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados em seu espaço de trabalho do Log Analytics usando o Azure Monitor.

Lista de alertas de proteção contra ameaças para Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Criar, visualizar e gerenciar alertas de log usando o Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; O Azure Cosmos DB não processa ou produz logs relacionados a malware.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável; O Azure Cosmos DB não processa ou produz registros relacionados ao DNS.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: Você pode usar o painel de controle de identidade e acesso (IAM) no portal Azure para configurar o Controle de Acesso Baseado em Função (RBAC) e manter o inventário nos recursos do Azure Cosmos DB. As funções são aplicadas a usuários, grupos, diretores de serviços e identidades gerenciadas no Active Directory. Você pode usar funções incorporadas ou funções personalizadas para indivíduos e grupos.

O Azure Cosmos DB fornece RBAC integrado para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Active Directory (AD) pode atribuir essas funções RBAC a usuários, grupos, diretores de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações nos recursos do Azure Cosmos DB.

Você também pode usar o módulo Azure AD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos. 

Além disso, algumas ações no Azure Cosmos DB podem ser controladas com o Azure Active Directory e chaves mestras específicas da conta.  Use a configuração da conta 'desativarChaveBaseMetadataWriteAccess' para controlar o acesso à chave.

Entenda o controle de acesso baseado em função no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Construa suas próprias funções personalizadas usando ações do Azure Cosmos DB (espaço de nome Microsoft.DocumentDB):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Crie uma nova função no Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Como obter um papel de diretório no Diretório Ativo do Azure com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Diretório Ativo do Azure com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Restringir o acesso do usuário apenas às operações de dados:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O conceito de senhas padrão ou em branco não existe em relação ao Azure AD ou Azure Cosmos DB. Em vez disso, o Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso aos seus dados e recursos; chaves mestres e tokens de recursos. As chaves podem ser regeneradas a qualquer momento.

Entendendo o acesso seguro aos dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Como regenerar as chaves do Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Como acessar programáticamente chaves usando o Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Não aplicável; O Azure Cosmos DB não suporta contas de administrador.  Todo o acesso é integrado ao Azure Active Directory e ao RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure).



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: O Azure Cosmos DB usa dois tipos de chaves para autorizar os usuários e não suporta O SSO (Single Sign-On) no nível do plano de dados. O acesso ao plano de controle do Cosmos DB está disponível via API REST e suporta SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações em um Token Web JSON que você obtém do Azure Active Directory.

Entenda o banco de dados do Azure para a API Cosmos DB REST:https://docs.microsoft.com/rest/api/cosmos-db/

Entenda o SSO com o Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite a autenticação multifatorial do Diretório Ativo do Azure e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use estações de trabalho de acesso privilegiado (PAW) com autenticação multifatorial configurada para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use o Advanced Threat Protection (ATP) para o Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você aborde ameaças e as integre a sistemas centrais de monitoramento de segurança. 

Além disso, você pode usar o Azure Active Directory (AD) Privileged Identity Management (PIM) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Use detecções de risco Azure AD para visualizar alertas e relatórios sobre comportamentos de usuários de risco.

Como implantar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie seus locais nomeados. Com locais nomeados, você pode criar agrupamentos lógicos de faixas de endereços IP ou países e regiões. Você pode restringir o acesso a recursos confidenciais, como as instâncias do Azure Cosmos DB, aos locais nomeados configurados.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AD) como sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância do Azure Active Directory:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como configurar e gerenciar a autenticação do Azure Active Directory com o Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo.

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Você pode criar configurações de diagnóstico para contas de usuários do Azure Active Directory, enviando os logs de auditoria e logs de login para um espaço de trabalho do Log Analytics onde você pode configurar os alertas desejados.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use o Advanced Threat Protection (ATP) para o Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você aborde ameaças e as integre a sistemas centrais de monitoramento de segurança. 

Você também pode usar o recurso de proteção de identidade ad do Azure para configurar respostas automatizadas para detectar ações suspeitas relacionadas às identidades dos usuários. Além disso, você pode ingerir logs no Azure Sentinel para mais investigações.

Como ver as entradas arriscadas do Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Atualmente não disponível; O Customer Lockbox ainda não foi suportado para o Banco de Dados Azure para Cosmos DB.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags para ajudar no rastreamento de instâncias do Azure Cosmos DB que armazenam ou processam informações confidenciais.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. As instâncias do Azure Cosmos DB são separadas por rede/sub-rede virtuais, marcadas adequadamente e protegidas dentro de um grupo de segurança de rede (NSG) ou do Azure Firewall. As instâncias do Azure Cosmos DB que armazenam dados confidenciais devem ser isoladas. Ao usar o Azure Private Link, você pode se conectar a uma conta de instância do Azure Cosmos DB através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados em uma sub-rede dentro de sua rede virtual. Em seguida, você pode limitar o acesso aos endereços IP privados selecionados. 

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar um Ponto Final Privado para O Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Como criar um grupo de segurança de rede com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Você pode implantar o Advanced Threat Protection for Azure Cosmos DB, que detectará atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. No momento, ele pode acionar os seguintes alertas:

- Acesso a partir de locais incomuns

- Extração de dados incomum

Além disso, ao usar máquinas virtuais para acessar suas instâncias do Azure Cosmos DB, faça uso de Private Link, Firewall, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados. A Microsoft gerencia a infra-estrutura subjacente ao Azure Cosmos DB e implementou controles rigorosos para evitar a perda ou exposição de dados de clientes.

Como configurar o Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: Todas as conexões com o Azure Cosmos DB suportam HTTPS. O Azure Cosmos DB também suporta O TLS1.2. É possível impor uma versão tls mínima do lado do servidor. Para isso, entre [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)em contato.

Visão geral da Segurança do Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos automáticos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Cosmos DB. No entanto, você pode usar a integração azure Cognitive Search para classificação e análise de dados. Você também pode implementar uma solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Index Azure Cosmos DB dados com https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampAzure Cognitive Search: ;bc=/azure/cosmos-db/breadcrumb/toc.json

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Azure Cosmos DB fornece rbac (control de acesso baseado em função) integrado para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Active Directory pode atribuir essas funções RBAC a usuários, grupos, diretores de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações nos recursos do Azure Cosmos DB. As atribuições das tarefas de função são escopo apenas para acesso a plano de controle, o que inclui acesso a contas, bancos de dados, contêineres e ofertas (throughput).

Como implementar o RBAC no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

A Microsoft gerencia a infra-estrutura subjacente ao Cosmos DB e implementou controles rigorosos para evitar a perda ou exposição dos dados dos clientes.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: Todos os dados do usuário armazenados no Cosmos DB são criptografados em repouso por padrão. Não há controles para desligá-lo. O Azure Cosmos DB usa criptografia AES-256 em todas as regiões onde a conta está sendo executado.

Por padrão, a Microsoft gerencia as chaves usadas para criptografar os dados em sua conta do Azure Cosmos. Você pode optar opcionalmente por adicionar uma segunda camada de criptografia com suas próprias chaves.

Entendendo a criptografia em repouso com o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Entendendo o gerenciamento de chaves para criptografia em repouso com o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Como configurar chaves gerenciadas pelo cliente para sua conta Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Azure com o Registro de Atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de produção do Azure Cosmos DB.

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para suas instâncias do Azure Cosmos DB. 

A Microsoft executa o patch de sistema e o gerenciamento de vulnerabilidades nos hosts subjacentes que suportam as instâncias do Azure Cosmos DB. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Recursos suportados disponíveis no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o portal Azure ou o Gráfico de Recursos do Azure para descobrir todos os recursos (não limitados ao Azure Cosmos DB, mas também incluindo recursos como computação, outros armazenamentos, rede, portas e protocolos etc.) dentro de sua assinatura(s).  Certifique-se de ter permissões apropriadas em seu inquilino e ser capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entendendo o controle de acesso baseado em função do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags em suas instâncias do Azure Cosmos DB e recursos relacionados com metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Quais recursos do Azure Cosmos DB suportam tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use tagging, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear ativos, incluindo, mas não se limitando aos recursos do Azure Cosmos DB. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais e Azure como um todo.

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

**Orientação**: Não aplicável; esta linha de base destina-se a recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais e Azure como um todo.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Use a Diretiva Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos 

- Tipos de recursos permitidos

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com a Política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o AzureResources Manager através de scripts

**Orientação**: Use o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management". Isso pode impedir a criação e alterações de recursos em um ambiente de alta segurança.

Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: Não aplicável; esta diretriz destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para suas instâncias do Cosmos DB com a Diretiva Azure. Use aliases de diretiva do Azure no namespace "Microsoft.DocumentDB" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do Cosmos DB. Você também pode fazer uso de definições de políticas incorporadas para o Azure Cosmos DB, tais como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

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

**Orientação**: Se usar definições de diretiva personalizadas do Azure para seu Cosmos DB ou recursos relacionados, use os Repos do Azure para armazenar e gerenciar seu código com segurança.

Documentação de Repos https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops do Azure:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negue], e [implantar se não existir] para aplicar automaticamente configurações para suas instâncias db do Azure Cosmos e recursos relacionados. 

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Para que máquinas virtuais do Azure ou aplicativos web em execução no Azure App Service sejam usados para acessar suas instâncias do Azure Cosmos DB, use a Identidade de Serviço Gerenciada em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto do Azure Cosmos DB. Certifique-se de que o Key Vault Soft Delete esteja ativado.

Como se integrar com as identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Para que máquinas virtuais do Azure ou aplicativos web em execução no Azure App Service sejam usados para acessar suas instâncias do Azure Cosmos DB, use a Identidade de Serviço Gerenciada em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto do Azure Cosmos DB.

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código.

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

**Orientação**: Não aplicável; esta diretriz destina-se a recursos computacionais. O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Azure App Service), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Serviço de Aplicativos Do Azure), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade pré-scanear quaisquer arquivos que estão sendo carregados para recursos não computados do Azure, incluindo o Azure Cosmos DB. A Microsoft não pode acessar os dados do cliente e, portanto, não pode realizar varreduras anti-malware do conteúdo do cliente em seu nome.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; benchmark destina-se a recursos computacionais. O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: O Azure Cosmos DB tira instantâneos de seus dados a cada quatro horas. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. A qualquer momento, somente os últimos dois instantâneos são mantidos. No entanto, se o contêiner ou banco de dados for excluído, o Azure Cosmos DB reterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias. Entre em contato com o suporte do Azure para restaurar a partir de um backup.

Entendendo backups automatizados do Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: O Azure Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Se o banco de dados ou contêiner for excluído, você pode registrar um bilhete de suporte ou ligar para o suporte do Azure para restaurar os dados de backups on-line automáticos. O suporte ao Azure está disponível apenas para planos selecionados, como Standard, Developer e planos superiores a eles. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo. 

Se usar o Key Vault para armazenar credenciais para suas instâncias do Cosmos DB, certifique-se de backups automatizados regulares de suas chaves.

Entenda os backups automatizados do Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como fazer backup das chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Se o banco de dados ou contêiner for excluído, você pode registrar um bilhete de suporte ou ligar para o suporte do Azure para restaurar os dados de backups on-line automáticos. O suporte ao Azure está disponível apenas para planos selecionados, como Standard, Developer e planos superiores a eles. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

Teste a restauração de seus segredos armazenados no Azure Key Vault usando o PowerShell. O cmdlet Restore-AzureKeyVaultKey cria uma chave no cofre de chaves especificado. Esta chave é uma réplica da chave de backup no arquivo de entrada e tem o mesmo nome da chave original.

Entenda os backups automatizados do Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados no Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como restaurar os segredos do cofre da chave do Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Como todos os dados do usuário armazenados no Cosmos DB são criptografados em repouso e em transporte, você não precisa tomar nenhuma ação. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. O Azure Cosmos DB usa criptografia AES-256 em todas as regiões onde a conta está sendo executado.

Habilite o Soft-Delete no Cofre de Chaves para proteger as chaves contra exclusão acidental ou maliciosa.

Entenda a criptografia de dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Como ativar o Soft-Delete no Cofre de Chaves:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.

Você também pode aproveitar o Guia de Tratamento de Incidentes de Segurança de Computador da NIST para ajudar na criação do seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está em encontrar ou nas análises usadas para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marca claramente assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento do Azure Security Center**: Não aplicável

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

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia e execução do Red Teaming e testes de penetração de sites ao vivo contra infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
