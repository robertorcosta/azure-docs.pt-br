---
title: Linha de base de segurança do Azure para o cofre-chave
description: Linha de base de segurança do Azure para o cofre-chave
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616892"
---
# <a name="azure-security-baseline-for-key-vault"></a>Linha de base de segurança do Azure para o cofre-chave

A linha de base de segurança do Azure para key vault contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções em nuvem no Azure com nossa orientação de práticas recomendadas.

Para obter mais informações, consulte [a visão geral do Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de Segurança: Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

**Orientação**: Integre o Azure Key Vault com o Azure Private Link. 

O Azure Private Link Service permite que você acesse os Serviços Azure (por exemplo, O Azure Key Vault) e o Azure hospedou serviços de clientes/parceiros em um Ponto Final Privado em sua rede virtual.

Um Ponto de Extremidade Privado do Azure é um adaptador de rede que conecta você de maneira privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Como integrar o Key Vault com o Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

**Orientação**: Use o Azure Security Center e siga as recomendações de proteção de rede para ajudar a proteger os recursos configurados pelo Key Vault no Azure. 

Para obter mais informações sobre a Segurança de Rede fornecida pelo Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilite o Padrão de Proteção DDoS do Azure nas Redes Virtuais do Azure associadas às instâncias do Cofre-Chave para proteção contra ataques distribuídos de negação de serviço. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet mal-intencionados ou não usados.

 
Gerenciar o Padrão de Proteção DDoS do Azure usando o portal Azure:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Detecção de ameaças para a camada de serviço do Azure no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

**Orientação**: O Azure Key Vault não usa grupos de segurança de rede (NSG) e os registros de fluxo do Azure Key Vault não são capturados. Em vez disso, use o Azure Private Link para proteger as instâncias do Azure Key Vault e habilitar as configurações de diagnóstico para registrar métricas e eventos de auditoria.

Integrar o Key Vault com o Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service

Registro do Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Este requisito pode ser cumprido configurando a proteção avançada contra ameaças (ATP) para o Azure Key Vault. A ATP fornece uma camada adicional de inteligência de segurança. Esta ferramenta detecta tentativas potencialmente prejudiciais de acessar ou explorar contas do Azure Key Vault.

Quando o Azure Security Center detecta atividade anômala, ele exibe alertas. Ele também envia e-mails ao administrador de assinaturas com detalhes da atividade suspeita e recomendações de como investigar e remediar as ameaças identificadas.

Configure proteção avançada contra ameaças para o Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicativos web em execução no Azure App Service ou recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para obter acesso às instâncias do Azure Key Vault, use as tags de serviço do Azure para o Azure Key Vault para definir controles de acesso à rede em grupos de segurança de rede ou firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Visão geral das tags de serviço do Azure:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados às instâncias do Azure Key Vault com a Diretiva Azure. Use aliases de diretiva do Azure nos espaços de nome "Microsoft.KeyVault" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das instâncias do Azure Key Vault. Você também pode fazer uso de definições de diretiva incorporadas relacionadas ao Azure Key Vault, tais como:

O Key Vault deve usar um ponto de extremidade de serviço de rede virtual

Tutorial: Crie e gerencie políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da política do Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Início rápido: Defina e atribua um projeto no portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

**Orientação**: Use tags para recursos relacionados à segurança da rede e fluxo de tráfego para as instâncias do Azure Key Vault para fornecer metadados e organização lógica.

Use qualquer uma das definições de política incorporadas do Azure relacionadas à marcação, como "Exigir tag e seu valor" para garantir que todos os recursos sejam criados com tags e notificá-lo dos recursos não marcados existentes.

Você pode usar o Azure PowerShell ou o Azure CLI para procurar ou executar ações com base em recursos com base em suas tags.

Use tags para organizar seus recursos do Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

**Orientação**: Use o Azure Activity Log para monitorar as configurações de recursos da rede e detectar alterações nos recursos de rede relacionados às instâncias do Cofre de Chaves do Azure. Crie alertas no Azure Monitor que serão acionados quando ocorrerem alterações nos recursos críticos da rede.

Exibir e recuperar eventos do Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Criar, visualizar e gerenciar alertas de registro de atividades usando o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de Segurança: Registro e Monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Use fontes de sincronização de tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo usada para os recursos do Azure, como o Azure Key Vault, para carimbos de tempo nos registros.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central do registro de segurança

**Orientação**: Ingerir logs via Azure Monitor para agregar dados de segurança gerados pelo Azure Key Vault. No Azure Monitor, use o espaço de trabalho do Azure Log Analytics para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. 

Registro do Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Quickstart: Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro de auditoria para os recursos do Azure

**Orientação**: Habilite as configurações de diagnóstico nas instâncias do Azure Key Vault para acesso a registros de auditoria, segurança e diagnóstico. Os registros de atividades, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data, endereços de origem, endereços de destino e outros elementos úteis.

Registro do Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Colete registros de segurança de sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configure a retenção de armazenamento de registro de segurança

**Orientação**: Dentro do Monitor Do Azure, para que o espaço de trabalho do Log Analytics seja usado para segurar seus logs do Azure Key Vault, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Alterar o período de retenção de dados:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e revisar logs

**Orientação**: Analise e monitore registros para comportamento anômalo e revise regularmente os resultados dos recursos protegidos pelo Azure Key Vault. Use o espaço de trabalho Log Analytics do Azure Monitor para revisar logs e executar consultas em dados de log. Alternativamente, você pode habilitar e a bordo dados para o Azure Sentinel ou um SIEM de terceiros. 

Partida rápida: A bordo do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Comece com o Log Analytics no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Comece com consultas de log no Monitor Do Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Habilite alertas para atividades anômalas

**Orientação**: No Azure Security Center, habilite a proteção avançada contra ameaças (ATP) para o Key Vault. Habilite as configurações de diagnóstico no Azure Key Vault e envie logs para um espaço de trabalho do Log Analytics. A bordo do espaço de trabalho do Log Analytics para o Azure Sentinel, pois ele fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isso permite que as cartilhas (soluções automatizadas) sejam criadas e usadas para corrigir problemas de segurança.

Partida rápida: A bordo do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gerenciar e responder a alertas de segurança na Central de Segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Responda a eventos com alertas do Monitor Do Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralize o registro anti-malware

**Orientação**: Não aplicável; O Azure Key Vault não processa ou produz logs relacionados a malware.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilite o registro de consulta de DNS

**Orientação**: Não aplicável; O Azure Key Vault não processa ou produz registros relacionados ao DNS.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilite o registro de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de Segurança: Controle de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: Mantenha um inventário de seus aplicativos registrados no Azure Active Directory, bem como de quaisquer contas de usuário que tenham acesso às chaves, segredos e certificados do Azure Key Vault. Você pode usar o portal Azure ou o PowerShell para consultar e reconciliar o acesso ao Key Vault. Para visualizar o acesso no PowerShell, use o seguinte comando:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Propriedades.Políticas de acesso

Registrando um aplicativo no Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Acesso seguro a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Orientação**: Não aplicável; O Azure Key Vault não tem o conceito de senhas padrão, pois a autenticação é fornecida pelo Active Directory e protegida com controle de acesso baseado em função.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas que tenham acesso às instâncias do Cofre chave do Azure. Use o Azure Security Center Identity and Access Management (atualmente em pré-visualização) para monitorar o número de contas administrativas ativas.

Monitorar identidade e acesso (visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

**Orientação**: Use um diretor de serviço do Azure em conjunto com o AppId, TenantID e ClientSecret, para autenticar perfeitamente seu aplicativo e recuperar o token que será usado para acessar seus segredos do Azure Key Vault.

Autenticação de serviço a serviço no Azure Key Vault usando .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

**Orientação**: Habilite a autenticação multifatorial do Azure Active Directory e siga as recomendações do Azure Security Center Identity and Access Management (atualmente em pré-visualização) para ajudar a proteger os recursos habilitados para o Hub de Eventos.

Planejamento de uma implantação de autenticação multifatorial baseada em nuvem:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorar identidade e acesso (visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Use uma Estação de Trabalho de Acesso Privilegiado (PAW) com O MFA (Azure Multi-Factor Authentication, autenticação multifatorial) configurada para fazer login e configurar recursos habilitados para Key Vault. 

Estações de trabalho de acesso privilegiado:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planejamento de uma implantação de autenticação multifatorial baseada em nuvem:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

**Orientação**: Use o Azure Active Directory (AAD) Privileged Identity Management (PIM) para geração de registros e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente. Use detecções de risco AAD para visualizar alertas e relatórios sobre comportamentos de usuários de risco. Para registro adicional, envie alertas de detecção de risco do Azure Security Center para o Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

Habilite a proteção avançada contra ameaças (ATP) para o Azure Key Vault para gerar alertas para atividades suspeitas.

Implantar o Azure AD Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Configure proteção avançada contra ameaças para o Azure Key Vault (visualização):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alertas para o Azure Key Vault (Visualização):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Detecções de risco do Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Criar e gerenciar grupos de ação no portal Azure:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie seus locais nomeados. Com locais nomeados, você pode criar agrupamentos lógicos de faixas de endereços IP ou países e regiões. Você pode restringir o acesso a recursos confidenciais, como seus segredos do Key Vault, aos seus locais nomeados configurados.

Qual é a condição de localização no Azure Active Directory Conditional Access?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

**Orientação**: Use o Azure Active Directory (AAD) como o sistema central de autenticação e autorização para recursos do Azure, como o Key Vault. Isso permite que o RBAC (Role-based Access Control, controle de acesso baseado em função) seja administrado recursos confidenciais.

 

Quickstart: Crie um novo inquilino no Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

**Orientação**: Revise os registros do Azure Active Directory (AAD) para ajudar a descobrir contas obsoletas com funções administrativas do Azure Key Vault. Além disso, use avaliações de acesso AAD para gerenciar eficientemente membros do grupo, acesso a aplicativos corporativos que podem ser usados para acessar o Azure Key Vault e atribuições de função. O acesso do usuário deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os usuários certos tenham acesso contínuo.

Relatórios do Azure Active Directory e documentação de monitoramento:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Quais são as avaliações de acesso ao Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

**Orientação**: Habilite as configurações de diagnóstico para o Azure Key Vault e o Azure Active Directory, enviando todos os logs para um espaço de trabalho do Log Analytics. Configure os alertas desejados (como tentativas de acessar segredos desativados) no Log Analytics.

Integre os logs do Azure AD com os logs do Monitor do Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrando da antiga solução Key Vault:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

**Orientação**: Use os recursos de proteção de identidade e detecção de risco do Azure Active Directory para configurar respostas automatizadas a ações suspeitas detectadas relacionadas aos recursos protegidos do Azure Key Vault. Você deve habilitar respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização. 

Relatório de logins arriscados no portal do Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Como: Configurar e habilitar políticas de risco:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: Não aplicável; O Customer Lockbox não é suportado para o Azure Key Vault.

Serviços e cenários suportados em disponibilidade geral:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: Use tags para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais nos recursos habilitados para o Azure Key Vault. 

Use tags para organizar seus recursos do Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

**Orientação**: Você pode garantir o acesso ao Azure Key Vault fazendo uso de pontos finais de serviço de rede virtuais configurados para restringir o acesso a sub-redes específicas.

Depois que as regras de firewall estiverem em vigor, você só poderá executar as operações do plano de dados do Azure Key Vault quando sua solicitação se originar de sub-redes ou intervalos de endereços IP permitidos. Isso também se aplica ao acesso do Azure Key Vault no portal Azure. Embora você possa navegar até um cofre de chaves do portal Azure, você pode não ser capaz de listar chaves, segredos ou certificados se sua máquina cliente não estiver na lista permitida. Isso também afeta o Azure Key Vault Picker e outros serviços do Azure. Você pode ser capaz de ver listas de Key Vaults, mas não chaves de lista, se as regras de firewall impedirem sua máquina cliente de fazê-lo.

Configure firewalls e redes virtuais do Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Pontos finais de serviço de rede virtual para Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação:** Todos os dados armazenados no Azure Key Vault são considerados sensíveis. Use os controles de acesso do avião de dados do Azure Key Vault para controlar o acesso aos segredos do Azure Key Vault. Você também pode usar o firewall incorporado do Key Vault para controlar o acesso na camada de rede. Para monitorar o acesso ao Azure Key Vault, habilite as Configurações de Diagnóstico do Cofre de Chaves e envie logs para uma conta de armazenamento azure ou espaço de trabalho do Log Analytics.

Acesso seguro a um cofre de chaves:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configure firewalls e redes virtuais do Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Registro do Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

**Orientação**: Todo o tráfego para o Azure Key Vault para autenticação, gerenciamento e acesso a data plane, é criptografado e passa por HTTPS: porta 443. (No entanto, ocasionalmente haverá tráfego HTTP [porta 80] para CRL.) 

Acesse o Azure Key Vault atrás de um firewall:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Não aplicável; todos os dados dentro do Azure Key Vault (segredos, chaves e certificados) são considerados sensíveis.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Use o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Acesso seguro ao plano de gerenciamento e dados das instâncias do Cofre chave do Azure.

Acesso seguro a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

**Orientação**: A Microsoft gerencia a infra-estrutura subjacente para o Azure Key Vault e implementou controles rigorosos para evitar a perda ou exposição dos dados dos clientes.

O que é o Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Proteção de dados de clientes do Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

**Orientação**: Todos os objetos gerenciados (chave, certificados e segredos) são criptografados em repouso no Azure Key Vault.

Documentação de suporte:

- [Modelo de criptografia e tabela de gerenciamento de chaves](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

**Orientação**: Use a solução Azure Key Vault Analytics no Azure Monitor para revisar os registros de eventos de auditoria do Azure Key Vault.

Solução Azure Key Vault Analytics no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: Gerenciamento de vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Execute ferramentas automatizadas de varredura de vulnerabilidades

**Orientação**: A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Azure Key Vault.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patches do sistema operacional

**Orientação**: N/A; A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que suportam o Key Vault.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar solução automatizada de gerenciamento de patches de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as varreduras de vulnerabilidade sinuosas

**Orientação**: A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que suportam o Key Vault.

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Use as classificações de risco padrão (Secure Score) fornecidas pelo Azure Security Center.

Melhore sua pontuação segura no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de Segurança: Inventário e Gestão de Ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use o Azure Asset Discovery

**Orientação**: Use o Gráfico de Recursos do Azure para consultar e descobrir todos os recursos (incluindo as instâncias do Azure Key Vault) dentro da sua assinatura. Certifique-se de ter permissões apropriadas (leia) em seu inquilino e seja capaz de enumerar todas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Partida rápida: Execute sua primeira consulta de gráfico de recursos usando o Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Obtenha assinaturas que a conta corrente pode acessar.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

O que é o RBAC (controle de acesso baseado em função) para recursos do Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique tags aos recursos do Azure Key Vault que dão metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

**Orientação**: Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear instâncias do Azure Key Vault e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Crie uma assinatura adicional do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Criar grupos de gerenciamento para organização e gerenciamento de recursos:

https://docs.microsoft.com/azure/governance/management-groups/create

Use tags para organizar seus recursos do Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos e títulos de software aprovados do Azure

**Orientação**: Defina lista de recursos aprovados do Azure e software aprovado para seus recursos de computação

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

**Orientação**: Use as políticas do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro da assinatura(s).

Tutorial: Crie e gerencie políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Partida rápida: Execute sua primeira consulta de gráfico de recursos usando o Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure como um todo, bem como recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

**Orientação**: Use as políticas do Azure para colocar restrições sobre o tipo de recursos que podem ser criados na assinatura do cliente usando as seguintes definições de política incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Tutorial: Crie e gerencie políticas para impor a conformidade:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da política do Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de aplicativos aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o AzureResources Manager através de scripts

**Orientação**: Use o Acesso Condicional do Azure para limitar a capacidade dos usuários de interagir com o Arm (Azure Resource Manager, gerente de recursos do Azure) configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management". Isso pode impedir a criação e alterações de recursos em um ambiente de alta segurança, como aqueles com configuração do Key Vault.

Gerenciar o acesso ao gerenciamento do Azure com acesso condicional:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.KeyVault" para criar políticas personalizadas para auditar ou impor a configuração das instâncias do Cofre de Chaves do Azure. Você também pode usar definições de diretiva azure incorporadas para o Azure Key Vault, tais como:

Os objetos do Key Vault devem ser recuperáveis

Implantar as configurações de diagnóstico do Key Vault no workspace do Log Analytics

Os logs de diagnóstico no Key Vault deve estar habilitados

O Key Vault deve usar um ponto de extremidade de serviço de rede virtual

Aplicar as Configurações de Diagnóstico do Key Vault no Hub de Eventos

Use as recomendações do Azure Security Center como uma linha de base de configuração segura para as instâncias do Cofre chave do Azure.

Como visualizar aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Crie e gerencie políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos habilitados para Azure Key Vault. 

Tutorial: Crie e gerencie políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Entenda os efeitos da política do Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

**Orientação**: Se usar definições personalizadas de diretiva do Azure para os recursos habilitados para o Azure Key Vault, use os Repos Do Azure para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentação de Repos do Azure: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

**Orientação**: Use alias de diretiva do Azure no namespace "Microsoft.KeyVault" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Use o Azure Security Center para executar varreduras de linha de base para os recursos protegidos pelo Cofre chave do Azure 

  

Como remediar recomendações no Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Não aplicável; este benchmark destina-se a recursos de computação.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

**Orientação**: Use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto para seus aplicativos na nuvem. Certifique-se de que o Azure Key Vault está ativado.

Como se integrar com as identidades gerenciadas do Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

**Orientação**: Use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto para seus aplicativos na nuvem. 

  

Como se integrar com as identidades gerenciadas do Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Como criar um Cofre de Chaves: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Como fornecer autenticação do Key Vault com uma identidade gerenciada:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pré-varredura de arquivos a serem carregados para recursos azure não computados

**Orientação**: O anti-malware da Microsoft é habilitado no host subjacente que suporta serviços do Azure (por exemplo, O Azure Key Vault), no entanto, ele não é executado no conteúdo do cliente.

Pré-escaneie qualquer conteúdo que seja carregado ou enviado para recursos não computados do Azure, como o Azure Key Vault. A Microsoft não pode acessar seus dados nessas instâncias.

Entenda o Microsoft Antimalware for Azure Cloud Services and Virtual Machines:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Certifique-se de que o software anti-malware e as assinaturas sejam atualizadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacionais. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

**Orientação**: Certifique-se de backups automatizados regulares de seus Certificados, Chaves, Contas de Armazenamento Gerenciados e Segredos, com os seguintes comandos PowerShell:

- Certificado de backup-AzKeyVault

- Chave de backup-AzkeyVaultkey

- Conta de armazenamento gerenciada do Backup-AzKeyVault

- Backup-AzKeyVaultSecret

Opcionalmente, você pode armazenar os backups do Key Vault no Backup do Azure.

Como fazer backup dos certificados do cofre-chave:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup das chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup de contas de armazenamento gerenciadas do cofre de chaves:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como fazer backup dos segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar o backup do Azure:https://docs.microsoft.com/azure/backup



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

**Orientação**: Execute backups de seus Certificados, Chaves, Contas de Armazenamento Gerenciados e Segredos, com os seguintes comandos PowerShell:

- Certificado de backup-AzKeyVault

- Chave de backup-AzkeyVaultkey

- Conta de armazenamento gerenciada do Backup-AzKeyVault

- Backup-AzKeyVaultSecret

Opcionalmente, você pode armazenar os backups do Key Vault no Backup do Azure.

Como fazer backup dos certificados do cofre-chave:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup das chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup de contas de armazenamento gerenciadas do cofre de chaves:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como fazer backup dos segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar o backup do Azure:https://docs.microsoft.com/azure/backup



**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação:** Execute periodicamente a restauração de dados de seus Certificados, Chaves, Contas de Armazenamento Gerenciados e Segredos, com os seguintes comandos PowerShell:

- Restore-AzKeyVaultCertificate

- Restaurar-AzKeyVaultKey

- Restaurar-AzKeyVaultGerenciamentodearmazenamentoConta

- Restaurando-AzKeyVaultSecret

Como restaurar certificados de cofre-chave:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Como restaurar chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Como restaurar contas de armazenamento gerenciadas do cofre-chave:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Como restaurar os segredos do Cofre chave:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Certifique-se de que a exclusão suave está ativada para o Azure Key Vault. A exclusão suave permite a recuperação de cofres de chaves excluídos e objetos do cofre, como chaves, segredos e certificados. 

Como usar o Soft Delete do Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de Segurança: Resposta a Incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

**Orientação**: Construa um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente. Esses processos devem ter um foco na proteção de sistemas sensíveis, como aqueles que usam segredos do Key Vault.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança de Computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. Além disso, marca claramente assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados confidenciais, como segredos do Azure Key Vault.


**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realize exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger as instâncias do Cofre de Chaves do Azure e os recursos relacionados. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

**Orientação**: As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta à segurança) descobrir que seus dados foram acessados por uma parte ilegal ou não autorizada.  Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento do Azure Security Center**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Azure Security Center usando o recurso Exportação Contínua para ajudar a identificar riscos aos recursos habilitados para o Azure Key Vault. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua.  Você pode usar o conector de dados do Azure Security Center para transmitir os alertas para o Azure Sentinel. 

 

Como configurar a exportação contínua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Como transmitir alertas no Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

**Orientação**: Use o recurso de automação de fluxo de trabalho no Azure Security Center para disparar automaticamente as respostas por meio de "Logic Apps" em alertas de segurança e recomendações para proteger seus recursos protegidos pelo Azure Key Vault. 

 

Como configurar aplicativos de automação e lógica de fluxo de trabalho: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento do Azure Security Center**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de Segurança: Testes de Penetração e Exercícios de Equipe Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração de seus recursos do Azure e garanta a remediação de todas as descobertas críticas de segurança dentro de 60 dias

**Orientação**: Você não deve realizar testes de caneta diretamente no serviço Azure Key Vault, no entanto, é encorajado a testar seus recursos do Azure que estão usando o Key Vault para garantir a segurança dos segredos.

Você precisará seguir as regras de engajamento da Microsoft para garantir que seus testes de penetração não estejam violando as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia e execução do Red Teaming e testes de penetração de sites ao vivo contra infra-estrutura, serviços e aplicativos gerenciados pela Microsoft, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento do Azure Security Center**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Veja o Benchmark de Segurança do [Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base do Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
