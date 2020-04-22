---
title: Linha de base de segurança do Azure para banco de dados Azure para MariaDB
description: Linha de base de segurança do Azure para banco de dados Azure para MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9df0de443e28e0eea3224e42178eddd46108ea64
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759165"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Linha de base de segurança do Azure para banco de dados Azure para MariaDB

O Banco de Dados de Segurança do Azure para OZure para MariaDB contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Configure o Link Privado para Banco de Dados Azure para MariaDB com Pontos finais privados. O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Virtual privada (VNet). O tráfego entre sua rede virtual e a instância Do MariaDB viaja pela rede backbone da Microsoft.

Alternativamente, você pode usar os Pontos Finais do Serviço de Rede Virtual para proteger e limitar o acesso da rede ao seu Banco de Dados Azure para implementações do MariaDB. As regras de rede virtual são um recurso de segurança de firewall que controla se o banco de dados Do Azure para MariaDB aceita comunicações enviadas de sub-redes particulares em redes virtuais.

Você também pode proteger seu banco de dados do Azure para MariaDB com regras de firewall. O firewall do servidor impede todo o acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

Como configurar o Private Link for Azure Database para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como criar e gerenciar os pontos finais de serviço vnet e as regras do VNet no Banco de Dados Azure para servidor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar o Banco de Dados Azure para regras de firewall do MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Quando o banco de dados Azure para servidor MariaDB é protegido em um ponto final privado, você pode implantar máquinas virtuais na mesma rede virtual. Você pode usar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Habilite os registros de fluxo do NSG e envie logs em uma conta de armazenamento para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como configurar o Private Link for Azure Database para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como ativar registros de fluxo https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal de NSG: como ativar e usar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Use proteção avançada contra ameaças para o Banco de Dados Azure para MariaDB. O Advanced Threat Protection detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Habilite o Padrão de Proteção DDoS nas redes virtuais associadas ao banco de dados Do Azure para as instâncias do MariaDB para se proteger contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

Como configurar o Advanced Threat Protection for Azure Database para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: Quando o banco de dados Azure para servidor MariaDB é protegido em um ponto final privado, você pode implantar máquinas virtuais na mesma rede virtual. Em seguida, você pode configurar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Habilite os registros de fluxo do NSG e envie logs em uma conta de armazenamento para auditoria de tráfego. Você também pode enviar registros de fluxo do NSG para um espaço de trabalho do Log Analytics e usar o Traffic Analytics para fornecer insights sobre o fluxo de tráfego em sua nuvem Do Zure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças à segurança, entender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar registros de fluxo https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal de NSG: como ativar e usar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Use proteção avançada contra ameaças para o Banco de Dados Azure para MariaDB. O Advanced Threat Protection detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.
Como configurar o Advanced Threat Protection for Azure Database para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que precisam acessar seu banco de dados Do Azure para instâncias MariaDB, use tags de serviço de rede virtuais para definir controles de acesso à rede em grupos de segurança de rede ou firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Especificando o nome da tag de serviço (por exemplo, SQL. WestUs) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.
Nota: O Banco de Dados Azure para MariaDB usa a tag de serviço "Microsoft.Sql".

Para obter mais informações https://docs.microsoft.com/azure/virtual-network/service-tags-overview sobre o uso de tags de serviço: Entenda o uso da tag de serviço para o Banco de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados ao banco de dados Do Zure para instâncias Do Azure com a Política do Azure. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.DBforMariaDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do banco de dados Do Azure para instâncias do MariaDB. Você também pode fazer uso de definições de diretiva incorporadas relacionadas ao networking ou ao seu Banco de Dados Azure para instâncias Do MariaDB, tais como:

- A Proteção contra DDoS Standard deve ser habilitada

- Ponto final privado deve ser habilitado para servidores MariaDB

- O servidor MariaDB deve usar um ponto final de serviço de rede virtual

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política do Azure para rede:https://docs.microsoft.com/azure/governance/policy/samples/

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para recursos relacionados à segurança da rede e fluxo de tráfego para suas instâncias MariaDB para fornecer metadados e organização lógica.

Use qualquer uma das definições de política incorporadas do Azure relacionadas à marcação, como "Exigir tag e seu valor" para garantir que todos os recursos sejam criados com tags e notificá-lo dos recursos não marcados existentes.

