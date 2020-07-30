---
title: Linha de base de segurança do Azure para Azure DevTest Labs
description: Linha de base de segurança do Azure para Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 7f0dc1fb18740e2b0611b5954821a5ceda2d2657
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387822"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de base de segurança do Azure para Azure DevTest Labs

A linha de base de segurança do Azure para Azure DevTest Labs contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](../security/benchmarks/overview.md), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas
**Diretrizes:** A Microsoft mantém fontes de tempo para recursos do Azure. No entanto, você pode gerenciar as configurações de sincronização de horário para seus recursos de computação.

Consulte o artigo a seguir para saber mais sobre como configurar a sincronização de horário para recursos de computação do Azure: [sincronização de horário para VMs do Windows no Azure](../virtual-machines/windows/time-sync.md). 

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** O

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um Log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível do plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível do plano de gerenciamento para suas instâncias do DevTest Labs.

Para obter mais informações, consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/platform/diagnostic-settings.md).

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um Log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível do plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível do plano de gerenciamento para suas instâncias do DevTest Labs.

Para obter mais informações, consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/platform/diagnostic-settings.md).

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais
**Diretrizes:** Azure DevTest Labs VMs (máquinas virtuais) são criadas e pertencentes ao cliente. Portanto, é responsabilidade da organização monitorá-la. Você pode usar a central de segurança do Azure para monitorar o sistema operacional de computação. Os dados coletados pela central de segurança do sistema operacional incluem o tipo e a versão do so, sistema operacional (logs de eventos do Windows), processos em execução, nome da máquina, endereços IP e usuário conectado. O agente de Log Analytics também coleta arquivos de despejo de memória.

Para obter mais informações, consulte os seguintes artigos: 

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança
***Diretrizes:** No Azure Monitor, defina o período de retenção de log para Log Analytics espaços de trabalho associados às suas instâncias de Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

Para obter mais informações, consulte o seguinte artigo: [como definir parâmetros de retenção de log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs
**Diretrizes:** Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho Log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para Azure DevTest Labs.

Para obter mais informações, consulte os seguintes artigos:

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal
**Diretrizes:** Use o espaço de trabalho do Azure Log Analytics para monitoramento e alertas sobre atividades anormais em logs de segurança e eventos relacionados à sua Azure DevTest Labs.

Para obter mais informações, consulte o seguinte artigo: [como alertar sobre dados de log do log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware
**Diretrizes:** Não aplicável. Azure DevTest Labs não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS
**Diretrizes:** Não aplicável. Azure DevTest Labs não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando
**Diretrizes:** Azure DevTest Labs cria máquinas de computação do Azure que são de propriedade e gerenciadas pelo cliente. Use Microsoft Monitoring Agent em todas as VMs do Windows do Azure com suporte para registrar em log o evento de criação de processo e o `CommandLine` campo. Para VMs Linux do Azure com suporte, você pode configurar manualmente o log do console por nó e usar o syslog para armazenar os dados. Além disso, use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas em dados registrados de VMs do Azure.

- [Coleta de dados na Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Como executar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Fontes de dados do Syslog no Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso
*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas
**Diretrizes:** O Azure Active Directory (AD do Azure) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure DevTest Labs funções](devtest-lab-add-devtest-user.md)  

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável
**Diretrizes:** O Azure Active Directory (AD do Azure) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

O DevTest Labs não tem o conceito de senhas padrão. 

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas
**Diretrizes:** Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)  
- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Azure DevTest Labs funções](devtest-lab-add-devtest-user.md)  

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory
**Diretrizes:** O DevTest Labs usa o serviço do Azure AD para gerenciamento de identidades. Considere esses dois aspectos principais ao conceder aos usuários acesso a um ambiente baseado em DevTest Labs:

- **Gerenciamento de recursos:** Ele fornece acesso ao portal do Azure para gerenciar recursos (criar VMs, criar ambientes, iniciar, parar, reiniciar, excluir e aplicar artefatos e assim por diante). O gerenciamento de recursos é feito no Azure usando o RBAC (controle de acesso baseado em função). Você atribui funções a usuários e define permissões de nível de acesso e recursos.
- **Máquinas virtuais (nível de rede)**: na configuração padrão, as VMs usam uma conta de administrador local. Se houver um domínio disponível (Azure AD Domain Services, um domínio local ou um domínio baseado em nuvem), as máquinas poderão ser unidas ao domínio. Os usuários podem usar suas identidades baseadas em domínio usando o artefato de ingresso no domínio para se conectarem aos computadores. 

