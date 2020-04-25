---
title: Linha de base de segurança do Azure para banco de dados do Azure para MariaDB
description: Linha de base de segurança do Azure para banco de dados do Azure para MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: babfd9aafa56091bfdd526366b1042966222f993
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144605"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Linha de base de segurança do Azure para banco de dados do Azure para MariaDB

A linha de base de segurança do Azure para o banco de dados do Azure para MariaDB contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral de linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Orientação**: configurar o link privado para o banco de dados do Azure para MariaDB com pontos de extremidade privados. O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O link privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (rede virtual privada). O tráfego entre sua rede virtual e a instância MariaDB viaja pela rede de backbone da Microsoft.

Como alternativa, você pode usar pontos de extremidade de serviço de rede virtual para proteger e limitar o acesso de rede ao banco de dados do Azure para implementações MariaDB. As regras de rede virtual são um recurso de segurança de firewall que controla se o banco de dados do Azure para MariaDB aceita comunicações enviadas de sub-redes específicas em redes virtuais.

Você também pode proteger seu banco de dados do Azure para MariaDB com regras de firewall. O Firewall do servidor impede todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

Como configurar o link privado para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para o servidor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar o banco de dados do Azure para regras de firewall do MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: quando o banco de dados do Azure para o servidor MariaDB é protegido para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Como configurar o link privado para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como habilitar os logs de fluxo do https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal NSG: como habilitar e usar análise de tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: Use a proteção avançada contra ameaças para o banco de dados do Azure para MariaDB. A proteção avançada contra ameaças detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas ao banco de dados do Azure para instâncias de MariaDB para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar a proteção contra DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: quando o banco de dados do Azure para o servidor MariaDB é protegido para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Em seguida, você pode configurar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Como habilitar os logs de fluxo do https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal NSG: como habilitar e usar análise de tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use a proteção avançada contra ameaças para o banco de dados do Azure para MariaDB. A proteção avançada contra ameaças detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.
Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Orientação**: para recursos que precisam de acesso ao banco de dados do Azure para instâncias MariaDB, use marcas de serviço de rede virtual para definir controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Especificando o nome da marca de serviço (por exemplo, SQL. Westus) no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.
Observação: o banco de dados do Azure para MariaDB usa a marca de serviço "Microsoft. SQL".

Para obter mais informações sobre como usar marcas https://docs.microsoft.com/azure/virtual-network/service-tags-overview de serviço: compreenda o uso da marca de serviço para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados ao banco de dados do Azure para instâncias MariaDB com Azure Policy. Use Azure Policy aliases nos namespaces "Microsoft. DBforMariaDB" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do banco de dados do Azure para instâncias do MariaDB. Você também pode fazer uso de definições de política internas relacionadas à rede ou ao banco de dados do Azure para instâncias MariaDB, como:

- A Proteção contra DDoS Standard deve ser habilitada

- O ponto de extremidade privado deve ser habilitado para servidores MariaDB

- O servidor MariaDB deve usar um ponto de extremidade de serviço de rede virtual

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exemplos de rede:https://docs.microsoft.com/azure/governance/policy/samples/

Como criar um Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para suas instâncias de MariaDB para fornecer metadados e organização lógica.

Use qualquer uma das definições de política internas do Azure relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao banco de dados do Azure para instâncias MariaDB. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.
Como exibir e recuperar eventos do log de atividades do https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view Azure: como criar alertas no Azure monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como o banco de dados do Azure para MariaDB para carimbos de data/hora nos logs.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: Habilitar configurações de diagnóstico e logs de servidor e logs de ingestão para agregar dados de segurança gerados pelo banco de dado do Azure para instâncias MariaDB. Em Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.
Como configurar e acessar logs de servidor para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar os logs de auditoria do banco de dados do https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Azure para MariaDB: como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico no banco de dados do Azure para instâncias MariaDB para acesso a logs de auditoria, segurança e diagnóstico. Certifique-se de que você habilitou especificamente o log de auditoria do MariaDB. Logs de atividade, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para o mesmo espaço de trabalho Log Analytics ou conta de armazenamento.

Como configurar e acessar os logs de servidor do banco de dados do https://docs.microsoft.com/azure/mariadb/concepts-server-logs Azure para MariaDB: como configurar e acessar os logs de auditoria do https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal banco de dados do Azure para MariaDB: como definir as configurações de diagnóstico para o log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: dentro de Azure monitor, para o espaço de trabalho log Analytics que está sendo usado para armazenar o banco de dados do Azure para logs do MariaDB, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.
Como definir parâmetros de retenção de log para espaços de trabalho https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period de log Analytics: armazenando logs de recursos em uma conta de armazenamento do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analise e monitore logs de suas instâncias do MariaDB para um comportamento anormal. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log. Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Para obter mais informações sobre o espaço de trabalho Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Orientação**: habilitar a proteção avançada contra ameaças para MariaDB. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Além disso, você pode habilitar logs do servidor e configurações de diagnóstico para MariaDB e enviar logs para um espaço de trabalho Log Analytics. Integre seu espaço de trabalho Log Analytics ao Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Isso permite que os guias estratégicos (soluções automatizadas) sejam criados e usados para corrigir problemas de segurança.

