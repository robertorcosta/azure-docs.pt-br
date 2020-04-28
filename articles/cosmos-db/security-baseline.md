---
title: Linha de base de segurança do Azure para Cosmos DB
description: Linha de base de segurança do Azure para Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e5c57ba56ce236bcf07478e1784eec6d4c632d1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195240"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Linha de base de segurança do Azure para Cosmos DB

A linha de base de segurança do Azure para Cosmos DB contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral de linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Orientação**: usando o link privado do Azure, você pode se conectar a uma conta do Azure Cosmos por meio de um ponto de extremidade privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode reduzir o risco de vazamento de dados Configurando um conjunto estrito de regras de saída em um grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

Você também pode usar pontos de extremidade de serviço para proteger sua conta do Azure Cosmos. Habilitando um ponto de extremidade de serviço, você pode configurar contas do Azure Cosmos para permitir o acesso somente de uma sub-rede específica de uma rede virtual do Azure. Depois que o ponto de extremidade de serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso a uma conta do Azure cosmos com conexões de uma sub-rede em uma rede virtual.

Você também pode proteger os dados armazenados em sua conta do Azure Cosmos usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta do Azure Cosmos usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

Visão geral do link privado do Azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Como configurar um ponto de extremidade privado para Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Como criar um grupo de segurança de rede com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como configurar o firewall IP no Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede relacionados à sua conta do Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que sua conta do Azure Cosmos, você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de dados vazamento. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditorias de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Entenda a segurança de rede fornecida pela central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar Análise de Tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: Use o recurso de compartilhamento de recursos entre origens (CORS) para habilitar um aplicativo Web em execução em um domínio para acessar recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

Configurar compartilhamento de recursos entre origens:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas às suas instâncias de Azure Cosmos DB para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

Como configureAzure Cosmos DB proteção avançada contra ameaças:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Como configurar a proteção contra DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda a inteligência de ameaças integrada da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Diretrizes**: Habilitar logs de fluxo do NSG (grupo de segurança de rede) e enviar logs para uma conta de armazenamento para auditoria de tráfego. Você pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar Análise de Tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central. 

Como configurar Cosmos DB proteção avançada contra ameaças:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; a recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Diretrizes**: para recursos que precisam de acesso à sua conta do Azure Cosmos, use marcas de serviço de rede virtual para definir controles de acesso à rede no securitygGroups de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

Para obter mais informações sobre como usar marcas de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. DocumentDB" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias de Azure Cosmos DB. Você também pode fazer uso de definições de política internas para Azure Cosmos DB, como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, RBAC (controle de acesso baseado em função) e políticas em uma única definição de Blueprint. Você pode aplicar facilmente o plano gráfico a novas assinaturas, ambientes e controle e gerenciamento finos por meio da versão.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Diretrizes**: use marcas para recursos de rede associados à sua implantação do Azure Cosmos DB para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias de Azure Cosmos DB. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. 

Como exibir e recuperar eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como Azure Cosmos DB para carimbos de data/hora nos logs.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure Cosmos DB. No Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar o e os dados integrados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros. 

Como habilitar os logs de diagnóstico para Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico para Azure Cosmos DB e envie os logs para um espaço de trabalho log Analytics ou conta de armazenamento. As configurações de diagnóstico no Azure Cosmos DB são usadas para coletar logs de recursos. Esses logs são capturados por solicitação e também são chamados de "logs do plano de dados". Alguns exemplos das operações do plano de dados incluem excluir, inserir e ler. Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviá-las para o mesmo espaço de trabalho Log Analytics.

Como habilitar as configurações de diagnóstico para Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Como habilitar as configurações de diagnóstico para o log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados às suas instâncias de Azure Cosmos DB de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de log:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: você pode executar consultas em log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos logs de Azure Cosmos DB que você coletou.

Como executar consultas para Azure Cosmos DB em espaços de trabalho do Log Analytics:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Orientação**: na central de segurança do Azure, habilite a proteção avançada contra ameaças para Azure Cosmos DB monitorar atividades anormais em logs de segurança e eventos. Habilite as configurações de diagnóstico no Azure Cosmos DB e envie logs para um espaço de trabalho Log Analytics.

 

Você também pode integrar seu espaço de trabalho Log Analytics ao Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Isso permite que os guias estratégicos (soluções automatizadas) sejam criados e usados para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados em seu espaço de trabalho do Log Analytics usando Azure Monitor.

Lista de alertas de proteção contra ameaças para Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Crie, exiba e gerencie alertas de log usando Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável; Azure Cosmos DB não processa nem produz logs relacionados a anti-malware.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável; Azure Cosmos DB não processa nem produz logs relacionados ao DNS.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Orientação**: você pode usar o painel iam (controle de acesso e identidade) no portal do Azure para configurar o RBAC (controle de acesso baseado em função) e manter o inventário em recursos de Azure Cosmos DB. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos.

O Azure Cosmos DB fornece RBAC interno para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory (AD) pode atribuir essas funções RBAC a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos.

