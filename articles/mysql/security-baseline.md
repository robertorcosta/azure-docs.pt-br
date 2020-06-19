---
title: Linha de base de segurança do Azure para o Banco de Dados do Azure para MySQL
description: Linha de base de segurança do Azure para o Banco de Dados do Azure para MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 02050745e7c8a9aa05d3f2de63a4bc5f1ebf8318
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654780"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Linha de base de segurança do Azure para o Banco de Dados do Azure para MySQL

A linha de base de segurança do Azure para o Banco de Dados do Azure para MySQL contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretriz**: configure o Link Privado para o Banco de Dados do Azure para MySQL com pontos de extremidade privados. O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O Link Privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (Rede Virtual privada). O tráfego entre sua rede virtual e a instância do MySQL viaja pela rede de backbone da Microsoft.

Uma alternativa é o uso dos Pontos de Extremidade de Serviço de Rede Virtual para proteger e limitar o acesso da rede às implementações do seu Banco de Dados do Azure para MySQL. As regras da rede virtual são um recurso de segurança de firewall que controla se o servidor do Banco de Dados do Azure para MySQL aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais.

Você também pode proteger seu servidor de Banco de Dados do Azure para MySQL com regras de firewall. O firewall do servidor impede todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

Como configurar o Link Privado para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Como criar e gerenciar pontos de extremidade de serviços e regras do VNet no Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Como configurar regras de firewall do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorar e registrar a configuração e o tráfego de VNets, sub-redes e NICs

**Diretriz**: quando a instância do Banco de Dados do Azure para MySQL é protegida para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Use um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Como configurar o Link Privado para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Como habilitar logs de fluxo de NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretriz**: use a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Habilite a Proteção contra DDoS Padrão nas redes virtuais do Azure associadas às suas instâncias do Banco de Dados do Azure para MySQL para proteção contra ataques de negação de serviço distribuído. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Como configurar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Como configurar a proteção contra DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrar pacotes de rede e logs de fluxo

**Diretriz**: quando a instância do Banco de Dados do Azure para MySQL é protegida para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Em seguida, você pode configurar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Como habilitar logs de fluxo de NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

**Diretriz**: use a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Como configurar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretriz**: para recursos que precisam de acesso às instâncias do Banco de Dados do Azure para MySQL, use as marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no Firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, SQL.WestUs) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Observação: o Banco de Dados do Azure para MySQL usa a marca de serviço “Microsoft.Sql”.

Para obter mais informações sobre como usar as marcas de serviço: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Entenda o uso de marcas de serviço para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretriz**: defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados às instâncias do Banco de Dados do Azure para MySQL com o Azure Policy. Use aliases do Azure Policy nos namespaces “Microsoft.DBforMySQL” e “Microsoft.Network” para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do Banco de Dados do Azure para MySQL. Você também pode usar as definições de política integradas relacionadas à rede ou às instâncias do Banco de Dados do Azure para MySQL, como:

- A Proteção contra DDoS Standard deve ser habilitada

- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras do Azure Policy para rede: https://docs.microsoft.com/azure/governance/policy/samples/

Como criar um Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretriz**: use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para as instâncias do Banco de Dados do Azure para MySQL para fornecer metadados e organização lógica.

Use qualquer uma das definições do Azure Policy integrado relacionadas à marcação, como “Exigir marca e seu valor” para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretriz**: use o Log de Atividades do Azure para monitorar configurações de recursos de rede e detectar alterações de recursos de rede relacionadas às suas instâncias do Banco de Dados do Azure para MySQL. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do Log de Atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretriz**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como o Banco de Dados do Azure para MySQL para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretriz**: habilite as configurações de diagnóstico e os logs de servidor e os logs de ingestão para agregar dados de segurança gerados pelas suas instâncias do Banco de Dados do Azure para MySQL. No Azure Monitor, use o Workspace do Log Analytics para consultar e realizar análises, e use contas de Armazenamento do Microsoft Azure para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Entenda os logs do servidor para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretriz**: habilite as configurações de diagnóstico em suas instâncias do Banco de Dados do Azure para MySQL para acesso a logs de auditorias, consulta lenta e métricas do MySQL. Verifique se você habilitou especificamente o log de auditoria do MySQL. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para o mesmo workspace do Log Analytics ou conta de armazenamento.

