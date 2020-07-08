---
title: Linha de base de segurança do Azure para lote
description: Linha de base de segurança do Azure para lote
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 76312a55262d316c679bde2a69e8b98844addcb0
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963862"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança do Azure para lote

A linha de base de segurança do Azure para o lote contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretrizes**: implante pool (s) do lote do Azure na rede virtual. Para permitir que os nós de computação do pool se comuniquem com segurança com outras máquinas virtuais, ou com uma rede local, você pode provisionar o pool em uma sub-rede de uma rede virtual do Azure. Além disso, a implantação do pool em uma rede virtual lhe dá controle sobre o NSG (grupo de segurança de rede) usado para proteger a NIC (interfaces de rede) dos nós individuais, bem como a sub-rede. Configure o NSG para permitir o tráfego somente de IP confiável/Locations na Internet.

Como criar um pool do lote do Azure em uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede relacionadas ao NSG (rede virtual/grupo de segurança de rede) associado ao pool do lote. Habilite os logs de fluxo no NSG que está sendo usado para proteger o pool do lote e envie os logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho do Azure Log Analytics e usar o Análise de Tráfego do Azure para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Azure Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar as configurações de configuração de rede.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Entenda a segurança de rede fornecida pela central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção padrão do DDoS do Azure (negação de serviço distribuído) na rede virtual, protegendo seu pool do lote do Azure para proteção contra ataques de DDoS. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Como configurar a proteção contra DDoS: 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda a inteligência de ameaças integrada da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: Habilitar logs de fluxo no NSG (grupo de segurança de rede) que está sendo usado para proteger o pool do lote do Azure e enviar logs para uma conta de armazenamento do Azure para auditoria de tráfego.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: implantar sistemas de detecção de invasão/prevenção de intrusão baseados em rede

**Diretrizes**: se necessário para fins de conformidade, selecione um dispositivo de rede virtual no Azure Marketplace que ofereça suporte a sistemas de detecção de intrusão (IDs) e funcionalidade de sistemas de prevenção de intrusão (IPS) com recursos de inspeção de conteúdo.

Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for um requisito, o Firewall do Azure com inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência de ameaças do firewall do Azure pode alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante o Firewall do Azure com um endereço IP público na mesma rede virtual que os nós do pool do lote do Azure. Configure as regras de NAT (conversão de endereços de rede) entre locais confiáveis na Internet e os endereços IP privados de seus nós de pool individuais. No firewall do Azure, em inteligência contra ameaças, configure "alerta e negação" para bloquear o alerta e bloquear o tráfego de/para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e domínios são originados do feed do Microsoft Threat Intelligence e apenas os registros de confiança mais altos são incluídos. 

Como criar um pool do lote do Azure em uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como implantar o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: gerenciar o tráfego para seus aplicativos Web

**Orientação**: não aplicável, o parâmetro de comparação destina-se a aplicativos Web em execução no serviço Azure app ou em instâncias de IaaS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou firewalls do Azure associados aos pools do lote do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Entender e usar marcas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados aos pools do lote do Azure com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft.Batch" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus pools do lote do Azure.

