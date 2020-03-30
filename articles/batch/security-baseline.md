---
title: Linha de base de segurança do Azure para lote
description: Linha de base de segurança do Azure para lote
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a9569c1f5de797c77f447b5df15e85a57e8be84b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472921"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança do Azure para lote

A linha de base de segurança do Azure para batch contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esses serviços é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Implantar pool(s) azure batch dentro da rede virtual. Para permitir que os nódulos de computação de pool se comuniquem com segurança com outras máquinas virtuais ou com uma rede local, você pode provisionar o pool em uma sub-rede de uma rede virtual Azure. Além disso, a implantação do Pool dentro de uma rede virtual dá-lhe controle sobre o grupo de segurança de rede (NSG) usado para proteger as interfaces de rede (NIC) dos nós individuais, bem como a sub-rede. Configure o NSG para permitir o tráfego somente IP(s)/locais confiáveis na Internet.


Como criar um pool de lotes do Azure dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICS

**Orientação**: Use o Azure Security Center e remediar as recomendações de proteção de rede relacionadas ao grupo de segurança de rede virtual /rede (NSG) associados ao seu pool de lotes. Habilite os registros de fluxo no NSG que estão sendo usados para proteger seu pool de lotes e envie logs em uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Azure Log Analytics e usar o Azure Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.


Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Como ativar e usar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Entenda a segurança de rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos críticos da Web

**Orientação**: Não aplicável, o Benchmark destina-se a aplicativos web em execução no Azure App Service ou em instâncias IaaS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite o Azure DDoS (negação distribuída de serviço) Proteção padrão na rede virtual que protege seu pool de lotes Do Zure para proteção contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.


Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Entenda o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Habilite os logs de fluxo no grupo de segurança de rede (NSG) que estão sendo usados para proteger o pool de lotes do Azure e envie logs em uma conta de armazenamento do Azure para auditoria de tráfego.


Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede

**Orientação**: Se necessário para fins de conformidade, selecione um dispositivo virtual de rede do Azure Marketplace que suporte a funcionalidade de sistemas de detecção de intrusões (IDS) e sistemas de prevenção de intrusões (IPS) com recursos de inspeção de carga.


Se a detecção e/ou prevenção de intrusões com base na inspeção de carga não for um requisito, o Firewall Azure com inteligência de ameaças pode ser usado. A filtragem baseada em ameaças do Azure Firewall pode alertar e negar tráfego de e para endereços e domínios IP maliciosos conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.


Implante o Firewall Azure com um endereço IP público na mesma rede virtual que os ates do Pool de Lotes do Azure. Configure as regras de tradução de endereços de rede (NAT) entre locais confiáveis na Internet e os endereços IP privados de seus nós de pool individuais. No Firewall do Azure, em Threat Intelligence, configure "Alerta e negação" para bloquear para alertar e bloquear o tráfego de/para endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são originados do feed de inteligência de ameaças da Microsoft, e apenas os registros de confiança mais altos estão incluídos. 


Como criar um pool de lotes do Azure dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Como implantar o Firewall Do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal


Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerencie o tráfego para seus aplicativos web

**Orientação**: Não aplicável, o Benchmark destina-se a aplicativos web em execução no Azure App Service ou em instâncias IaaS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Use tags de serviço de rede virtuais para definir controles de acesso à rede em grupos de segurança de rede ou Firewalls Azure associados ao pool de lotes do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.


