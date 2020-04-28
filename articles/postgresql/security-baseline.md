---
title: Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL servidor único
description: Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL servidor único
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4a94b0b84290bbf796e951dfee8b3f35b80036ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183153"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL servidor único

O servidor único da linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral de linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Orientação**: configurar o link privado para o banco de dados do Azure para PostgreSQL com pontos de extremidade privados. O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O link privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (rede virtual privada). O tráfego entre sua rede virtual e a instância PostgreSQL viaja pela rede de backbone da Microsoft.

Como alternativa, você pode usar pontos de extremidade de serviço de rede virtual para proteger e limitar o acesso à rede para as implementações do banco de dados do Azure para PostgreSQL. As regras da rede virtual são um recurso de segurança de firewall que controla se o servidor do Banco de Dados do Azure para PostgreSQL aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais.

Você também pode proteger seu banco de dados do Azure para servidor PostgreSQL com regras de firewall. O Firewall do servidor impede todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

Como configurar o link privado para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Como criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Como configurar as regras de firewall do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: quando a instância do banco de dados do Azure para PostgreSQL é protegida a um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Como configurar o link privado para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar Análise de Tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: Use a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL. A proteção avançada contra ameaças detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas ao banco de dados do Azure para instâncias do PostgreSQL para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Como configurar a proteção contra DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: quando a instância do banco de dados do Azure para PostgreSQL é protegida a um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Em seguida, você pode configurar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Como habilitar os logs de fluxo do NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar Análise de Tráfego:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL. A proteção avançada contra ameaças detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Orientação**: para recursos que precisam de acesso às instâncias do banco de dados do Azure para PostgreSQL, use marcas de serviço de rede virtual para definir controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Especificando o nome da marca de serviço (por exemplo, SQL. Westus) no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

Observação: o banco de dados do Azure para PostgreSQL usa a marca de serviço "Microsoft. SQL".

Para obter mais informações sobre como usar marcas de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Entender o uso da marca de serviço para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados às instâncias do banco de dados do Azure para PostgreSQL com Azure Policy. Use Azure Policy aliases nos namespaces "Microsoft. DBforPostgreSQL" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do banco de dados do Azure para PostgreSQL. Você também pode fazer uso de definições de política internas relacionadas à rede ou ao banco de dados do Azure para instâncias do PostgreSQL, como:

- A Proteção contra DDoS Standard deve ser habilitada

- Impor a conexão TLS deve ser habilitada para servidores de banco de dados PostgreSQL

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exemplos de rede:https://docs.microsoft.com/azure/governance/policy/samples/

Como criar um Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para as instâncias do banco de dados do Azure para PostgreSQL para fornecer metadados e organização lógica.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às instâncias do banco de dados do Azure para PostgreSQL. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como o banco de dados do Azure para PostgreSQL para carimbos de data/hora nos logs.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: Habilitar configurações de diagnóstico e logs de servidor e logs de ingestão para agregar dados de segurança gerados por instâncias do banco de dado do Azure para PostgreSQL. Em Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico em suas instâncias do banco de dados do Azure para PostgreSQL para acesso a logs de auditoria, segurança e recursos. Certifique-se de que você habilitou especificamente o log de auditoria do PostgreSQL. Logs de atividade, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para o mesmo espaço de trabalho Log Analytics ou conta de armazenamento.

Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Como definir configurações de diagnóstico para o log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: em Azure monitor, para o espaço de trabalho log Analytics que está sendo usado para armazenar os logs do banco de dados do Azure para PostgreSQL, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para Log Analytics espaços de trabalho:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Armazenando logs de recursos em uma conta de armazenamento do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analise e monitore logs de suas instâncias do banco de dados do Azure para PostgreSQL para comportamento anormal. Use Log Analytics Azure Monitor para examinar os logs e executar consultas nos dados de log. Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Para obter mais informações sobre o Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Orientação**: habilitar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL. A proteção avançada contra ameaças detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Além disso, você pode habilitar logs do servidor e configurações de diagnóstico para PostgreSQL e enviar logs para um espaço de trabalho Log Analytics. Integre seu espaço de trabalho Log Analytics ao Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Isso permite que os guias estratégicos (soluções automatizadas) sejam criados e usados para corrigir problemas de segurança.