Você pode usar o Azure PowerShell ou o Azure CLI para procurar ou executar ações com recursos com base em suas Tags.

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar configurações de recursos de rede e detectar alterações para recursos de rede relacionados ao banco de dados Do Azure para instâncias do MariaDB. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.
Como visualizar e recuperar eventos do https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view Azure Activity Log: Como criar alertas no Monitor do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo usada para os recursos do Azure, como o Azure Database for MariaDB para carimbos de tempo nos logs.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Habilite configurações de diagnóstico e logs de servidor e ingerir logs para agregar dados de segurança gerados pelo banco de dados Do Azure para instâncias Do MariaDB. No Azure Monitor, use o Log Analytics Workspace(s) para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros.
Como configurar e acessar logs de servidor para o Banco de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar registros de auditoria para o https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Azure Database para MariaDB: Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico no banco de dados do Azure para as instâncias do MariaDB para acesso a registros de auditoria, segurança e diagnóstico. Certifique-se de que você habilite especificamente o registro de auditoria do MariaDB. Os registros de atividades, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data, endereços de origem, endereços de destino e outros elementos úteis. Você também pode ativar as configurações de diagnóstico do Log de atividade do Azure e enviar os logs para o mesmo espaço de trabalho do Log Analytics ou da conta de armazenamento.

Como configurar e acessar logs de servidor para o https://docs.microsoft.com/azure/mariadb/concepts-server-logs Banco de Dados Do Azure para MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Como configurar e acessar logs de auditoria para o Banco de Dados Azure para MariaDB: Como configurar configurações de diagnóstico para o Log de Atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: Dentro do Azure Monitor, para que o Espaço de Trabalho do Log Analytics seja usado para manter seu banco de dados Azure para logs Do Azure, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.
Como definir parâmetros de retenção de https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period log para espaços de trabalho do Log Analytics: Armazenar logins de recursos em uma conta de armazenamento do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Analise e monitore registros de suas instâncias MariaDB para comportamento anômalo. Use o Espaço de Trabalho log analytics do Azure Monitor para revisar logs e executar consultas em dados de log. Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros.

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Para obter mais informações sobre o Espaço de Trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Monitor Do Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: Habilite a Proteção Avançada contra Ameaças para MariaDB. O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O VaDb detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Além disso, você pode habilitar logs de servidor e configurações de diagnóstico para MariaDB e enviar logs para um espaço de trabalho do Log Analytics. A bordo do Log Analytics Workspace para o Azure Sentinel, pois fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isso permite que as cartilhas (soluções automatizadas) sejam criadas e usadas para corrigir problemas de segurança.

Como ativar o Advanced Threat Protection for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e acessar logs de servidor para MariDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar registros de auditoria para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: N/A; O MariaDB não processa ou produz logs relacionados a malware.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: N/A; A MariaDB não processa ou produz registros relacionados ao DNS.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: N/A; benchmark destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: Manter um inventário das contas de usuário que tenham acesso administrativo ao plano de gerenciamento (Portal Azure/Azure Resource Manager) de suas instâncias MariaDB. Além disso, mantenha um inventário das contas administrativas que tenham acesso ao plano de dados de suas instâncias MariaDB. (Ao criar o servidor MariaDB, você fornece credenciais para um usuário administrador. Este administrador pode ser usado para criar usuários adicionais do MariaDB.)

Entenda a gestão de acesso para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Entenda as funções de RBAC incorporadas para assinaturas do Azure:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: O Azure Active Directory não tem o conceito de senhas padrão.

Após a criação do próprio recurso MariaDB, o Azure força a criação de um usuário administrativo com uma senha forte. No entanto, uma vez criada a instância Do MariaDB, você pode usar a primeira conta administrativa do servidor que você criou para criar usuários adicionais e conceder acesso administrativo a eles. Ao criar essas contas, certifique-se de configurar uma senha diferente e forte para cada conta.

Como criar contas adicionais para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas que tenham acesso às suas instâncias MariaDB. Use o Azure Security Center Identity e o gerenciamento de acesso para monitorar o número de contas administrativas.

Entenda a identidade e acesso do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: O acesso de avião de dados ao MariaDB é controlado por identidades armazenadas no banco de dados e não suporta SSO. O acesso ao plano de controle para MariaDB está disponível via API REST e suporta SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações em um Token Web JSON que você obtém do Azure Active Directory.

Entenda o Banco de Dados Azure para a API MariaDB REST:https://docs.microsoft.com/rest/api/mariadb/

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite o Azure AD MFA e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho de acesso privilegiado) com MFA configurado para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Habilite a Proteção Avançada contra Ameaças para o MariaDB para gerar alertas para atividades suspeitas.

Além disso, você pode usar o Azure AD Privileged Identity Management (PIM) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente. Use detecções de risco Azure AD para visualizar alertas e relatórios sobre comportamentos de usuários de risco.