- [Arquitetura de referência para o DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory
**Diretrizes:** Habilite a MFA (autenticação multifator) do Azure Active Directory (AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da central de segurança do Azure:*** Sim

**Responsabilidade:** Cliente


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas
**Diretrizes:** Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Monitoramento da central de segurança do Azure:** N/A

**Responsabilidade:** Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrar e alertar sobre atividades suspeitas em contas administrativas
**Diretrizes:** Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)  
- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)  

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados
**Diretrizes:** Use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services
**Diretrizes:** Use o Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário
**Diretrizes:** O Azure Active Directory (AD do Azure) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/overview-reports.md)  
- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)  

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas
**Diretrizes:** Você tem acesso à atividade de entrada do Azure Active Directory (AD do Azure), auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta de/Monitoring de SIEM (gerenciamento de informações e eventos de segurança).

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory e enviando os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar alertas no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta
**Diretrizes:** Use os recursos do Azure Active Directory (Azure AD) de proteção de identidade e de risco para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)  
- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Monitoramento da central de segurança do Azure:** Não disponível no momento

**Responsabilidade:** Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte
**Diretrizes:** No momento, não há suporte para Sistema de Proteção de Dados do Cliente para Azure DevTest Labs.

- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades
*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade
**Diretrizes:** Siga as recomendações da central de segurança do Azure para proteger suas instâncias de Azure DevTest Labs e recursos relacionados.

A Microsoft executa o gerenciamento de vulnerabilidades nos recursos subjacentes que dão suporte ao Azure DevTest Labs.

- [Entender as recomendações da central de segurança do Azure](../security-center/recommendations-reference.md) 

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional
**Diretrizes:** Use o Azure Gerenciamento de Atualizações para garantir que as atualizações de segurança mais recentes sejam instaladas em suas VMs Windows e Linux hospedadas no DevTest Labs. Para VMs do Windows, verifique se Windows Update foi habilitado e definido para atualizar automaticamente. Essa configuração não está disponível no momento para ser configurada por meio do DevTest Labs, no entanto, o administrador do laboratório/administrador de assinatura pode definir essa configuração nas VMs de computação subjacentes em sua assinatura. 