Como habilitar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Como definir configurações de diagnóstico para o log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável; O banco de dados do Azure para PostgreSQL não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável; O banco de dados do Azure para PostgreSQL não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Diretrizes**: Mantenha um inventário das contas de usuário que têm acesso administrativo ao plano de controle (por exemplo, portal do Azure) de suas instâncias do banco de dados do Azure para PostgreSQL. Além disso, mantenha um inventário das contas administrativas que têm acesso ao plano de dados (dentro do próprio banco de dado) de suas instâncias do banco do dados do Azure para PostgreSQL. (Ao criar o servidor PostgreSQL, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários adicionais do PostgreSQL.)

O banco de dados do Azure para PostgreSQL não dá suporte ao controle de acesso baseado em função interno, mas você pode criar funções personalizadas com base em operações específicas do provedor de recursos.

Entender as funções personalizadas da assinatura do Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Entender as operações do provedor de recursos do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Compreenda o gerenciamento de acesso do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: o Azure Active Directory e o banco de dados do Azure para PostgreSQL não têm o conceito de senhas padrão.

Após a criação do recurso de banco de dados do Azure para PostgreSQL em si, o Azure força a criação de um usuário administrativo com uma senha forte. No entanto, depois que a instância PostgreSQL tiver sido criada, você poderá usar a primeira conta de administrador de servidor que criou conta para criar usuários adicionais e conceder acesso administrativo a eles. Ao criar essas contas, certifique-se de configurar uma senha forte diferente para cada conta.

Como criar contas adicionais para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

Como atualizar a senha do administrador:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às instâncias do banco de dados do Azure para PostgreSQL. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas. 

Entenda a identidade e o acesso da central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Entenda como criar usuários administradores no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Orientação**: entrar no banco de dados do Azure para PostgreSQL tem suporte usando o nome de usuário/senha configurados diretamente no banco de dados, bem como usando uma identidade de Azure Active Directory (AD) e utilizando um token do Azure ad para se conectar. Ao usar um token do Azure AD, há suporte para métodos diferentes, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados.

Separadamente, o acesso ao plano de controle para PostgreSQL está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações para um token Web JSON obtido de Azure Active Directory.

Use Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Entender a API REST do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/rest/api/postgresql/

Entenda o SSO com o Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator (MFA) do Azure Active Directory e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure. Ao utilizar tokens do Azure AD para entrar no banco de dados, isso permite que você exija a autenticação multifator para entradas de banco de dados.

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Use Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Como monitorar a identidade e o acesso na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: habilitar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL para gerar alertas para atividades suspeitas.

Além disso, você pode usar o Azure Active Directory (AD) Privileged Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorre no ambiente.

Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado.

Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Como implantar Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o portal e o acesso a Azure Resource Manager apenas de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

Para entrar no banco de dados do Azure para PostgreSQL, é recomendável usar o Azure AD e usar um token do Azure AD para se conectar. Ao usar um token do Azure AD, há suporte para métodos diferentes, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados.

As credenciais do Azure AD também podem ser usadas para administração no nível do plano de gerenciamento (por exemplo, o portal do Azure) para controlar as contas de administrador do PostgreSQL.

Use Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: examine os logs de Azure Active Directory para ajudar a descobrir contas obsoletas que podem incluir aquelas com as funções administrativas do banco de dados do Azure para PostgreSQL. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso a aplicativos corporativos que podem ser usados para acessar o banco de dados do Azure para PostgreSQL e atribuições de função. O acesso do usuário deve ser revisado regularmente, como a cada 90 dias, para garantir que apenas os usuários certos tenham acesso contínuo.

