---
title: Linha de base de segurança do Azure para backup
description: Linha de base de segurança do Azure para backup
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b18463720b63f62a2ae4b19a3bdbae6862bc9985
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120724"
---
# <a name="azure-security-baseline-for-backup"></a>Linha de base de segurança do Azure para backup

A linha de base de segurança do Azure para backup contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral de linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Orientação**: não aplicável; Não é possível associar uma rede virtual, uma sub-rede ou um grupo de segurança de rede a um cofre dos serviços de recuperação. Ao fazer backup de uma máquina virtual do Azure, os dados são transferidos pelo backbone do Azure. Ao fazer backup de um computador local, um túnel criptografado é criado com um ponto de extremidade específico no Azure e as credenciais são usadas para criptografar previamente os dados antes de serem enviados pelo túnel criptografado.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: não aplicável; Não é possível associar uma rede virtual, uma sub-rede ou um grupo de segurança de rede a um cofre dos serviços de recuperação. Ao fazer backup de uma máquina virtual do Azure, os dados são transferidos pelo backbone do Azure. Ao fazer backup de um computador local, um túnel criptografado é criado com um ponto de extremidade específico no Azure e as credenciais são usadas para criptografar previamente os dados antes de serem enviados pelo túnel criptografado.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: os pontos de extremidade usados pelo backup do Azure (incluindo o agente de serviços de recuperação do Microsoft Azure) são todos gerenciados pela Microsoft. Você é responsável por quaisquer controles adicionais que deseja implantar em seus sistemas locais.