Você também pode usar o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

Além disso, algumas ações no Azure Cosmos DB podem ser controladas com Azure Active Directory e chaves mestras específicas da conta.  Use a configuração de conta ' disableKeyBasedMetadataWriteAccess ' para controlar o acesso à chave.

Compreenda o controle de acesso baseado em função no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Crie suas próprias funções personalizadas usando ações de Azure Cosmos DB (namespace do Microsoft. DocumentDB):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Crie uma nova função no Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Como obter uma função de diretório no Azure Active Directory com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de uma função de diretório no Azure Active Directory com o PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Restringir o acesso do usuário somente a operações de dados:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Orientação**: o conceito de senhas padrão ou em branco não existe em relação ao Azure AD ou Azure Cosmos DB. Em vez disso, Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso a seus dados e recursos; chaves mestras e tokens de recurso. As chaves podem ser geradas novamente a qualquer momento.

Noções básicas sobre o acesso seguro aos dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Como regenerar chaves Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Como acessar de forma programática as chaves usando Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Orientação**: não aplicável; Azure Cosmos DB não dá suporte a contas de administrador.  Todo o acesso é integrado com o Azure Active Directory e o RBAC (controle de acesso baseado em função) do Azure.



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Diretrizes**: o Azure Cosmos DB usa dois tipos de chaves para autorizar usuários e não oferece suporte ao SSO (logon único) no nível do plano de dados. O acesso ao plano de controle para Cosmos DB está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações para um token Web JSON obtido de Azure Active Directory.

Entender o banco de dados do Azure para Cosmos DB API REST:https://docs.microsoft.com/rest/api/cosmos-db/

Entenda o SSO com Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Diretrizes**: habilite Azure Active Directory autenticação multifator e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar a identidade e o acesso na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central. 

Além disso, você pode usar o Azure Active Directory (AD) Privileged Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorre no ambiente.

Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado.

Como implantar Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Diretrizes**: Configure a condição de local de uma política de acesso condicional e gerencie seus locais nomeados. Com os locais nomeados, você pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Você pode restringir o acesso a recursos confidenciais, como suas instâncias de Azure Cosmos DB, aos locais nomeados configurados.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância de Azure Active Directory:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como configurar e gerenciar a autenticação Azure Active Directory com o SQL do Azure:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Como usar as revisões de acesso de identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Orientação**: você pode criar configurações de diagnóstico para Azure Active Directory contas de usuário, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho log Analytics em que você pode configurar os alertas desejados.

Como integrar os logs de atividades do Azure ao Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central. 

Você também pode usar o recurso de detecções de Azure AD Identity Protection e riscos para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir os logs no Azure Sentinel para uma investigação mais aprofundada.

Como exibir Azure Active Directory entradas arriscadas:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o banco de dados do Azure para Cosmos DB.

Lista de serviços Sistema de Proteção de Dados do Cliente com suporte:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de Azure Cosmos DB instâncias que armazenam ou processam informações confidenciais.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Azure Cosmos DB instâncias são separadas por rede virtual/sub-rede, marcadas adequadamente e protegidas em um NSG (grupo de segurança de rede) ou firewall do Azure. Azure Cosmos DB instâncias que armazenam dados confidenciais devem ser isoladas. Usando o link privado do Azure, você pode se conectar a uma conta de instância do Azure Cosmos DB por meio de um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro de sua rede virtual. Você pode então limitar o acesso aos endereços IP privados selecionados. 

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar um ponto de extremidade privado para Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Como criar um grupo de segurança de rede com uma configuração de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: você pode implantar a proteção avançada contra ameaças para Azure Cosmos DB, o que detectará atividades anormais indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Atualmente, ele pode disparar os seguintes alertas:

- Acesso de locais incomuns

- Extração de dados incomum

Além disso, ao usar máquinas virtuais para acessar suas instâncias de Azure Cosmos DB, use o link privado, o firewall, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados. A Microsoft gerencia a infraestrutura subjacente para Azure Cosmos DB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Como configurar Cosmos DB proteção avançada contra ameaças:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: todas as conexões com Azure Cosmos DB dão suporte a https. O Azure Cosmos DB também dá suporte a TLS 1.2. É possível impor uma versão mínima do TLS do lado do servidor. Para fazer isso, entre em [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)contato com.

Visão geral da segurança do Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda automática ainda não estão disponíveis para Azure Cosmos DB. No entanto, você pode usar a integração de Pesquisa Cognitiva do Azure para classificação e análise de dados. Você também pode implementar uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Indexar dados de Azure Cosmos DB com o https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampAzure pesquisa cognitiva:; BC =/Azure/Cosmos-DB/breadcrumb/TOC.JSON

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Diretrizes**: o Azure Cosmos DB fornece RBAC (controle de acesso baseado em função) interno para cenários de gerenciamento comuns no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory pode atribuir essas funções de RBAC a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos. As atribuições de função têm como escopo o acesso ao plano de controle, que inclui acesso a contas, bancos de dados, contêineres e ofertas (taxa de transferência) do Azure Cosmos.