Entenda os logs do servidor para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Como configurar e acessar os logs de consulta lenta para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Como definir as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, para o workspace do Log Analytics que está sendo usado para armazenar os logs do Banco de Dados do Azure para MySQL, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para workspaces do Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Armazenar logs de recursos em uma conta de Armazenamento do Microsoft Azure: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretriz**: analise e monitore logs de suas instâncias do Banco de Dados do Azure para MySQL para comportamento anormal. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Para obter mais informações sobre o Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal

**Diretriz**: habilite a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Além disso, você pode habilitar os logs do servidor e as configurações de diagnóstico do MySQL e enviar os logs para um workspace do Log Analytics. Integre seu workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução SOAR (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança.

Como habilitar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL (versão prévia): https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Entenda os logs do servidor para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Como configurar e acessar os logs de consulta lenta para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Como definir as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretriz**: não aplicável; o Banco de Dados do Azure para MySQL não processa nem produz logs relacionados a antimalware.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretriz**: não aplicável; o Banco de Dados do Azure para MySQL não processa nem produz logs relacionados ao DNS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretriz**: mantenha um inventário das contas de usuário que têm acesso administrativo ao plano de gerenciamento (por exemplo, portal do Azure) de suas instâncias do Banco de Dados do Azure para MySQL. Além disso, mantenha um inventário das contas administrativas que têm acesso ao plano de dados (no próprio banco de dados) das suas instâncias do Banco de Dados do Azure para MySQL. (Você deve fornecer credenciais para um usuário administrador ao criar o servidor MySQL. Tal administrador pode ser usado para criar usuários adicionais do MySQL.)

O Banco de Dados do Azure para MySQL não dá suporte ao controle de acesso baseado em função interna, mas você pode criar funções personalizadas com base em opções específicas do provedor de recursos.

Entenda as funções personalizadas da assinatura do Azure: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Entenda as operações do provedor de recursos do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Entenda o gerenciamento de acesso ao Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretriz**: O Azure AD não tem o conceito de senhas padrão.

Após a criação do Banco de Dados do Azure para MySQL em si, o Azure força a criação de um usuário administrativo com uma senha forte. No entanto, depois que a instância do MySQL for criada, você poderá usar a primeira conta de administrador do servidor que você criou para criar usuários adicionais e conceder acesso administrativo a eles. Configure uma senha forte diferente para cada conta ao criá-las.

Como criar contas adicionais para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

Como atualizar a senha de administrador: https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às suas instâncias do Banco de Dados do Azure para MySQL. Use a identidade da Central de Segurança do Azure e o gerenciamento de acesso para monitorar a quantidade de contas administrativas.

Entenda a identidade e o acesso à Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Entenda como criar usuários administrativos no Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretriz**: entre no Banco de Dados do Azure para MySQL é possível usando o nome de usuário/senha configurados diretamente no banco de dados, bem como usando uma identidade de Azure Active Directory (AD) e um token do Azure Active Directory para se conectar. Métodos diferentes são compatíveis ao usar um token do Azure AD, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados.

Separadamente, o acesso ao plano de controle para MySQL está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações como um Token Web JSON que você obtém no Azure Active Directory.

Usar o Azure Active Directory para autenticação com o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Entenda o API REST do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/rest/api/mysql/

Entenda SSO com o Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretriz**: habilite a autenticação multifator (MFA) do Azure Active Directory e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure. Ao utilizar tokens do Azure AD para entrar no banco de dados, isso permite que você exija a autenticação multifator para entradas de banco de dados.

Como habilitar a MFA no Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Usar o Azure Active Directory para autenticação com o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Como monitorar identidade e acesso na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretriz**: use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como habilitar a MFA no Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretriz**: habilitar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL para gerar alertas de atividades suspeitas.

Além disso, você pode usar o PIM (Privileged Identity Management) do Azure AD para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito.

Como configurar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Como implantar o PIM: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: use localizações nomeadas de acesso condicional para permitir o acesso ao portal e ao Azure Resource Manager somente para agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

Como configurar localizações nomeadas no Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretriz**: use o Azure Active Directory (AD) como o sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Para entrar no Banco de Dados do Azure para MySQL, é recomendável usar o Azure AD e usar um token do Azure AD para se conectar. Métodos diferentes são compatíveis ao usar um token do Azure AD, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados. 

Para controlar as contas de administrador do MySQL, as credenciais do Azure AD também podem ser usadas para administração no nível do plano de gerenciamento (por exemplo, o portal do Azure).

Usar o Azure Active Directory para autenticação com o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretriz**: revise os logs de Azure Active Directory para ajudar a descobrir contas obsoletas que podem incluir aquelas com funções administrativas do Banco de Dados do Azure para MySQL. Além disso, use as revisões de acesso de identidade do Azure para gerenciar de forma eficiente associações de grupo, o acesso aos aplicativos empresariais que podem ser usados para acessar o Banco de Dados do Azure para MySQL e as atribuições de função. O acesso de usuários deve ser revisado regularmente, a cada 90 dias, para garantir que somente os usuários corretos tenham acesso contínuo.

Entenda os relatórios do Azure AD https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas

**Diretriz**: habilite as configurações de diagnóstico do Banco de Dados do Azure para MySQL e do Azure Active Directory, enviando todos os logs para um workspace do Log Analytics. Configure os alertas desejados (como tentativas de autenticação com falha) no Log Analytics.

Como configurar e acessar os logs de consulta lenta para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Como configurar e acessar os logs de auditoria para Banco de Dados do Azure para MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Como integrar os logs de atividades do Azure ao Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretriz**: habilitar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL para gerar alertas de atividades suspeitas.

Use os recursos da Azure Active Directory Identity Protection e de detecção de riscos do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas. Você pode habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir logs no Azure Sentinel para uma investigação mais aprofundada.

Como configurar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Visão geral do Azure AD Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Como exibir entradas suspeitas do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretriz**: não disponível; o Sistema de Proteção de Dados do Cliente ainda não é suportado no Banco de Dados do Azure para MySQL.

Lista de serviços suportados do Sistema de Proteção de Dados do Cliente: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretriz**: use marcas para auxiliar no rastreamento de instâncias do Banco de Dados do Azure para MySQL ou recursos relacionados que armazenam ou processam informações confidenciais.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Use uma combinação de link privado, pontos de extremidade de serviço e/ou regras de firewall para isolar e limitar o acesso à rede para suas instâncias do Banco de Dados do Azure para MySQL.

Como criar assinaturas adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: https://docs.microsoft.com/azure/governance/management-groups/create

Como configurar o Link Privado para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Como criar e gerenciar pontos de extremidade de serviços e regras do VNet no Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Como configurar regras de firewall do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretriz**: Ao usar as VMs do Azure para acessar instâncias do Banco de Dados do Azure para MySQL, use o link privado, as configurações de rede do MySQL, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente para o Banco de Dados do Azure para MySQL e implementou controles rígidos para evitar a perda ou a exposição dos dados do cliente.

Como atenuar o vazamento de dados do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Entenda a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretriz**: O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor do MySQL com aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. No portal do Azure, certifique-se de que “Impor conexão SSL” esteja habilitado para todas as instâncias do Banco de Dados do Azure para MySQL por padrão.

Atualmente, a versão do TLS com suporte para o Banco de Dados do Azure para MySQL é TLS 1.0, TLS 1.1, TLS 1.2.

Como configurar a criptografia em trânsito para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretriz**: Os recursos de identificação de dados, classificação e prevenção de perdas ainda não estão disponíveis para o Banco de Dados do Azure para MySQL. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entenda a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Diretriz**: use o RBAC (controle de acesso baseado em função) do Azure para controlar o acesso ao plano de controle do Banco de Dados do Azure para MySQL (por exemplo, o portal do Azure). Para o acesso do plano de dados (dentro do próprio banco de dados), use consultas SQL para criar usuários e configurar permissões de usuário. O RBAC não afeta permissões de usuário no banco de dados.

Como configurar o RBAC do Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Como configurar o acesso do usuário com SQL para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o Banco de Dados do Azure para MySQL e implementou controles rígidos para evitar a perda ou a exposição dos dados do cliente.

Entenda a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretriz**: O serviço Banco de Dados do Azure para MySQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

A criptografia de dados com chaves gerenciadas pelo cliente para o Banco de Dados do Azure para MySQL permite que você traga sua própria chave (BYOK) para proteção de dados em repouso. No momento, é necessário solicitar acesso para usar esta capacidade. Para fazer isso, entre em contato com:

AskAzureDBforMySQL@service.microsoft.com

Entenda a criptografia em repouso para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-security

Como configurar chaves gerenciadas pelo cliente para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretriz**: use o Azure Monitor com o log de atividades do Azure para criar alertas para quando alterações ocorrerem nas instâncias de produção do Banco de Dados do Azure para MySQL e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretriz**: Não disponível no momento; a Central de Segurança do Azure ainda não dá suporte à avaliação de vulnerabilidades para o Banco de Dados do Azure para MySQL.

Cobertura de recurso para serviços de PaaS do Azure na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/features-paas

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: implantar uma solução automatizada de gerenciamento de patch de software de terceiros

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretriz**: a Microsoft realiza o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Banco de Dados do Azure para MySQL.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Diretriz**: use o Azure Resource Graph para consultar e descobrir todos os recursos (inclusive instâncias do Banco de Dados do Azure para MySQL) em sua(s) assinatura(s). Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Como criar consultas com o Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o RBAC do Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretriz**: aplique marcas às instâncias do Banco de Dados do Azure para MySQL e outros recursos relacionados fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretriz**: use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear instâncias do Banco de Dados do Azure para MySQL e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: manter um inventário de recursos e títulos de software aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s).

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com o Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: limitar a capacidade dos usuários de interagir com o Azure Resource Manager por meio de scripts