Entenda e use tags de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos pools de lotes do Azure com a Política Do Azure. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.Batch" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos pools do Azure Batch.



Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para grupos de serviços de rede (NSGs) e outros recursos relacionados à segurança da rede e fluxo de tráfego associados aos seus pools de lotes do Azure. Para regras individuais do NSG, use o campo "Descrição" para especificar a necessidade e/ou duração dos negócios (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.


Use qualquer uma das definições de política incorporadas do Azure relacionadas à marcação, como "Exigir tag e seu valor" para garantir que todos os recursos sejam criados com tags e notificá-lo dos recursos não marcados existentes.


Você pode usar o Azure PowerShell ou o Azure CLI para procurar ou executar ações com base em recursos com base em suas tags.


Como criar e usar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Como criar um NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar as configurações de recursos da rede e detectar alterações nos recursos de rede relacionados aos seus pools de lotes do Azure. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: Para o Azure Batch, por padrão, a Microsoft fornece sincronização de tempo. No entanto, se você tiver requisitos específicos de sincronização de tempo, você pode implementar essas alterações.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento do registro de segurança central

**Orientação**: Conta do Azure Batch a bordo do Azure para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detectar e responder a ameaças no ambiente.  Para monitoramento em nível de recurso do Azure Batch, use as APIs de lote para monitorar ou consultar o status de seus recursos, incluindo empregos, tarefas, números de nó e pools.



Como embarcar em uma conta do Azure Batch para o Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Para o monitoramento do nível da conta do Azure Batch, monitore cada conta em lote usando recursos do Monitor Do Azure. O Azure Monitor coleta métricas e, opcionalmente, logs de diagnóstico para recursos com escopo no nível de uma conta do Lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades na conta do Lote e para diagnosticar problemas.


Para monitoramento do nível de recursos do Azure Batch, use as APIs do lote do Azure para monitorar ou consultar o status de seus recursos, incluindo empregos, tarefas, números de nó e pools.


Como configurar o monitoramento e o registro em nível de conta do Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview


Entenda o monitoramento em nível de recurso em lote:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Coletar registros de segurança do sistema operacional

**Orientação**: O Azure Monitor coleta métricas e registros de diagnóstico para recursos em sua conta do Azure Batch. Colete e consuma esses dados de várias maneiras de monitorar sua conta do Azure Batch e diagnosticar problemas. Também é possível configurar alertas de métrica para receber notificações quando uma métrica alcançar um valor especificado.


Se necessário, você talvez se conecte aos seus nós de pool individuais via Secured Shell (SSH) ou Remote Desktop Protocol (RDP) para acessar os logs do sistema operacional local.


Como coletar registros de diagnóstico da sua conta do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


Como conectar remotamente aos seus nós de pool do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: Conta de lote a bordo do Azure para o Monitor do Azure. Certifique-se de que o espaço de trabalho do Azure Log Analytics usado tenha o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização


Como configurar o monitoramento e o registro do Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview


Como configurar o período de retenção do espaço de trabalho do Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registros de monitores e avaliações

**Orientação**: Crie alertas métricos do Lote Azure que sejam acionados quando o valor de uma métrica especificada cruzar um determinado limiar.


Como configurar alertas métricos do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Crie alertas métricos do Lote Azure que sejam acionados quando o valor de uma métrica especificada cruzar um determinado limiar.


Como configurar alertas métricos do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Use o Windows Defender em seus nós de lote individuais no caso de sistemas operacionais Windows ou forneça sua própria solução anti-malware se você estiver usando o Linux.

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Implementar solução de terceiros para registro de DNs

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Configure manualmente o registro do console e a transcrição do PowerShell em uma base por nó.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter inventário de contas administrativas

**Orientação**: Manter o registro da conta administrativa local criada durante o provisionamento do pool de lotes do Azure, bem como quaisquer outras contas que você criar. Além disso, se a integração do Azure Active Directory (AAD) for usada, a AAD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, que podem ser consultadas. Use o módulo AAD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos.


Além disso, você pode usar as recomendações de identidade e gerenciamento de acesso do Azure Security Center.


Como obter um papel de diretório no AAD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Como obter membros de um papel de diretório no AAD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Como monitorar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: Ao provisionar um pool de lotes do Azure, você tem a opção de criar contas de máquina local. Não há senhas padrão para alterar, no entanto, você pode especificar diferentes senhas para acesso ao SSH (Secured Shell) e Remote Desktop Protocol (RDP). Depois que o Azure Batch Pool for configurado, você pode gerar um usuário aleatório para nós individuais dentro do portal Azure ou via API do Azure Resource Manager.


Como adicionar um usuário a um nó computacional específico:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Garantir o uso de contas administrativas dedicadas

**Orientação**: Integrar autenticação para aplicativos em lote do Azure com o Diretório Ativo do Azure. Crie políticas e procedimentos em torno do uso de contas administrativas dedicadas.


Além disso, você pode usar as recomendações de identidade e gerenciamento de acesso do Azure Security Center.


Como autenticar aplicativos em lote com o Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth


Como monitorar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize o Single Sign-On (SSO) com o Azure Active Directory

**Orientação**: Não aplicável, Enquanto o Azure Batch suporta a autenticação Azure AD, o login único não é suportado.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use a autenticação multifatorial para todos os acessos baseados no Azure Active Directory.

**Orientação**: Integrar autenticação para aplicativos em lote do Azure com o AAD (AAD). Habilite a autenticação multifatorial AAD (MFA) e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.
 


Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Como monitorar identidade e acesso no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho de acesso privilegiado) com autenticação multifatorial (MFA) configurada para fazer login e configurar seus recursos do Azure Batch.