Entender os relatórios do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Examine os usuários do PostgreSQL e as funções atribuídas:https://www.postgresql.org/docs/current/database-roles.html

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Orientação**: Habilitar configurações de diagnóstico para o banco de dados do Azure para PostgreSQL e Azure Active Directory, enviando todos os logs para um espaço de trabalho log Analytics. Configure os alertas desejados (como tentativas de autenticação com falha) em Log Analytics.

Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Como integrar os logs de atividades do Azure ao Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Orientação**: habilitar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL para gerar alertas para atividades suspeitas.

Use a proteção de identidade e os recursos de detecção de riscos do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas. Você pode habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir os logs no Azure Sentinel para uma investigação mais aprofundada.

Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Visão geral do Azure AD Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Como exibir entradas arriscadas do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Como carregar o Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o banco de dados do Azure para PostgreSQL.

Lista de serviços Sistema de Proteção de Dados do Cliente com suporte:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de instâncias do banco de dados do Azure para PostgreSQL ou recursos relacionados que armazenam ou processam informações confidenciais.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Use uma combinação de link privado, pontos de extremidade de serviço e/ou regras de firewall para isolar e limitar o acesso à rede para as instâncias do banco de dados do Azure para PostgreSQL.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento:https://docs.microsoft.com/azure/governance/management-groups/create

Como configurar o link privado para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Como criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Como configurar as regras de firewall do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ao usar as máquinas virtuais do Azure para acessar instâncias do banco de dados do Azure para PostgreSQL, use o link privado, as configurações de rede do PostgreSQL, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente para o banco de dados do Azure para PostgreSQL e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Como mitigar o data vazamento para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o banco de dados do Azure para PostgreSQL dá suporte à conexão do servidor PostgreSQL a aplicativos cliente usando TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL). A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo. No portal do Azure, certifique-se de que "impor conexão SSL" esteja habilitado para todas as instâncias do banco de dados do Azure para PostgreSQL por padrão.

Atualmente, a versão do TLS com suporte para o banco de dados do Azure para PostgreSQL é TLS 1,0, TLS 1,1, TLS 1,2.

Como configurar a criptografia em trânsito para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: a identificação de dados, a classificação e os recursos de prevenção de perda ainda não estão disponíveis para o Azure Database para PostgreSQL. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Orientação**: Use o RBAC (controle de acesso baseado em função) do Azure para controlar o acesso ao banco de dados do Azure para o plano de controle PostgreSQL (por exemplo, portal do Azure). Para o acesso do plano de dados (dentro do próprio banco de dado), use consultas SQL para criar usuários e configurar permissões de usuário. O RBAC não afeta as permissões de usuário no banco de dados.

Como configurar o RBAC no Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Como configurar o acesso do usuário com o SQL para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o banco de dados do Azure para PostgreSQL e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entender a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Orientação**: o serviço banco de dados do Azure para PostgreSQL usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

A criptografia de dados com chaves gerenciadas pelo cliente (CMK) do banco de dados do Azure para PostgreSQL Single Server permite que você traga sua própria chave (BYOK) para a proteção em repouso. Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em contato com:

AskAzureDBforPostgreSQL@service.microsoft.com.

Entenda a criptografia em repouso para o banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security

Entenda a criptografia em repouso para o banco de dados do Azure para PostgreSQL usando chaves gerenciadas pelo cliente:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do banco de dados do Azure para PostgreSQL e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Diretrizes**: atualmente não disponível; A central de segurança do Azure ainda não dá suporte à avaliação de vulnerabilidade para o banco de dados do Azure para PostgreSQL.

Cobertura de recurso para serviços de PaaS do Azure na central de segurança do Azure:https://docs.microsoft.com/azure/security-center/features-paas