Como configurar e gerenciar o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para NSGs (grupos de serviço de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados aos seus pools do lote do Azure. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Como criar e usar marcas: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos seus pools do lote do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do Log de Atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Orientação**: para o lote do Azure, por padrão, a Microsoft fornece sincronização de horário. No entanto, se você tiver requisitos de sincronização de tempo específicos, poderá implementar essas alterações.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Diretrizes**: integrar a conta do lote do Azure ao Azure monitor para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detectar e responder a ameaças no ambiente.  Para o monitoramento em nível de recursos do lote do Azure, use as APIs do lote para monitorar ou consultar o status de seus recursos, incluindo trabalhos, tarefas, nós e pools.

Como carregar uma conta do lote do Azure para Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: para o monitoramento em nível de conta do lote do Azure, monitore cada conta do lote usando os recursos de Azure monitor. O Azure Monitor coleta métricas e, opcionalmente, logs de diagnóstico para recursos com escopo no nível de uma conta do Lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades na conta do Lote e para diagnosticar problemas.

Para o monitoramento em nível de recurso do lote do Azure, use as APIs do lote do Azure para monitorar ou consultar o status de seus recursos, incluindo trabalhos, tarefas, nós e pools.

Como configurar o monitoramento e o registro em nível de conta do lote do Azure:

https://docs.microsoft.com/azure/batch/monitoring-overview

Entender o monitoramento em nível de recurso do lote:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: coletar logs de segurança do sistema operacional

**Diretrizes**: o Azure monitor coleta métricas e logs de diagnóstico para recursos em sua conta do lote do Azure. Colete e consuma esses dados de várias maneiras para monitorar sua conta do lote do Azure e diagnosticar problemas. Também é possível configurar alertas de métrica para receber notificações quando uma métrica alcançar um valor especificado.

Se necessário, você pode se conectar a seus nós de pool individuais via SSH (shell protegido) ou protocolo RDP (RDP) para acessar os logs do sistema operacional local.

Como coletar logs de diagnóstico de sua conta do lote do Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Como se conectar remotamente aos nós do pool do lote do Azure:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: integrar a conta do lote do Azure ao Azure monitor. Verifique se o espaço de trabalho do Azure Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização

Como configurar o log e o monitoramento do lote do Azure:

https://docs.microsoft.com/azure/batch/monitoring-overview

Como configurar o período de retenção do espaço de trabalho do Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: criar alertas de métrica do lote do Azure que são disparados quando o valor de uma métrica especificada ultrapassa um determinado limite.

Como configurar alertas de métrica do lote do Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Orientação**: criar alertas de métrica do lote do Azure que são disparados quando o valor de uma métrica especificada ultrapassa um determinado limite.

Como configurar alertas de métrica do lote do Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: Use o Windows Defender em seus nós de lote individuais no caso de sistemas operacionais Windows ou forneça sua própria solução antimalware se você estiver usando o Linux.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consulta DNS

**Diretrizes**: implementar solução de terceiros para registro em log DNS

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Diretrizes**: configure manualmente o log do console e a transcrição do PowerShell em uma base por nó.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: manter o inventário de contas administrativas

**Orientação**: manter o registro da conta administrativa local que é criada durante o provisionamento do pool do lote do Azure, bem como qualquer outra conta que você criar. Além disso, se a integração do Azure Active Directory (AAD) for usada, o AAD terá funções internas que devem ser explicitamente atribuídas e, portanto, consultáveis. Use o módulo AAD PowerShell para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como obter uma função de diretório no AAD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de uma função de diretório no AAD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Como monitorar a identidade e o acesso à central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Orientação**: ao provisionar um pool do lote do Azure, você terá a opção de criar contas do computador local. Não há nenhuma senha padrão a ser alterada; no entanto, você pode especificar senhas diferentes para acesso de shell seguro (SSH) e protocolo RDP (RDP). Depois que o pool do lote do Azure tiver sido configurado, você poderá gerar um usuário aleatório para nós individuais dentro do portal do Azure ou via API Azure Resource Manager.

Como adicionar um usuário a um nó de computação específico:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: garantir o uso de contas administrativas dedicadas

**Diretrizes**: integre a autenticação para aplicativos do lote do Azure com Azure Active Directory. Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como autenticar aplicativos do lote com o Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Como monitorar a identidade e o acesso à central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: utilizar SSO (logon único) com Azure Active Directory

**Orientação**: não aplicável, enquanto o lote do Azure dá suporte à autenticação do Azure AD, não há suporte para logon único.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Use a autenticação multifator para todo o acesso baseado em Azure Active Directory.

**Diretrizes**: integre a autenticação para aplicativos do lote do Azure com Azure Active Directory (AAD). Habilite a MFA (autenticação multifator) do AAD e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

 

Como habilitar a MFA no Azure: 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso na Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar seus recursos do lote do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado: 

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure: 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: se você tiver a autenticação integrada para aplicativos do lote do Azure com Azure Active Directory (AAD), use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

Como identificar usuários do Azure AD sinalizados para atividades arriscadas:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: gerenciar o recurso do Azure somente de locais aprovados

**Diretrizes**: se você tiver a autenticação integrada para aplicativos do lote do Azure com Azure Active Directory, poderá usar os locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas no Azure: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Orientação**: Use Azure Active Directory (AAD) como o sistema central de autenticação e autorização e integre a autenticação para aplicativos do lote do Azure com o AAD. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também Salts, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância do AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como autenticar aplicativos do lote com o AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: Azure Active Directory (AAD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso dos usuários pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Como usar as revisões de acesso de identidade do Azure: 

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: Crie configurações de diagnóstico para contas de usuário Azure Active Directory, enviando logs de auditoria e logs de entrada para um espaço de trabalho do Azure log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics do Azure.

Como integrar os logs de atividades do Azure ao Azure Monitor: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: Use o recurso de proteção de risco do AAD (Azure Active Directory) para configurar as respostas automatizadas para as ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

Como exibir entradas arriscadas do AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como integrar o Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte<br></div>

**Orientação**: não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o lote do Azure.
 
Lista de serviços suportados do Sistema de Proteção de Dados do Cliente: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os pools do lote do Azure devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos com um NSG (grupos de segurança de rede). Os dados do lote do Azure devem estar contidos em uma conta de armazenamento do Azure protegida.

Como criar um pool do lote do Azure em uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como proteger as contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitore e bloqueie a transferência não autorizada de informações confidenciais.

**Orientação**: para contas de armazenamento do Azure associadas aos pools do lote do Azure que contêm informações confidenciais, marque-as como confidenciais usando marcas e proteja-as com as práticas recomendadas do Azure.

Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure: 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como proteger as contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Microsoft Azure recursos negociará o TLS 1,2 por padrão. Verifique se todos os clientes que se conectam a seus pools de lote do Azure ou armazenamentos de dados (contas de armazenamento do Azure) são capazes de negociar o TLS 1,2 ou superior.

Verifique se o HTTPS é necessário para acessar a conta de armazenamento que contém os dados do lote do Azure.

Entender a criptografia da conta de armazenamento do Azure em trânsito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: para contas de armazenamento do Azure associadas aos pools do lote do Azure que contêm informações confidenciais, marque-as como confidenciais usando marcas e proteja-as com as práticas recomendadas do Azure.

Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure: 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como proteger as contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: usar o RBAC (controle de acesso baseado em função) Azure Active Directory (AAD) para controlar o acesso ao plano de gerenciamento de recursos do Azure, incluindo conta do lote, pools do lote e contas de armazenamento.

Entenda o RBAC do Azure: 

https://docs.microsoft.com/azure/role-based-access-control/overview

Como configurar o RBAC do Azure: 

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Diretrizes**: para contas de armazenamento associadas à sua conta do lote do Azure, é recomendável permitir que a Microsoft gerencie chaves de criptografia, no entanto, você tem a opção de gerenciar suas próprias chaves, se necessário.

Como gerenciar chaves de criptografia para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure relacionados ao ou associados às suas contas/pools do lote do Azure.

Defina as configurações de diagnóstico para contas de armazenamento associadas ao pool do lote do Azure para monitorar e registrar todas as operações CRUD em relação aos dados do pool.

Como criar alertas para eventos do log de atividades do Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como habilitar o registro em log/auditoria adicional para uma conta de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: para nós do pool do lote do Azure, você é responsável por gerenciar a solução de gerenciamento de vulnerabilidades.

Opcionalmente, se você tiver um Rapid7, Qualys ou qualquer outra assinatura da plataforma de gerenciamento de vulnerabilidades, poderá instalar manualmente agentes de avaliação de vulnerabilidade em nós do pool do lote e gerenciar nós por meio do portal respectivo.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Diretrizes**: Microsoft para manter e atualizar as imagens de nó do pool do lote do Azure base. Verifique se o sistema operacional dos nós do pool do lote do Azure permanece corrigido durante o tempo de vida do cluster, o que pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Orientação**: Verifique se os aplicativos de terceiros dos nós do pool do lote do Azure permanecem corrigidos durante o tempo de vida do cluster, o que pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Diretrizes**: se você tiver um Rapid7, Qualys ou qualquer outra assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar o portal desse fornecedor para exibir e comparar verificações de vulnerabilidade de back-to-back.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas.

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, etc.) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar recursos de Azure Resource Manager (ARM) no futuro.

Como criar consultas com o Azure Resource Graph: 

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure: 

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o RBAC do Azure: 

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: 

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar marcas de usuário e:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter inventário de recursos e títulos de software aprovados do Azure.

**Diretrizes**: definir a lista de recursos aprovados do Azure e o software aprovado para recursos de computação


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para monitorar os nós de cluster para aplicativos de software não aprovados.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para monitorar os nós de cluster para aplicativos de software não aprovados.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: para nós do pool do lote do Azure, implemente uma solução de terceiros para impedir a execução de software não autorizado.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com o Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para impedir a execução de tipos de arquivo não autorizados.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com Azure Resource Manager por meio de scripts</div>

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: não aplicável,

Isso não é aplicável ao lote do Azure, pois os usuários (não administradores) dos pools do lote do Azure não precisam acessar os nós individuais para executar trabalhos. O administrador de cluster já tem acesso à raiz para todos os nós.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável, o parâmetro de comparação destina-se a aplicativos Web em execução no serviço Azure app ou em instâncias de IaaS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração de suas contas e pools do lote do Azure.

Como exibir os aliases do Azure Policy disponíveis: 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: estabelecer configurações seguras para seu sistema operacional

**Orientação**: estabelecer configurações seguras para o sistema operacional dos nós do pool do lote.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: manter configurações seguras para todos os recursos do Azure

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras para os recursos do Azure relacionados à sua conta do lote e pools (como redes virtuais, sub-redes, firewalls do Azure, contas de armazenamento do Azure, etc.). Você pode usar Azure Policy aliases dos seguintes namespaces para criar políticas personalizadas:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos do Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: manter configurações seguras para sistemas operacionais

**Diretrizes**: imagens do sistema operacional do pool do lote do Azure gerenciadas e mantidas pela Microsoft. Você é responsável pela implementação da configuração de estado no nível do sistema operacional.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas para suas contas do lote do Azure, pools ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Diretrizes**: se você estiver usando imagens personalizadas para seus pools do lote do Azure, use o RBAC (controle de acesso baseado em função) para garantir que somente usuários autorizados possam acessar as imagens.

Entender o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Como configurar o RBAC do Azure: 

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: Use definições de Azure Policy internas para alertar, auditar e impor configurações de recursos relacionados ao lote do Azure.  Use Azure Policy aliases no namespace "Microsoft.Batch" para criar políticas personalizadas para suas contas e pools do lote do Azure. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para manter o estado desejado para os sistemas operacionais dos nós do pool do lote do Azure.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da instância do lote do Azure. Você também pode usar quaisquer políticas internas criadas especificamente para o lote do Azure ou os recursos usados pelo lote do Azure, como:

- As sub-redes devem ser associadas a um Grupo de Segurança de Rede

-As contas de armazenamento devem usar um ponto de extremidade de serviço de rede virtual

- Os logs de diagnóstico em contas do Lote devem ser habilitados

Como exibir os aliases do Azure Policy disponíveis: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para monitorar o estado dos sistemas operacionais dos nós do pool do lote do Azure.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="711-securely-manage-azure-secrets"></a>7,11: gerenciar com segurança os segredos do Azure

**Diretrizes**: Azure Key Vault podem ser usadas com implantações do lote do Azure para gerenciar chaves para armazenamento de pools em contas de armazenamento do Azure.

Como integrar-se às Identidades Gerenciadas do Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do cofre de chaves com uma identidade gerenciada: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: gerenciar identidades de forma segura e automática

**Orientação**: não disponível, identidade de serviço gerenciada não suportado pelo lote do Azure


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: Use o Windows Defender em seus nós individuais do pool do lote do Azure no caso de sistemas operacionais Windows ou forneça sua própria solução antimalware se você estiver usando o Linux.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, lote do Azure), mas não é executado no conteúdo do cliente.

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc. A Microsoft não pode acessar dados do cliente nessas instâncias.