**Diretriz**: use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e as alterações em recursos em um ambiente de alta segurança, como instâncias do Banco de Dados do Azure para MySQL que contêm informações confidenciais.

Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretriz**: defina e implemente configurações de segurança padrão para suas instâncias do Banco de Dados do Azure para MySQL com o Azure Policy. Use aliases do Azure Policy no namespace “Microsoft.DBforMySQL” para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do Banco de Dados do Azure para MySQL. Você também usar as definições de política integradas relacionadas às suas instâncias do Banco de Dados do Azure para MySQL, como:

'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL

Como exibir os aliases do Azure Policy disponíveis: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos do Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretriz**: Se você estiver usando definições personalizadas do Azure Policy para suas instâncias do Banco de Dados do Azure para MySQL e recursos relacionados, use o Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretriz**: use aliases do Azure Policy no namespace “Microsoft.DBforMySQL” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementar o monitoramento automatizado de configuração para serviços do Azure

**Diretriz**: use aliases do Azure Policy no namespace “Microsoft.DBforMySQL” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Use o Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para as instâncias do Banco de Dados do Azure para MySQL e recursos relacionados.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretriz**: para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure que está sendo usado para acessar suas instâncias do Banco de Dados do Azure para MySQL, use a Identidade de Serviço Gerenciado junto com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Banco de Dados do Azure para MySQL. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Como integrar-se às Identidades Gerenciadas do Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um cofre de chaves: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do cofre de chaves com uma identidade gerenciada: https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretriz**: a instância do Banco de Dados do Azure para MySQL oferece suporte à autenticação do Azure Active Directory para acessar bancos de dados.  Ao criar a instância do Banco de Dados do Azure para MySQL, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários adicionais do banco de dados.  