**Monitoramento da central de segurança do Azure**: não disponível no momento

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

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao banco de dados do Azure para PostgreSQL.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo as instâncias do banco de dados do Azure para PostgreSQL) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Como criar consultas com o grafo de recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entender o RBAC do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Orientação**: aplique marcas às instâncias do banco de dados do Azure para PostgreSQL e outros recursos relacionados, fornecendo metadados para organizá-las logicamente em uma taxonomia.

Como criar e usar marcas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar as instâncias do banco de dados do Azure para PostgreSQL e os recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

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

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

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

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

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

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure por meio de scripts

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure". Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança, como instâncias do banco de dados do Azure para PostgreSQL contendo informações confidenciais.

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

**Diretrizes**: defina e implemente configurações de segurança padrão para as instâncias do banco de dados do Azure para PostgreSQL com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. DBforPostgreSQL" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do banco de dados do Azure para PostgreSQL. Você também pode fazer uso de definições de política internas relacionadas às instâncias do banco de dados do Azure para PostgreSQL, como:

- Impor a conexão TLS deve ser habilitada para servidores de banco de dados PostgreSQL

- As conexões de log devem ser habilitadas para os servidores de banco de dados PostgreSQL

Como exibir os aliases de Azure Policy disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entender Azure Policy efeitos:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições de Azure Policy personalizadas para as instâncias do banco de dados do Azure para PostgreSQL e recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DBforPostgreSQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. DBforPostgreSQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para as instâncias do banco de dados do Azure para PostgreSQL e recursos relacionados.

Como configurar e gerenciar Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar as instâncias do banco de dados do Azure para PostgreSQL, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos do banco de dados do Azure para PostgreSQL. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Como integrar com identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer Key Vault autenticação com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Diretrizes**: o servidor de banco de dados do Azure para PostgreSQL dá suporte à autenticação Azure Active Directory (em versão prévia) para acessar bancos de dados.  Ao criar o banco de dados do Azure para o servidor PostgreSQL, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários de banco de dados adicionais.  

Para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure App que está sendo usado para acessar o servidor do banco de dados do Azure para PostgreSQL, use Identidade de Serviço Gerenciada em conjunto com Azure Key Vault para armazenar e recuperar credenciais do banco de dados do Azure para o servidor PostgreSQL. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

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

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure App Service), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, banco de dados do Azure para PostgreSQL), no entanto, ele não é executado no conteúdo do cliente.

Examine previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, banco de dados do Azure para PostgreSQL etc. A Microsoft não pode acessar seus dados nessas instâncias.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, banco de dados do Azure para PostgreSQL), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Diretrizes**: o banco de dados do Azure para PostgreSQL faz backups dos arquivos de data e do log de transações. Dependendo do tamanho máximo de armazenamento com suporte, pegamos backups totais e diferenciais (servidores de armazenamento máximo de 4 TB) ou backups de instantâneo (até 16 TB de servidores de armazenamento máximo). Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Entender a configuração inicial do banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: o banco de dados do Azure para PostgreSQL cria automaticamente backups de servidor e os armazena no armazenamento com redundância local ou com redundância geográfica, de acordo com a opção do usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

Se estiver usando Azure Key Vault para armazenar credenciais para as instâncias do banco de dados do Azure para PostgreSQL, garanta backups regulares automatizados de suas chaves.

Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Como fazer backup de chaves de Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: no banco de dados do Azure para PostgreSQL, a execução de uma restauração cria um novo servidor a partir dos backups do servidor original. Há dois tipos de restauração disponíveis: restauração pontual e a restauração geográfica. A Restauração pontual está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original. A Restauração geográfica está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

Teste periodicamente a restauração de suas instâncias do banco de dados do Azure para PostgreSQL.

Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: o banco de dados do Azure para PostgreSQL usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entenda o backup e a restauração no banco de dados do Azure para PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Monitoramento da central de segurança do Azure**: Sim

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

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

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