Como configurar o Advanced Threat Protection for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como implantar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Use locais nomeados de acesso condicional para permitir o acesso a grupos lógicos específicos de faixas de endereços IP ou países/regiões para limitar o acesso aos recursos do Azure, como o MariaDB.

Como configurar locais nomeados no Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AAD) como sistema central de autenticação e autorização. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

A autenticação do Azure AD não pode ser usada para acesso direto ao plano de dados MariaDB, no entanto, as credenciais Azure AD podem ser usadas para administração no nível do plano de gerenciamento (por exemplo, o portal Azure) para controlar as contas administrativas do MariaDB.

Como atualizar a senha de admin para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: Revise os registros do Diretório Ativo do Azure para ajudar a descobrir contas obsoletas que podem incluir aquelas com funções administrativas do MariaDB. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos que podem ser usados para acessar o MariaDB e atribuições de função. O acesso do usuário deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os Usuários tenham acesso contínuo.

Entenda o relatório do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Habilite as Configurações de Diagnóstico para o Diretório Ativo Do Azure e do Azure, enviando todos os logs para um espaço de trabalho do Log Analytics. Configure os alertas desejados (como tentativas de autenticação falhadas) no Espaço de Trabalho do Log Analytics.

Como configurar e acessar logs de servidor para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e acessar registros de auditoria para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como integrar os Logs de Atividades do Azure no Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Habilite a Proteção Avançada contra Ameaças para MariaDB. O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O VaDb detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Use os recursos de proteção de identidade e detecção de risco do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas. Você pode habilitar respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como ativar o Advanced Threat Protection for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como ver os logins arriscados do Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Não aplicável; O Customer Lockbox ainda não foi suportado para o Azure Database for MariaDB.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags para ajudar no rastreamento do Banco de Dados Azure para instâncias do MariaDB ou recursos relacionados que armazenam ou processam informações confidenciais.

Como criar e usar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Implementar assinaturas separadas e/ou grupos de gerenciamento para desenvolvimento, teste e produção. Use uma combinação de regras de firewall Private Link, Service Endpoints e/ou MariaDB para isolar e limitar o acesso à rede às instâncias do MariaDB.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como configurar o Private Link for Azure Database para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Como configurar pontos finais de serviço para o Banco de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar regras de firewall para o Banco de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Ao usar as VMs do Azure para acessar as instâncias do MariaDB, faça uso do Private Link, configurações de rede MariaDB, Grupos de Segurança de Rede e Etiquetas de Serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gerencia a infra-estrutura subjacente ao MariaDB e implementou controles rigorosos para evitar a perda ou exposição dos dados dos clientes.

Como mitigar a exfiltração de dados para o Banco de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: O Banco de Dados Azure para MariaDB suporta conectar seu banco de dados Azure para servidor MariaDB a aplicativos clientes usando o Secure Sockets Layer (SSL). Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. No portal Azure, certifique-se de que a "Conexão SSL imposta" esteja habilitada para todas as suas instâncias Do MariaDB.

Como configurar a criptografia em trânsito para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Os recursos de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Banco de Dados Azure para MariaDB. Implementar solução de terceiros, se necessário para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Use o Azure AD RBAC para controlar o acesso ao Banco de Dados Do Azure para o plano de gerenciamento do MariaDB (Portal Azure/Azure Resource Manager). Para acesso a plano de dados (dentro do próprio banco de dados), use consultas SQL para criar usuários e configurar permissões de usuário.

Como configurar o RBAC no Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Como configurar o acesso do usuário com SQL para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: N/A; esta diretriz destina-se a recursos computacionais.

A Microsoft gerencia a infra-estrutura subjacente ao MariaDB e implementou controles rigorosos para evitar a perda ou exposição dos dados dos clientes.

Entenda a proteção de dados do cliente no Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: O banco de dados Azure para o serviço MariaDB usa o módulo criptográfico validado fips 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados em disco, com exceção de arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Entenda a criptografia em repouso para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use o Monitor Do Azure com o Registro de Atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de produção do Banco de Dados Azure para MariaDB e outros recursos críticos ou relacionados.

Como criar alertas para eventos do Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: Atualmente não disponível; O Azure Security Center ainda não suporta avaliação de vulnerabilidade sustais para o servidor MariaDB.


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

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

**Orientação**: A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Banco de Dados Azure para o servidor MariaDB.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar e descobrir todos os recursos (incluindo o Banco de Dados Azure para servidor MariaDB) dentro de sua assinatura(s). Certifique-se de ter permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Como criar consultas com o Gráfico de Recursos do Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver suas assinaturas do Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags no Banco de Dados Azure para servidor MariaDB e outros recursos relacionados que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear o Banco de Dados Do Azure para servidor MariaDB e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais e Azure como um todo.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

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

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com o Azure Resources Manager através de scripts</div>