Para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure que está sendo usado para acessar a instância do Banco de Dados do Azure para MySQL, use a Identidade de Serviço Gerenciado junto com o Azure Key Vault para armazenar e recuperar as credenciais da instância do Banco de Dados do Azure para MySQL. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (AD). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

Como configurar identidades gerenciadas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se às Identidades Gerenciadas do Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Banco de Dados do Azure para SQL), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretriz**: o antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Banco de Dados do Azure para MySQL), mas não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de Blobs, Banco de Dados do Azure para MySQL, etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Banco de Dados do Azure para MySQL), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/D

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Diretriz**: o Banco de Dados do Azure para MySQL faz backups dos arquivos de dados e do log de transações. Dependendo do tamanho máximo de armazenamento com suporte, fazemos backups totais e diferenciais (servidores de armazenamento máximo de 4 TB) ou backups de instantâneo (servidores de armazenamento máximo de até 16 TB). Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Entenda backups para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

Entenda a configuração inicial do Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Diretriz**: o Banco de Dados do Azure para MySQL cria automaticamente backups do servidor e os armazena em armazenamento redundante localmente ou redundante geograficamente, de acordo com a escolha do usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais. 

Se estiver usando o Azure Key Vault para armazenar credenciais para suas instâncias do Banco de Dados do Azure para MySQL, garanta backups regulares automatizados de suas chaves. 

Entenda backups para o Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Como fazer backup de chaves do cofre de chaves: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretriz**: No Banco de Dados do Azure para MySQL, a execução de uma restauração cria um novo servidor de backup do servidor original. Há dois tipos de restauração disponíveis: restauração pontual e restauração geográfica. A restauração pontual está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original. A restauração geográfica está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

Teste periodicamente a restauração das suas instâncias do Banco de Dados do Azure para MySQL.

Entenda o backup e a restauração no Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretriz**: O Banco de Dados do Azure para MySQL usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Entenda o backup e a restauração no Banco de Dados do Azure para MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e capacidades: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança da Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Sentinel.

Como configurar a exportação contínua: https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretriz**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias

**Diretriz**: siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [Linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