Entenda o Microsoft antimalware para os serviços de nuvem do Azure e máquinas virtuais:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: Use o Windows Defender em seus nós de pool de lote do Azure individuais no caso de sistemas operacionais Windows e verifique se a atualização automática está habilitada. Forneça sua própria solução antimalware se você estiver usando o Linux.


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do pool do lote do Azure, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs). 

Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do pool do lote do Azure, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs).  Se estiver usando Azure Key Vault para qualquer parte da implantação do lote do Azure, verifique se o backup das suas chaves é feito.

Como configurar a redundância de armazenamento para contas de armazenamento do Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como fazer backup de chaves do Key Vault no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: se você estiver gerenciando suas próprias chaves para contas de armazenamento do Azure ou qualquer outro recurso relacionado à sua implementação do lote do Azure, teste periodicamente a restauração de chaves de backup.

Como fazer backup de chaves do Key Vault no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Como restaurar uma chave gerenciada pelo cliente com o PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: se Azure Key Vault estiver sendo usada para manter as chaves relacionadas às contas de armazenamento do pool do lote do Azure, habilite a exclusão reversível em Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

Como habilitar a exclusão reversível no Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: criar guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: criar o procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e capacidades: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações &nbsp; de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados foram acessados por uma parte ilegal ou não autorizada.

Como definir o contato de segurança da Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretriz**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e certifique-se de corrigir todas as conclusões de segurança críticas dentro de 60 dias.

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Você pode encontrar mais informações sobre a estratégia e a execução da Microsoft de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos da nuvem gerenciada da Microsoft, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