Como habilitar a proteção avançada contra ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e acessar os logs do servidor para MariDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar os logs de auditoria do MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Diretrizes**: N/A; O MariaDB não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Diretrizes**: N/A; O MariaDB não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Diretrizes**: N/A; o parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Diretrizes**: Mantenha um inventário das contas de usuário que têm acesso administrativo ao plano de gerenciamento (portal do Azure/Azure Resource Manager) de suas instâncias do MariaDB. Além disso, mantenha um inventário das contas administrativas que têm acesso ao plano de dados de suas instâncias do MariaDB. (Ao criar o servidor MariaDB, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários MariaDB adicionais.)

Entender o gerenciamento de acesso para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Entenda as funções RBAC internas para assinaturas do Azure:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory não tem o conceito de senhas padrão.

Após a criação do recurso MariaDB, o Azure força a criação de um usuário administrativo com uma senha forte. No entanto, depois que a instância de MariaDB tiver sido criada, você poderá usar a primeira conta de administrador de servidor que criou conta para criar usuários adicionais e conceder acesso administrativo a eles. Ao criar essas contas, certifique-se de configurar uma senha forte diferente para cada conta.

Como criar contas adicionais para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às instâncias do MariaDB. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas.

Entenda a identidade e o acesso da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Orientação**: o acesso do plano de dados ao MariaDB é controlado por identidades armazenadas no banco de dado e não oferece suporte a SSO. O acesso do plano de controle para MariaDB está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações para um token Web JSON obtido de Azure Active Directory.

Entender a API REST do banco de dados do Azure para MariaDB:https://docs.microsoft.com/rest/api/mariadb/

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Diretrizes**: habilite o Azure ad MFA e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar a identidade e o acesso na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: habilite a proteção avançada contra ameaças para MariaDB para gerar alertas para atividades suspeitas.

Além disso, você pode usar Azure AD Privileged Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorre no ambiente. Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado.

Como configurar a proteção avançada contra ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como implantar Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões para limitar o acesso aos recursos do Azure, como MariaDB.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Use o AAD (Azure Active Directory) como o sistema de autenticação e autorização central. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também Salts, hashes e armazena com segurança as credenciais do usuário.

A autenticação do Azure AD não pode ser usada para acesso direto ao plano de dados MariaDB. no entanto, as credenciais do Azure AD podem ser usadas para administração no nível do plano de gerenciamento (por exemplo, o portal do Azure) para controlar contas de administrador do MariaDB.

Como atualizar a senha de administrador para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: examine os logs de Azure Active Directory para ajudar a descobrir contas obsoletas que podem incluir aquelas com funções administrativas MariaDB. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso a aplicativos corporativos que podem ser usados para acessar MariaDB e atribuições de função. O acesso do usuário deve ser revisado regularmente, como a cada 90 dias, para garantir que apenas os usuários certos tenham acesso contínuo.

Entender os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: habilite as configurações de diagnóstico para MariaDB e Azure Active Directory, enviando todos os logs para um espaço de trabalho log Analytics. Configure os alertas desejados (como tentativas de autenticação com falha) no espaço de trabalho Log Analytics.

Como configurar e acessar os logs do servidor para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar os logs de auditoria do MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como integrar os logs de atividades do Azure ao Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Orientação**: habilitar a proteção avançada contra ameaças para MariaDB. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Use a proteção de identidade e os recursos de detecção de riscos do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas. Você pode habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como habilitar a proteção avançada contra ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e habilitar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como exibir entradas arriscadas do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não aplicável; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o banco de dados do Azure para MariaDB.

Lista de serviços Sistema de Proteção de Dados do Cliente com suporte:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Orientação**: use marcas para auxiliar no rastreamento do banco de dados do Azure para instâncias MariaDB ou recursos relacionados que armazenam ou processam informações confidenciais.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Use uma combinação de link privado, pontos de extremidade de serviço e/ou regras de firewall MariaDB para isolar e limitar o acesso à rede às suas instâncias do MariaDB.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como configurar o link privado para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Como configurar pontos de extremidade de serviço para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar regras de firewall para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ao usar VMs do Azure para acessar instâncias do MariaDB, use o link privado, as configurações de rede do MariaDB, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente do MariaDB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Como mitigar o data vazamento para o banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o banco de dados do Azure para MariaDB dá suporte à conexão do banco de dados do Azure para o servidor MariaDB para aplicativos cliente usando TLS (segurança de camada de transporte), anteriormente conhecido como SSL (protocolo SSL). A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo. No portal do Azure, certifique-se de que "impor conexão SSL" esteja habilitado para todas as suas instâncias de MariaDB.