Saiba mais sobre estações de trabalho de acesso privilegiado:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Log e Alerta sobre Atividades Suspeitas de Contas Administrativas

**Orientação**: Se você tiver autenticação integrada para aplicativos de lote do Azure com o AAD (AAD), use relatórios de segurança do Azure Active Directory para geração de registros e alertas quando ocorrer atividades suspeitas ou inseguras no ambiente. Use o Azure Security Center para monitorar a atividade de identidade e acesso.


Como identificar usuários do Azure AD sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerenciar o recurso do Azure a partir de apenas locais aprovados

**Orientação**: Se você tiver autenticação integrada para aplicativos de lote do Azure com o Azure Active Directory, você pode usar locais nomeados de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.



Como configurar locais nomeados no Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o AaD (AAD) Active Directory (AAD) como o sistema central de autenticação e autorização e integre a Autenticação para aplicativos em lote do Azure com AAD. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também armazena sais, hashes e armazena com segurança as credenciais do usuário.


Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


Como autenticar aplicativos em lote com AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: O AaD (AAD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso dos usuários pode ser revisto regularmente para garantir que apenas os usuários certos tenham acesso contínuo.


Como usar as avaliações de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Crie configurações de diagnóstico para contas de usuários do Azure Active Directory, enviando os registros de auditoria e logs de login para um espaço de trabalho do Azure Log Analytics. Configure os alertas desejados no espaço de trabalho do Azure Log Analytics.


Como integrar os Logs de Atividades do Azure no Monitor do Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use o recurso AaD (Risk Detections) do Azure Active Directory (AAD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas às identidades dos usuários. Além disso, você pode ingerr dados no Azure Sentinel para mais investigações.


Como visualizar os logins arriscados do AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte<br></div>

**Orientação**: Não disponível; O Customer Lockbox ainda não foi suportado para o Azure Batch. Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

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

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Os pools de lotes do Azure devem ser separados por rede/sub-rede virtual, marcados adequadamente e protegidos com um NSG (Network Security Groups). Os dados do Azure Batch devem ser contidos em uma conta de armazenamento azure protegida.


Como criar um pool de lotes do Azure dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Como proteger contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais.

**Orientação**: Para contas de armazenamento azure associadas aos seus pools de lotes do Azure que contêm informações confidenciais, marque-as como sensíveis usando tags e proteja-as com as práticas recomendadas do Azure.


Os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para recursos de armazenamento ou computação do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.


Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.


Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Como proteger contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: Criptografe todas as informações confidenciais em trânsito. Os recursos do Microsoft Azure negociarão o TLS 1.2 por padrão. Certifique-se de que todos os clientes que se conectam aos seus Pools de Lotes do Azure ou aos armazenamentos de dados (Contas de Armazenamento Azure) sejam capazes de negociar TLS 1.2 ou superior.


Certifique-se de que https é necessário para acessar a conta de armazenamento que contém seus dados do Azure Batch.


Entenda a criptografia da conta de armazenamento do Azure em trânsito:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Para contas de armazenamento azure associadas aos seus pools de lotes do Azure que contêm informações confidenciais, marque-as como sensíveis usando tags e proteja-as com as práticas recomendadas do Azure.


Os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para recursos de armazenamento ou computação do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.


Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.


Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Como proteger contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Use o RBAC (AaD) Role-based Role-based (RBAC) do Azure Active Directory (AAD) para controlar o acesso ao plano de gerenciamento de recursos do Azure, incluindo conta em lote, pool de lotes e contas de armazenamento.


Entenda o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview


Como configurar o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a Prevenção de Perda de Dados baseada em host para reforçar o controle de acesso

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas de dados ainda não estão disponíveis para recursos de armazenamento ou computação do Azure. Implementar solução de terceiros, se necessário para fins de conformidade.



Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.



Entenda a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Orientação**: Para contas de armazenamento associadas à sua conta Azure Batch, recomenda-se permitir que a Microsoft gerencie chaves de criptografia, no entanto, você tem a opção de gerenciar suas próprias chaves, se necessário.



Como gerenciar chaves de criptografia para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor do Azure com o Registro de Atividades do Azure para criar alertas para quando as alterações ocorrerem em recursos críticos do Azure relacionados ou associados às suas contas/pools do Azure Batch.



Configure configurações de diagnóstico para contas de armazenamento associadas ao Pool de lotes do Azure para monitorar e registrar todas as operações CRUD contra dados de pool.



Como criar alertas para eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



Como habilitar o registro/auditoria adicional para uma conta de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Para nós de pool de lotes do Azure, você é responsável pelo gerenciamento da solução de gerenciamento de vulnerabilidades.


Opcionalmente, se você tiver uma assinatura de plataforma de gerenciamento de vulnerabilidades Rapid7, Qualys ou qualquer outra plataforma de gerenciamento de vulnerabilidades, você pode instalar manualmente agentes de avaliação de vulnerabilidades em nós de pool de lotes e gerenciar os nós através do respectivo portal.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: Microsoft para manter e atualizar imagens de nó de pool de lote azure base. Certifique-se de que o sistema operacional do azure Batch Pool permanece corrigido durante a duração da vida útil do cluster, o que pode exigir a ativação de atualizações automáticas, o monitoramento dos nódulos ou a realização de reinicializações periódicas.

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patch de software de terceiros

**Orientação**: Certifique-se de que os aplicativos de terceiros do Azure Batch Pool permanecem corrigidos durante a duração da vida útil do cluster, o que pode exigir a ativação de atualizações automáticas, o monitoramento dos nódulos ou a realização de reinicializações periódicas.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare varreduras de vulnerabilidades back-to-back

**Orientação**: Se você tiver uma assinatura de plataforma de gerenciamento de vulnerabilidades Rapid7, Qualys ou qualquer outra, você pode usar o portal desse fornecedor para visualizar e comparar varreduras de vulnerabilidades back-to-back.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação das vulnerabilidades descobertas.

**Orientação**: Use um programa comum de pontuação de risco (por exemplo, Sistema de Pontuação de Vulnerabilidade Comum) ou as classificações de risco padrão fornecidas pela ferramenta de varredura de terceiros.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, etc.) dentro de sua assinatura(s). Certifique-se de que você tenha permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.


Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph Explorer, é altamente recomendável criar e usar os recursos arm (Arm) do Azure Resource Manager daqui para frente.


Como criar consultas com o Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Como ver suas assinaturas do Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Entenda o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento do Azure Security Center**: Não aplicável

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



Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Definir lista de recursos aprovados do Azure e software aprovado para recursos de computação

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos azure não aprovados

**Orientação**: Use a política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos


Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s). Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.


Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Como criar consultas com o Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro de recursos de computação