- [Como configurar Gerenciamento de Atualizações para VMs no Azure](../automation/automation-update-management.md)
- [Entender as políticas de segurança do Azure monitoradas pela central de segurança](../security-center/security-center-policy-definitions.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: implantar uma solução automatizada de gerenciamento de patch de software de terceiros
***Diretrizes:*** Como administrador de laboratório, você pode usar [artefatos do DevTest Labs](add-artifact-vm.md) para automatizar atualizações para imagens personalizadas do laboratório, incluindo patches de segurança e outras atualizações. 

Saiba mais sobre o [DevTest Labs Image Factory](image-factory-create.md), que é uma solução de configuração como código que cria e distribui imagens automaticamente regularmente com todas as configurações desejadas. 

Como administrador de assinatura, você também pode usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações e patches para VMs do DevTest Labs. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

- [Solução Gerenciamento de Atualizações no Azure](../automation/automation-update-management.md)
- [Gerenciar atualizações e patches para as VMs do Azure](../automation/automation-tutorial-update-management.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas
**Diretrizes:** Exporte os resultados da verificação em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram corrigidas. Ao usar a recomendação de gerenciamento de vulnerabilidade sugerida pela central de segurança do Azure, o cliente pode dinamizar o portal da solução selecionada para exibir os dados de verificação históricas.

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas
**Diretrizes:** Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

- [Entender a pontuação segura da central de segurança do Azure](../security-center/secure-score-security-controls.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos
*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada
**Diretrizes:** Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo os recursos do DevTest Labs) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e pode enumerar todas as assinaturas e recursos do Azure em suas assinaturas.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo
**Diretrizes:** Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)
- [Configurar marcas para o DevTest Labs](devtest-lab-add-tag.md)

**Monitoramento da central de segurança do Azure:** Não disponível

**Responsabilidade:** Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados
**Diretrizes:** Use marcação, grupos de gerenciamento e assinaturas separadas, além de laboratórios separados, quando apropriado, para organizar e controlar laboratórios e recursos relacionados ao laboratório. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura rapidamente.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)
- [Como criar Grupos de Gerenciamento](../governance/management-groups/create.md)
- [Como criar um laboratório usando o DevTest Labs](devtest-lab-create-lab.md)
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)
- [Como configurar marcas para um laboratório](devtest-lab-add-tag.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure
**Diretrizes:** Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com as necessidades organizacionais. Como administrador de assinatura, você também pode usar controles de aplicativo adaptáveis, um recurso da central de segurança do Azure para ajudá-lo a definir um conjunto de aplicativos que podem ser executados em grupos de computadores de laboratório configurados. Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

- [Como habilitar o controle de aplicativo adaptável](../security-center/security-center-adaptive-application.md)
 
**Monitoramento da central de segurança do Azure:** Não é **responsabilidade aplicável:** cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados
**Diretrizes:** Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas. Ele pode ajudar em ambientes com alta segurança, como aqueles com contas de armazenamento.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação
**Diretrizes:** A automação do Azure fornece controle total durante a implantação, operações e encerramento de cargas de trabalho e recursos. Como administrador de assinatura, você pode aproveitar o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em VMs do DevTest Labs em sua assinatura. As propriedades nome do software, versão, editor e hora de atualização estão disponíveis na portal do Azure. Para obter acesso à data de instalação e outras informações, o cliente precisou habilitar o diagnóstico em nível de convidado e colocar os logs de eventos do Windows em um espaço de trabalho Log Analytics.

Além de usar Controle de Alterações para o monitoramento de aplicativos de software, os controles de aplicativo adaptáveis na central de segurança do Azure usam o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e criar uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você evite o uso de software indesejado em seu ambiente. Você pode configurar o modo de auditoria ou o modo impor. O modo de auditoria só audita a atividade nas VMs protegidas. O modo impor impõe as regras e garante que os aplicativos que não têm permissão para serem executadas sejam bloqueados. 

- [Uma introdução à Automação do Azure](../automation/automation-intro.md)
- [Como habilitar o inventário de VM do Azure](../automation/automation-tutorial-installed-software.md)
- [Entender os controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure
**Diretrizes:** A automação do Azure fornece controle total durante a implantação, operações e encerramento de cargas de trabalho e recursos. Como administrador de assinatura, você pode usar Controle de Alterações para identificar todos os softwares instalados em VMs hospedadas no DevTest Labs. Você pode implementar seu próprio processo ou usar a configuração de estado da automação do Azure para remover software não autorizado.

- [Uma introdução à Automação do Azure](../automation/automation-intro.md)
- [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/change-tracking.md)
- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md)

**Monitoramento da central de segurança do Azure:** Não disponível

**Responsabilidade:** Cliente

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados
**Diretrizes:** Como administrador de assinatura, você pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em VMs do Azure hospedadas no DevTest Labs.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados
**Diretrizes:** Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: 

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Veja os artigos a seguir: 
- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/not-allowed-resource-types.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados
**Diretrizes:** O controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure, que ajuda você a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux), hospedados no DevTest Labs. Observe que você precisa ser um administrador de assinatura para poder definir essa configuração para os recursos de computação subjacentes hospedados no DevTest Labs. Implemente uma solução de terceiros se essa configuração não atender ao requisito da sua organização.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager
**Diretrizes:** Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando o **acesso ao bloco**"* *" para o aplicativo de **Gerenciamento de Microsoft Azure** .

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação
**Diretrizes:** Dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nas VMs hospedadas no DevTest Labs. Você também pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado, e que todos os softwares não autorizados sejam impedidos de serem executados nas VMs do Azure subjacentes.