Como configurar a criptografia em trânsito para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: a identificação de dados, a classificação e os recursos de prevenção de perda ainda não estão disponíveis para o Azure Database para MariaDB. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Orientação**: Use o RBAC do Azure ad para controlar o acesso ao banco de dados do Azure para o plano de gerenciamento do MariaDB (portal do Azure/Azure Resource Manager). Para o acesso do plano de dados (dentro do próprio banco de dado), use consultas SQL para criar usuários e configurar permissões de usuário.

Como configurar o RBAC no Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Como configurar o acesso do usuário com o SQL para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Diretrizes**: N/A; esta diretriz destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente do MariaDB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Orientação**: o banco de dados do Azure para o serviço MariaDB usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Entenda a criptografia em repouso para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do banco de dados do Azure para MariaDB e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Diretrizes**: atualmente não disponível; A central de segurança do Azure ainda não dá suporte à avaliação de vulnerabilidade para o banco de dados do Azure para MariaDB Server.


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usar um processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao banco de dados do Azure para servidor MariaDB.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo o banco de dados do Azure para o servidor MariaDB) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Como criar consultas com o grafo de recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entender o RBAC do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas ao banco de dados do Azure para MariaDB Server e outros recursos relacionados, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear o banco de dados do Azure para o servidor MariaDB e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Orientação**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o grafo do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação e ao Azure como um todo.



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Orientação**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6,11: <div>Limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure por meio de scripts</div>

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure". Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança, como o banco de dados do Azure para MariaDB Server que contém informações confidenciais.

Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para o banco de dados do Azure para instâncias MariaDB com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. DBforMariaDB" para criar políticas personalizadas para auditar ou impor a configuração de rede do banco de dados do Azure para servidores MariaDB. Você também pode fazer uso de definições de política internas relacionadas ao banco de dados do Azure para servidores MariaDB, como:

- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB

Como exibir os aliases de Azure Policy disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entender Azure Policy efeitos:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições personalizadas de política do Azure para o banco de dados do Azure para servidores MariaDB e recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para o banco de dados do Azure para instâncias MariaDB e recursos relacionados.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar o banco de dados do Azure para servidores MariaDB, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o banco de dados do Azure para gerenciamento de segredos do MariaDB Server. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Como integrar com identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer Key Vault autenticação com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Diretrizes**: o banco de dados do Azure para MariaDB Server atualmente não dá suporte à autenticação Azure Active Directory para acessar bancos de dados.  Ao criar o banco de dados do Azure para o servidor MariaDB, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários MariaDB adicionais.  

Para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure App que está sendo usado para acessar o banco de dados do Azure para o servidor MariaDB, use Identidade de Serviço Gerenciada em conjunto com Azure Key Vault para armazenar e recuperar as credenciais do banco de dados do Azure para o servidor MariaDB.  Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código. Como configurar identidades gerenciadas https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm:. Como integrar com identidades gerenciadas do https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identityAzure:.



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

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure App Service), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, banco de dados do Azure para servidor MariaDB), no entanto, ele não é executado no conteúdo do cliente.

Pré-examinar qualquer conteúdo que está sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, banco de dados do Azure para servidor MariaDB, etc. A Microsoft não pode acessar seus dados nessas instâncias.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, banco de dados do Azure para servidor MariaDB), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Diretrizes**: o banco de dados do Azure para MariaDB usa backups completos, diferenciais e de log de transações.  O Banco de Dados do Azure para MariaDB cria backups de servidor automaticamente e os armazena no armazenamento com redundância local ou geográfica configurado pelo usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.  O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entender os backups do MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Entender a configuração inicial do MariaDB:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: o banco de dados do Azure para MariaDB cria automaticamente backups de servidor e os armazena em um armazenamento com redundância geográfica ou local configurado pelo usuário. Os backups podem ser usados para restaurar o servidor pontualmente.  Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

Se estiver usando Key Vault para criptografia de dados do lado do cliente para dados armazenados em seu servidor MariaDB, garanta backups regulares automatizados de suas chaves.

Entender os backups do MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Como fazer backup de chaves de Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: no banco de dados do Azure para MariaDB, execute uma restauração dos backups do servidor original para testes periódicos de backups. Há dois tipos de restauração disponíveis: restauração pontual e a restauração geográfica. A Restauração pontual está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original. A Restauração geográfica está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

Entenda o backup e a restauração no banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: o banco de dados do Azure para MariaDB usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entenda o backup e a restauração no banco de dados do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Use marcas para organizar os recursos do Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma entidade ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.
    

Como configurar a automação de fluxo de trabalho e os aplicativos lógicos:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [controle de segurança: testes de penetração e exercícios de equipe vermelhos](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas dentro de 60 dias

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