**Orientação**: Para os ádenos de pool de lotes do Azure, implemente uma solução de terceiros para monitorar os álos de cluster para aplicativos de software não aprovados.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software do Azure não aprovados

**Orientação**: Para os ádenos de pool de lotes do Azure, implemente uma solução de terceiros para monitorar os álos de cluster para aplicativos de software não aprovados.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Para os ádenos do Pool de lotes Azure, implemente uma solução de terceiros para impedir a execução de software não autorizado.

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

**Orientação**: Para os contratos de pool de lotes do Azure, implemente uma solução de terceiros para impedir a execução de tipos de arquivos não autorizados.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com o Azure Resource Manager via Scripts</div>

**Orientação**: Use o Azure Conditional Access para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".


Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro dos recursos de computação

**Orientação**: Não aplicável,

Isso não se aplica ao Azure Batch, pois os usuários (não administradores) dos pools de lotes do Azure não precisam ter acesso aos nós individuais para executar trabalhos. O administrador de cluster já tem acesso raiz a todos os nós.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

**Orientação**: Não aplicável, o Benchmark destina-se a aplicativos web em execução no Azure App Service ou em instâncias IaaS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

**Orientação**: Use aliases de diretiva do Azure no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração de suas contas e pools do Azure Batch.


Como visualizar aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabeleça configurações seguras para o seu sistema operacional

**Orientação:** Estabeleça configurações seguras para o sistema operacional dos nossos nós de pool de lote.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras para os recursos do Azure relacionados à sua conta em lote e pools (como redes virtuais, sub-redes, Firewalls Azure, Contas de Armazenamento Azure, etc.). Você pode usar aliases de diretiva do Azure a partir dos seguintes namespaces para criar políticas personalizadas:

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network


Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Entenda os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operacionais

**Orientação**: Imagens do sistema operacional do pool de lotes do Azure gerenciadas e mantidas pela Microsoft. Você é responsável pela implementação da configuração de estado em nível de SO.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições de diretiva personalizadas do Azure para suas contas, pools ou recursos relacionados do Azure, use os Azure Repos para armazenar e gerenciar seu código com segurança.


Como armazenar código no Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops


Documentação de Repos do Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Se usar imagens personalizadas para os pools do Azure Batch, use o RBAC (Role-based Access Control, controle de acesso baseado em função) para garantir que apenas usuários autorizados possam acessar as imagens.


Entenda o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles


Como configurar o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use definições de diretiva azure incorporadas para alertar, auditar e impor configurações de recursos relacionadas ao azure Batch.  Use aliases de diretiva do Azure no namespace "Microsoft.Batch" para criar políticas personalizadas para suas contas e pools do Azure Batch. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.



Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Implemente uma solução de terceiros para manter o estado desejado para os sistemas operacionais do grupo de lotes azure.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da instância do lote do Azure. Você também pode usar quaisquer políticas incorporadas criadas especificamente para o Azure Batch ou os recursos usados pelo Azure Batch, tais como:

- As sub-redes devem ser associadas a um grupo de segurança de rede - contas de armazenamento devem usar um ponto final de serviço de rede virtual
- Os logs de diagnóstico em contas do Lote devem ser habilitados

Como visualizar aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Implemente uma solução de terceiros para monitorar o estado dos sistemas operacionais do grupo de lotes do Azure.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerencie com segurança os segredos do Azure

**Orientação**: O Azure Key Vault pode ser usado com implantações do Azure Batch para gerenciar chaves para armazenamento em pool satiscadas no Azure Storage Accounts.


Como se integrar com as identidades gerenciadas do Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


Como criar um Cofre de Chaves Do Azure:

https://docs.microsoft.com/azure/key-vault/quick-create-portal


Como fornecer autenticação do Key Vault com uma identidade gerenciada:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação:** Não disponível, identidade de serviço gerenciada não suportada pelo Azure Batch

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

**Orientação**: Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner de Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de Segurança: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Use software anti-malware gerenciado centralmente

**Orientação**: Use o Windows Defender em seus nós de pool de lote azure individuais no caso de sistemas operacionais Windows ou forneça sua própria solução anti-malware se você estiver usando o Linux.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O Microsoft Antimalware está habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Lote Azure), no entanto, ele não é executado no conteúdo do cliente.


Pré-escaneie quaisquer arquivos que estão sendo carregados para recursos azure não computados, como Serviço de Aplicativo, Armazenamento de Data Lake, Armazenamento Blob, etc. A Microsoft não pode acessar os dados do cliente nessas instâncias.


Entenda o Microsoft Antimalware for Azure Cloud Services and Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizados

**Orientação**: Use o Windows Defender em seus ártões individuais do Azure Batch no caso de sistemas operacionais Windows e certifique-se de que a atualização automática esteja ativada. Forneça sua própria solução anti-malware se estiver usando o Linux.

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Orientação**: Ao usar uma conta de armazenamento do Azure para o armazenamento de dados do Pool de Lotes Do Azure, escolha a opção de redundância apropriada (LRS,ZRS, GRS, RA-GRS). 


Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Realize backups completos do sistema e faça backup de quaisquer chaves gerenciadas pelo cliente

**Orientação**: Ao usar uma conta de armazenamento do Azure para o armazenamento de dados do Pool de Lotes Do Azure, escolha a opção de redundância apropriada (LRS,ZRS, GRS, RA-GRS).  Se usar o Azure Key Vault para qualquer parte da implantação do Azure Batch, certifique-se de que suas chaves estão em backup.


Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Como fazer backup das chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Se você estiver gerenciando suas próprias chaves para contas de armazenamento do Azure ou qualquer outro recurso relacionado à implementação do lote do Azure, teste periodicamente a restauração de chaves de backup.


Como fazer backup das chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


Como restaurar uma chave gerenciada pelo cliente com o PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Se o Azure Key Vault estiver sendo usado para armazenar quaisquer chaves relacionadas às contas de armazenamento de pool de lote do Azure, habilite a exclusão suave no Cofre de Chaves do Azure para proteger as chaves contra exclusão acidental ou maliciosa.


Como ativar o Soft Delete no Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Certifique-se de que existam planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gerenciamento de incidentes.



Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Security Center atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você atende a cada alerta, para que quando um recurso estiver comprometido, você possa chegar a ele imediatamente. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornecer detalhes de contato &nbsp;com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada.



Como definir o contato de segurança do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Do Azure usando o recurso Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas para o Azure Sentinel.


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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta remediar todas as descobertas críticas de segurança dentro de 60 dias.

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução do Red Teaming e testes de penetração de sites ao vivo contra a infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