**Orientação**: Use o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management". Isso pode impedir a criação e alterações de recursos em um ambiente de alta segurança, como o Banco de Dados Azure para servidor MariaDB contendo informações confidenciais.

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

**Orientação**: Defina e implemente configurações de segurança padrão para o banco de dados Do Azure para instâncias MariaDB com a Política do Azure. Use aliases da Diretiva Azure no namespace "Microsoft.DBforMariaDB" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Banco de Dados Azure para servidores MariaDB. Você também pode fazer uso de definições de diretiva incorporadas relacionadas ao seu Banco de Dados Azure para servidores MariaDB, tais como:

- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB

Como visualizar aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições de diretiva personalizadas do Azure para o banco de dados Do Azure para servidores MariaDB e recursos relacionados, use os Repos do Azure para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negue], e [implantar se não existir] para aplicar automaticamente configurações para o seu Banco de Dados Azure para instâncias do MariaDB e recursos relacionados.

Como configurar e gerenciar a política do Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Para máquinas virtuais do Azure ou aplicativos da Web em execução no Azure App Service sendo usados para acessar seu banco de dados Azure para servidores MariaDB, use identidade de serviço gerenciada em conjunto com o Azure Key Vault para simplificar e proteger o Banco de Dados Azure para gerenciamento secreto do servidor MariaDB. Certifique-se de que o Key Vault Soft Delete esteja ativado.

Como se integrar com as identidades gerenciadas do Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: O banco de dados Azure para servidor MariaDB atualmente não suporta autenticação do Azure Active Directory para acessar bancos de dados.  Ao criar o Banco de Dados Azure para o servidor MariaDB, você fornece credenciais para um usuário administrador. Este administrador pode ser usado para criar usuários adicionais do MariaDB.  

Para que as máquinas virtuais do Azure ou aplicativos da Web em execução no Azure App Service sejam usados para acessar seu banco de dados Azure para servidor MariaDB, use a Identidade de Serviço Gerenciada em conjunto com o Azure Key Vault para armazenar e recuperar credenciais para o Banco de Dados Azure para o servidor MariaDB.  Certifique-se de que o Key Vault Soft Delete esteja ativado.

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código. Como configurar identidades gerenciadas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm. Como se integrar com as identidades https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identitygerenciadas do Azure: .



**Monitoramento do Azure Security Center**: Não aplicável

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

**Orientação**: O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Banco de Dados Azure para servidor MariaDB), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie qualquer conteúdo que seja carregado para recursos não computados do Azure, como Serviço de Aplicativo, Armazenamento de Data Lake, Armazenamento Blob, Banco de Dados Azure para servidor MariaDB, etc. A Microsoft não pode acessar seus dados nessas instâncias.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.

O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, o Banco de Dados Azure para servidor MariaDB), no entanto, ele não é executado no conteúdo do cliente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: O Banco de Dados Azure para MariaDB recebe backups completos, diferenciais e de registro de transações.  O Banco de Dados do Azure para MariaDB cria backups de servidor automaticamente e os armazena no armazenamento com redundância local ou geográfica configurado pelo usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.  O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entenda backups para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Entenda a configuração inicial do MariaDB:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: O Banco de Dados Azure para MariaDB cria automaticamente backups de servidor e os armazena no armazenamento localmente redundante ou geo-redundante. Os backups podem ser usados para restaurar o servidor pontualmente.  Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

Se usar o Key Vault para criptografia de dados do lado do cliente para dados armazenados em seu servidor MariaDB, certifique-se de backups automatizados regulares de suas chaves.

Entenda backups para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Como fazer backup das chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: No Banco de Dados Azure para MariaDB, execute uma restauração dos backups do servidor original para testes periódicos de backups. Existem dois tipos de restauração disponíveis: restauração point-in-time e georestauração. A Restauração pontual está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original. A Restauração geográfica está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

Entenda backup e restauração no Banco de Dados Do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: O Banco de Dados Azure para MariaDB recebe backups completos, diferenciais e de registro de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entenda backup e restauração no Banco de Dados Do Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Use tags para organizar seus recursos do Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realize exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada. Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Azure Security Center usando o recurso Exportação Contínua para ajudar a identificar riscos aos recursos do Azure. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas para o Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas de segurança e recomendações para proteger seus recursos do Azure.
    

Como configurar aplicativos de automação e lógica de fluxo de trabalho:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta a remediação de todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia e execução do Red Teaming e testes de penetração de sites ao vivo contra infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