- [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure:** Não disponível

**Responsabilidade:** Cliente


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco
**Diretrizes:** Aplicativos de alto risco implantados em seu ambiente do Azure podem ser isolados usando rede virtual, sub-rede, assinaturas, grupos de gerenciamento e assim por diante. e é suficientemente protegido com um firewall do Azure, o WAF (firewall do aplicativo Web) ou o NSG (grupo de segurança de rede).

- [Configurar a rede virtual para o DevTest Labs](devtest-lab-configure-vnet.md)
- [Visão geral do Firewall do Azure](../firewall/overview.md)
- [Visão geral de Firewall de Aplicativo Web](../web-application-firewall/overview.md)
- [Visão geral da segurança da rede](../virtual-network/security-overview.md)
- [Visão geral da rede virtual do Azure]()
- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
- [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da central de segurança do Azure:** Não disponível

**Responsabilidade:** Cliente


## <a name="malware-defense"></a>Defesa contra malware
*Para saber mais, confira Controle de segurança: Defesa contra malware.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usar software antimalware gerenciado centralmente
**Diretrizes:** Use o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais para monitorar e defender continuamente seus recursos. Para o Linux, use a solução antimalware de terceiros. Além disso, use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento.

- Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais
- Proteção contra ameaças na Central de Segurança do Azure

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure
**Diretrizes:** O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure App serviço hospedado em um laboratório), no entanto, ele não é executado no seu conteúdo.
Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc.

Use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento.

- Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais
- Entender a detecção de ameaças da central de segurança do Azure para serviços de dados

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Não aplicável


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados
**Diretrizes:** Quando implantado, o Microsoft antimalware para Azure instalará automaticamente as atualizações de assinatura, plataforma e mecanismo mais recentes por padrão. Siga as recomendações na central de segurança do Azure: "computação & aplicativos" para garantir que todos os pontos de extremidade para DevTest Labs subjacentes aos recursos de computação estejam atualizados com as assinaturas mais recentes. O sistema operacional Windows pode estar mais protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de proteção avançada contra ameaças do Microsoft defender, que se integra à central de segurança do Azure.

- Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure
- Proteção Avançada contra Ameaças do Microsoft Defender

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

## <a name="data-recovery"></a>Recuperação de dados
*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares
**Diretrizes:** Atualmente, Azure DevTest Labs não dá suporte a backups e instantâneos de VM. No entanto, você pode habilitar e configurar o backup do Azure nas VMs do Azure subjacentes hospedadas no DevTest Labs. Além disso, você também pode configurar a frequência desejada e o período de retenção para backups automáticos, contanto que você tenha acesso apropriado aos recursos de computação subjacentes. 

- [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)
- [Fazer backup de uma VM do Azure usando as configurações da VM](../backup/backup-azure-vms-first-look-arm.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente
**Diretrizes:** Atualmente, Azure DevTest Labs não dá suporte a backups e instantâneos de VM. No entanto, você pode criar instantâneos de suas VMs do Azure subjacentes hospedadas no DevTest Labs ou os discos gerenciados anexados a essas instâncias usando o PowerShell ou APIs REST, contanto que você tenha acesso apropriado aos recursos de computação subjacentes. Você também pode fazer backup de qualquer chave gerenciada pelo cliente dentro de Azure Key Vault.

Habilite o backup do Azure em VMs do Azure de destino e os períodos de frequência e retenção desejados. Ele inclui o backup completo do estado do sistema. Se você estiver usando o Azure Disk Encryption, o backup de VM do Azure manipulará automaticamente o backup de chaves gerenciadas pelo cliente.

- [Fazer backup em VMs do Azure que usam criptografia](../backup/backup-azure-vms-encryption.md)
- [Visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)
- [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)
- [Como fazer backup de chaves Key Vault no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente
**Diretrizes:** Garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste a restauração de conteúdo para uma rede virtual isolada ou assinatura. Além disso, teste a restauração de chaves de backup gerenciadas pelo cliente.

Se você estiver usando o Azure Disk Encryption, poderá restaurar a VM do Azure com as chaves de criptografia de disco. Ao usar a criptografia de disco, você pode restaurar a VM do Azure com as chaves de criptografia de disco.

- [Fazer backup em VMs do Azure que usam criptografia](../backup/backup-azure-vms-encryption.md)
- [Como recuperar arquivos do backup de VM do Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Como fazer backup e restaurar uma VM criptografada](../backup/backup-azure-vms-encryption.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente
**Diretrizes:** Quando você faz backup de discos gerenciados com o backup do Azure, as VMs são criptografadas em repouso com Criptografia do Serviço de Armazenamento (SSE). O backup do Azure também pode fazer backup de VMs do Azure que são criptografadas usando Azure Disk Encryption. O Azure Disk Encryption se integra com as chaves de criptografia do BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com Azure Key Vault chaves de criptografia de chave (KEKs). Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

- [Exclusão reversível para VMs](../backup/soft-delete-virtual-machines.md)
- [Visão geral do Azure Key Vault-Soft-Delete](../key-vault/general/soft-delete-overview.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

## <a name="incident-response"></a>Resposta a incidentes
*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes
**Diretrizes:** Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal e fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Aproveite o guia de tratamento de incidentes de segurança de computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes
**Diretrizes:** A central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)
- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure:** Ok

**Responsabilidade:** Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança
**Diretrizes:** Conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Guia de publicação do NIST para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança
**Diretrizes:** As informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes
**Diretrizes:** Exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)
- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Cliente

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança
**Diretrizes:** Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)
 
Monitoramento da central de segurança do Azure: * * * * não aplicável

**Responsabilidade:** Cliente


## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team
*Para obter mais informações, consulte controle de segurança: [testes de penetração e exercícios de equipe vermelhos](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias
**Diretrizes:** Siga as regras de envolvimento da Microsoft para garantir que os testes de penetração não estejam em violação às políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de teste de penetração do Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red Integration](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure:** Não aplicável

**Responsabilidade:** Compartilhado

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo:

- [Alertas de segurança para ambientes no Azure DevTest Labs](environment-security-alerts.md)