- [Entender o suporte de rede e acesso para o agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: não aplicável; Não é possível associar uma rede virtual, uma sub-rede ou um grupo de segurança de rede a um cofre dos serviços de recuperação. Ao fazer backup de uma máquina virtual do Azure, os dados são transferidos pelo backbone do Azure. Ao fazer backup de um computador local, um túnel criptografado é criado com um ponto de extremidade específico no Azure e as credenciais são usadas para criptografar previamente os dados antes de serem enviados pelo túnel criptografado.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: os pontos de extremidade usados pelo backup do Azure (incluindo o agente de serviços de recuperação do Microsoft Azure) são todos gerenciados pela Microsoft. Você é responsável por quaisquer controles adicionais que deseja implantar em seus sistemas locais.

- [Entender o suporte de rede e acesso para o agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Orientação**: se você estiver usando o agente Mars em uma máquina virtual do Azure, use a marca de serviço AzureBackup no seu NSG ou firewall do Azure para permitir o acesso de saída ao backup do Azure.

- [Fazer backup de bancos de dados SQL Server em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: não aplicável; os pontos de extremidade usados pelo backup do Azure (incluindo o agente de Serviços de Recuperação do Microsoft Azure) são todos gerenciados pela Microsoft.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: se você estiver usando o agente Mars em uma máquina virtual do Azure, associe essa VM a um grupo de segurança de rede Use a descrição para especificar a necessidade comercial da regra

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: se você estiver usando o agente Mars em uma máquina virtual do Azure que está sendo protegida por um NSG ou um firewall do Azure, use o log de atividades do Azure para monitorar a configuração do NSG ou do firewall. Você pode criar alertas em Azure Monitor que serão disparados quando ocorrerem alterações nesses recursos.

- [Exibir e recuperar eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Criar, exibir e gerenciar alertas do log de atividades usando Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Orientação**: não aplicável; A Microsoft mantém a fonte de tempo usada para recursos do Azure, como o backup do Azure, para carimbos de data/hora nos logs.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para os recursos do Azure.

Além disso, ingerir logs por meio de Azure Monitor para agregar dados de segurança gerados pelo backup do Azure. No Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar o e os dados integrados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros. 

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Usando configurações de diagnóstico para os cofres dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

- [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para os recursos do Azure.

Além disso, o backup do Azure envia eventos de diagnóstico que podem ser coletados e usados para fins de análise, alertas e relatórios. Você pode definir as configurações de diagnóstico para um cofre dos serviços de recuperação por meio do portal do Azure. Você pode enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, Hub de eventos ou um espaço de trabalho Log Analytics.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Usando configurações de diagnóstico para os cofres dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados aos seus cofres dos serviços de recuperação do Azure de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: o backup do Azure fornece recursos internos de monitoramento e alerta em um cofre dos serviços de recuperação. Essas funcionalidades estão disponíveis sem nenhuma infraestrutura de gerenciamento adicional. Aumente também a escala do monitoramento e dos relatórios usando o Azure Monitor.

Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho Log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para cofres dos serviços de recuperação.

- [Monitorando cargas de trabalho de backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Diretrizes**: o backup do Azure fornece recursos internos de monitoramento e alerta em um cofre dos serviços de recuperação. Essas funcionalidades estão disponíveis sem nenhuma infraestrutura de gerenciamento adicional. Aumente também a escala do monitoramento e dos relatórios usando o Azure Monitor.

Os alertas são principalmente cenários em que os usuários são notificados para que possam executar uma ação relevante. A seção alertas de backup mostra alertas gerados pelo serviço de backup do Azure. Esses alertas são definidos pelo serviço e você não pode criar alertas personalizados.

Você também pode carregar um espaço de trabalho Log Analytics para o Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Isso permite que os guias estratégicos (soluções automatizadas) sejam criados e usados para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados em seu espaço de trabalho do Log Analytics usando Azure Monitor.

- [Monitorando cargas de trabalho de backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável; O backup do Azure não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável; O backup do Azure não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Diretrizes**: o Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

Documentação de suporte:

- [Como obter uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: o Azure ad não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas.

Além disso, para ajudá-lo a manter o controle de contas administrativas dedicadas, você pode usar recomendações da central de segurança do Azure ou de políticas internas do Azure, como: deve haver mais de um proprietário atribuído à sua assinatura as contas preteridas com permissões de proprietário devem ser removidas de suas contas externas de assinatura com permissões de proprietário devem ser removidas da sua assinatura

- [Como usar a central de segurança do Azure para monitorar a identidade e o acesso (visualização)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Como usar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Diretrizes**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seus cofres de serviços de recuperação por meio de chamadas à API.

- [Como chamar as APIs REST do Azure](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações da API dos serviços de recuperação do Azure](https://docs.microsoft.com/rest/api/recoveryservices/)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Orientação**: ao executar operações críticas no backup do Azure, você precisa inserir um PIN de segurança, disponível no portal do Azure. A habilitação da Autenticação Multifator do Azure adiciona uma camada de segurança. Apenas usuários autorizados com credenciais válidas do Azure, e autenticados de um segundo dispositivo, podem acessar o portal do Azure.

- [Autenticação multifator no backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature)

- [Planejar uma implantação da Autenticação Multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use uma Paw (estação de trabalho com acesso privilegiado) com o MFA (autenticação multifator) do Azure configurada para fazer logon e configurar seus recursos habilitados para backup do Azure. 

- [Estações de trabalho com acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planejar uma implantação da Autenticação Multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: Use o PIM (Azure Active Directory Privileged Identity Management) para geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Além disso, use as detecções de risco do Azure AD para exibir alertas e relatórios sobre comportamento de usuário arriscado.

- [Como implantar Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Entender as detecções de riscos do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso ao portal do Azure apenas de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para suas instâncias de backup do Azure. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

- [Como configurar o backup do Azure para usar o logon do Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como criar e configurar uma instância do AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de acesso de identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para suas instâncias de backup do Azure. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com o Azure Sentinel ou um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como embutir o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para seus cofres dos serviços de recuperação. Para o desvio do comportamento de logon da conta no plano de controle (o portal do Azure), use Azure AD Identity Protection e recursos de detecção de risco para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como configurar o backup do Azure para usar o logon do Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como exibir o logon arriscado do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e habilitar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o backup do Azure.

- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Orientação**: ao fazer backup de VMs IaaS do Azure, o backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação.

Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e cofres de serviços de recuperação de produção. Os recursos devem ser separados por VNet/sub-rede, marcados adequadamente e protegidos por um NSG ou um firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser suficientemente isolados. Para máquinas virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desligá-los quando não estiverem em uso.

Documentação de suporte:

- [Visão geral do Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview)

- [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: atualmente não disponível; os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o backup do Azure.

A Microsoft gerencia a infraestrutura subjacente para o backup do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Orientação**: o tráfego de backup de servidores para o cofre dos serviços de recuperação é transferido por um link https seguro e criptografado usando criptografia AES (AES) 256 quando armazenado no cofre.

- [Entender a criptografia em repouso no backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: atualmente não disponível; os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o backup do Azure.

A Microsoft gerencia a infraestrutura subjacente para o backup do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: atualmente não disponível

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Diretrizes**: o RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

O backup do Azure fornece três funções internas para controlar as operações de gerenciamento de backup: colaborador de backup, operador de backup e leitor de backup. Você pode mapear funções internas de backup para várias ações de gerenciamento de backup.

- [Como configurar o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

- [Use o Controle de Acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação. A Microsoft gerencia a infraestrutura subjacente para o backup do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Proteção de dados do cliente do Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Diretrizes**: o backup do Azure dá suporte à criptografia para dados em repouso. Para backup local, a criptografia em repouso é realizada usando a frase secreta que você fornece ao fazer backup no Azure. Para cargas de trabalho de nuvem, os dados são criptografados em repouso usando o Criptografia do Serviço de Armazenamento (SSE). A Microsoft não descriptografa os dados de backup em nenhum momento.

Ao fazer backup com o agente MARS ou usar um cofre de serviços de recuperação criptografado com uma chave gerenciada pelo cliente, somente você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

- [Entender a criptografia em repouso para o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para os cofres dos serviços de recuperação do Azure de produção, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: ainda não disponível; a avaliação de vulnerabilidade na central de segurança do Azure ainda não está disponível para o backup do Azure.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para o backup do Azure para reduzir as vulnerabilidades relacionadas à configuração de serviço.

- [Noções básicas sobre os controles de segurança disponíveis para o backup do Azure](https://docs.microsoft.com/azure/backup/backup-security-controls)

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

**Diretrizes**: atualmente não disponível; as configurações de segurança para o backup do Azure ainda não têm suporte na central de segurança do Azure.

- [Lista de serviços de PaaS com suporte da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/features-paas)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

- [Como criar consultas com o grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar os recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: não permitidos tipos de recursos permitidos tipos de recurso

- [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

**Diretrizes**: defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. 

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com o grafo do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: tipos de recursos não permitidos são tipos de recursos permitidos

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo de recurso específico com Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Limitar a capacidade dos usuários de interagir com Azure Resource Manager por meio de scripts</div>

**Orientação**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

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

**Diretrizes**: defina e implemente configurações de segurança padrão para seu cofre de serviços de recuperação com Azure Policy. Use aliases de Azure Policy no namespace "Microsoft. Recoveryservices" para criar políticas personalizadas para auditar ou impor a configuração de seus cofres de serviços de recuperação.

- [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições personalizadas de política do Azure, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. recoveryservices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. recoveryservices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Orientação**: ao configurar o agente Mars, armazene sua senha de criptografia dentro do Azure Key Vault.

- [Como criar um Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Orientação**: não aplicável; Identidades gerenciadas não têm suporte para o backup do Azure.

- [Serviços que dão suporte a identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

**Diretrizes**: implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [controle de segurança: defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, backup do Azure), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, backup do Azure), no entanto, ele não é executado no seu conteúdo. 

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc. 

Use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento. 

- [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

- [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: não aplicável; Essa recomendação destina-se a recursos cujo backup está sendo feito e não o próprio backup do Azure.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: o LRS (armazenamento com redundância local) Replica seus dados três vezes (ele cria três cópias de seus dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger seus dados contra falhas de hardware local. O armazenamento com redundância geográfica (GRS) é a opção de replicação padrão e recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem). O GRS é mais caro do que o LRS, mas fornece um nível mais alto de durabilidade para seus dados, mesmo quando há uma interrupção regional.

Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

- [Visão geral do Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview)

- [Como fazer backup de chaves do Key Vault no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Entender a criptografia no backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar a restauração de chaves de backup gerenciadas pelo cliente.

- [Como restaurar chaves do Key Vault no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: para o backup local, a criptografia em repouso é fornecida usando a senha que você fornece ao fazer backup no Azure. Para VMs do Azure, os dados são criptografados em repouso usando SSE (Criptografia do Serviço de Armazenamento). Você pode habilitar a exclusão reversível em Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar Automaçãos de fluxo de trabalho na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Você também pode aproveitar o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

- [Consulte a publicação do NIST: guia para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato do incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato da segurança da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas sentinela.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os aplicativos lógicos](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [controle de segurança: testes de penetração e exercícios de equipe vermelhos](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas dentro de 60 dias

**Diretrizes**: [siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