Como implementar o RBAC no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para Cosmos DB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Diretrizes**: todos os dados de usuário armazenados no cosmos DB são criptografados em repouso por padrão. Não há controles para desativá-lo. Azure Cosmos DB usa a criptografia AES-256 em todas as regiões em que a conta está em execução.

Por padrão, a Microsoft gerencia as chaves que são usadas para criptografar os dados em sua conta do Azure Cosmos. Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com suas próprias chaves.

Noções básicas sobre criptografia em repouso com Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Entendendo o gerenciamento de chaves para criptografia em repouso com Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Como configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do Azure Cosmos DB.

Como criar alertas para eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure para suas instâncias de Azure Cosmos DB. 

A Microsoft executa aplicação de patch do sistema e gerenciamento de vulnerabilidade nos hosts subjacentes que dão suporte às instâncias de Azure Cosmos DB. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Recursos com suporte disponíveis na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usar um processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Diretrizes**: Use o portal do Azure ou o grafo de recursos do Azure para descobrir todos os recursos (sem limitação de Azure Cosmos DB, mas também incluindo recursos como computação, outro armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Verifique se você tem as permissões apropriadas em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

Como criar consultas com o grafo de recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Noções básicas sobre o controle de acesso baseado em função do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas às suas instâncias de Azure Cosmos DB e aos recursos relacionados com metadados para organizá-las logicamente em uma taxonomia.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Quais recursos de Azure Cosmos DB dão suporte a marcas:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos, incluindo, mas não se limitando a Azure Cosmos DB recursos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

**Orientação**: não aplicável; Essa diretriz destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o grafo do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: não aplicável; Esta linha de base destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: não aplicável; Essa diretriz destina-se a recursos de computação e ao Azure como um todo.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos 

- Tipos de recursos permitidos

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com o AzureResources Manager por meio de scripts

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure". Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança.

Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável; Essa diretriz destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Cosmos DB com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. DocumentDB" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Cosmos DB. Você também pode fazer uso de definições de política internas para Azure Cosmos DB, como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

Como exibir os aliases de Azure Policy disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entender Azure Policy efeitos:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições de Azure Policy personalizadas para seu Cosmos DB ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Documentação do Azure Repos https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops :https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para suas instâncias de Azure Cosmos DB e recursos relacionados. 

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar suas instâncias de Azure Cosmos DB, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos Azure Cosmos DB. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Como integrar com identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer Key Vault autenticação com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar suas instâncias de Azure Cosmos DB, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos Azure Cosmos DB.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

Como configurar identidades gerenciadas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar com identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

**Diretrizes**: implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.

Como configurar o verificador de credenciais:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [controle de segurança: defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação. O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure App Service), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure app Service), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente todos os arquivos que estão sendo carregados em recursos não computados do Azure, incluindo Azure Cosmos DB. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável; o parâmetro de comparação destina-se a recursos de computação. O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Diretrizes**: Azure Cosmos dB obtém instantâneos de seus dados a cada quatro horas. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. A qualquer momento, somente os últimos dois instantâneos são mantidos. No entanto, se o contêiner ou banco de dados for excluído, o Azure Cosmos DB reterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias. Contate o suporte do Azure para restaurar a partir de um backup.

Compreendendo Azure Cosmos DB backups automatizados:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Azure Cosmos DB automaticamente faz backups de seus dados em intervalos regulares. Se o banco de dados ou o contêiner for excluído, você poderá arquivar um tíquete de suporte ou chamar o suporte do Azure para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como Standard, Developer e Plans superiores. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo. 

Se estiver usando Key Vault para armazenar credenciais para suas instâncias de Cosmos DB, garanta backups regulares automatizados de suas chaves.

Entenda Azure Cosmos DB backups automatizados:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como fazer backup de chaves de Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: se o banco de dados ou o contêiner for excluído, você poderá arquivar um tíquete de suporte ou ligar para o suporte do Azure para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como Standard, Developer e Plans superiores. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

Teste a restauração de seus segredos armazenados no Azure Key Vault usando o PowerShell. O cmdlet Restore-AzureKeyVaultKey cria uma chave no cofre de chaves especificado. Essa chave é uma réplica da chave de backup no arquivo de entrada e tem o mesmo nome da chave original.

Entenda Azure Cosmos DB backups automatizados:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados no Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como restaurar Azure Key Vault segredos:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: como todos os dados de usuário armazenados no cosmos DB são criptografados em repouso e no transporte, você não precisa realizar nenhuma ação. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. Azure Cosmos DB usa a criptografia AES-256 em todas as regiões em que a conta está em execução.

Habilite a exclusão reversível no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

Entender a criptografia de dados no Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Como habilitar a exclusão reversível no Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

Você também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Como configurar Automaçãos de fluxo de trabalho na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está encontrando ou a análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

Consulte a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato do incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato da segurança da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da central de segurança do Azure**: Sim

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
