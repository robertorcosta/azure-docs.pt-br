---
title: Controles de exemplo de blueprint do Azure Security Benchmark
description: Mapeamento de controle da amostra de blueprint do Azure Security Benchmark para o Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691298"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint do Azure Security Benchmark

O artigo a seguir fornece detalhes sobre como o exemplo do blueprint do Azure Security Benchmark do Azure Blueprints é mapeado para os controles do Azure Security Benchmark. Para obter mais informações sobre os controles, confira [Azure Security Benchmark](../../../../security/benchmarks/overview.md).

Os mapeamentos a seguir são para os controles do **Azure Security Benchmark**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Os controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: Auditar as recomendações do Azure Security Benchmark e implantar uma iniciativa de política interna de suporte específico das Extensões de VM**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência 1:1 ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade pode incluir controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

- As sub-redes devem ser associadas a um Grupo de Segurança de Rede
- As recomendações da Proteção de Rede Adaptável devem ser aplicadas nas máquinas virtuais para a Internet
- As máquinas virtuais devem estar conectadas a uma rede virtual aprovada
- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual
- O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual
- O SQL Server deve usar um ponto de extremidade de serviço de rede virtual
- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual
- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB
- O Key Vault deve usar um ponto de extremidade de serviço de rede virtual
- Auditar o acesso irrestrito à rede para contas de armazenamento
- As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual
- O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual
- As redes virtuais devem usar o gateway de rede virtual especificado
- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes
- \[Versão Prévia\]: O encaminhamento IP na máquina virtual deve ser desabilitado
- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

- O Observador de Rede deve ser habilitado

## <a name="13-protect-critical-web-applications"></a>1.3 Proteger aplicativos Web críticos

- Garantir que o aplicativo Web tenha a opção 'Certificados de Cliente (Certificados de cliente de entrada)' definida como 'Ativado'
- O CORS não deve permitir que todos os recursos acessem seus aplicativos Web
- O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções
- O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API
- A depuração remota deve ser desativada para aplicativos Web
- A depuração remota deve ser desativada para o aplicativos de funções
- A depuração remota deve ser desligada para aplicativos de API

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Rejeitar comunicações com endereços IP mal-intencionados conhecidos

- A Proteção contra DDoS Standard deve ser habilitada
- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- As recomendações da Proteção de Rede Adaptável devem ser aplicadas nas máquinas virtuais para a Internet

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Gravar pacotes de rede e logs de fluxo

- O Observador de Rede deve ser habilitado

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

- Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Acesso à Rede'
- Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Cliente de Rede Microsoft'
- Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Segurança de Rede'
- Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Segurança de Rede'
- Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Servidor de Rede Microsoft'
- Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Servidor de Rede Microsoft'
- Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Modelos Administrativos – Rede'
- Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Modelos Administrativos – Rede'

## <a name="22-configure-central-security-log-management"></a>2.2 Configurar o gerenciamento central de log de segurança

- O agente do Log Analytics deve ser instalado nas máquinas virtuais
- O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais
- Implantar pré-requisitos para auditar as VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- Mostrar os resultados da auditoria das VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- O perfil de log do Azure Monitor deve coletar logs para as categorias 'gravar', 'excluir' e 'ação'
- O Azure Monitor deve coletar os logs de atividades de todas as regiões
- O provisionamento automático do agente de monitoramento do Log Analytics deve estar habilitado na assinatura

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Habilitar o log de auditoria para recursos do Azure

- Os logs de diagnóstico no Azure Data Lake Storage devem ser habilitados
- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados
- Os logs de diagnóstico no Hub IoT devem ser habilitados
- Os logs de diagnóstico em contas do Lote devem ser habilitados
- Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados
- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados
- Os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados
- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados
- Os logs de diagnóstico no Key Vault deve estar habilitados
- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- A auditoria no SQL Server deve ser habilitada
- Configuração de diagnóstico de auditoria

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Coletar logs de segurança de sistemas operacionais

- O provisionamento automático do agente de monitoramento do Log Analytics deve estar habilitado na assinatura
- O agente do Log Analytics deve ser instalado nas máquinas virtuais
- O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais
- Implantar pré-requisitos para auditar as VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- Mostrar os resultados da auditoria das VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Habilitar alertas para atividade anômala

- O tipo de preço Standard da Central de Segurança deve ser selecionado
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada do SQL Server
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada da instância gerenciada de SQL

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centralizar o registro em log de antimalware

- O Microsoft Antimalware para o Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção
- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Manter um inventário de contas administrativas

- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Usar contas administrativas dedicadas

- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém somente os membros especificados
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores não contém somente os membros especificados
- Implantar pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Mostrar os resultados da auditoria das VMs do Windows em que o grupo Administradores não contém todos os membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Alertar sobre atividades suspeitas de contas administrativas e registrá-las em log

- O tipo de preço Standard da Central de Segurança deve ser selecionado

## <a name="39-use-azure-active-directory"></a>3.9 Usar o Azure Active Directory

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- Garantir que o Registro com o Azure Active Directory esteja habilitado no aplicativo de API
- Garantir que o Registro com o Azure Active Directory esteja habilitado no aplicativo Web
- Garantir que o Registro com o Azure Active Directory esteja habilitado no aplicativo de funções

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Examinar e reconciliar regularmente o acesso do usuário

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Manter um inventário de informações confidenciais

- Os dados confidenciais nos bancos de dados SQL devem ser classificados

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Criptografar todas as informações confidenciais em trânsito

- A transferência segura para contas de armazenamento deve ser habilitada
- A última versão do TLS deve ser usada no aplicativo de API
- A última versão do TLS deve ser usada no aplicativo Web
- A última versão do TLS deve ser usada no aplicativo de funções
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL
- Apenas conexões seguras com o Cache Redis devem ser habilitadas

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

- Os dados confidenciais nos bancos de dados SQL devem ser classificados
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Usar o RBAC do Azure para controlar o acesso aos recursos

- O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes
- Auditar o uso de regras personalizadas do RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Criptografar informações confidenciais em repouso

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- A criptografia de disco deve ser aplicada em máquinas virtuais
- Os discos desconectados devem ser criptografados
- O protetor TDE do SQL Server deve ser criptografado com sua própria chave
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- As variáveis da conta de automação devem ser criptografadas
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Alertar sobre alterações em recursos críticos do Azure e registrá-las em log

- O Azure Monitor deve coletar os logs de atividades de todas as regiões

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Executar ferramentas automatizadas de verificação de vulnerabilidade

- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- \[Versão prévia\] A Avaliação de Vulnerabilidades deve ser habilitada nas Máquinas Virtuais
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Implantar uma solução automatizada de gerenciamento de patch de sistema operacional

- As atualizações do sistema devem ser instaladas em suas máquinas
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas
- Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo de funções
- Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo Web
- Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo de API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Implantar uma solução automatizada de gerenciamento de patch de software de terceiros

- Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de API
- Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo Web
- Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de funções
- Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo Web
- Garantir que a "versão do Java" seja a mais recente, se usada como parte do aplicativo de funções
- Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo de API
- Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo Web
- Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de funções
- Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de API
- Os Serviços de Kubernetes devem ser atualizados para uma versão não vulnerável do Kubernetes

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas

## <a name="68-use-only-approved-applications"></a>6.8 Usar somente aplicativos aprovados

- O tipo de preço Standard da Central de Segurança deve ser selecionado
- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="69-use-only-approved-azure-services"></a>6.9 Usar somente os serviços do Azure aprovados

- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="610-implement-approved-application-list"></a>6.10 Implementar a lista de aplicativos aprovados

- O tipo de preço Standard da Central de Segurança deve ser selecionado
- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Manter configurações seguras de recursos do Azure

- \[Versão Prévia\]: as políticas de segurança de pods devem ser definidas nos Serviços de Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Manter configurações seguras de sistema operacional

- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementar o monitoramento automatizado de configuração para serviços do Azure

- \[Versão Prévia\]: as políticas de segurança de pods devem ser definidas nos Serviços de Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementar o monitoramento automatizado de configuração para sistemas operacionais

- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas

## <a name="711-manage-azure-secrets-securely"></a>7.11 Gerenciar segredos do Azure com segurança

- Os objetos do Key Vault devem ser recuperáveis

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Gerenciar identidades de maneira segura e automática

- A identidade gerenciada deve ser usada no aplicativo de funções
- A identidade gerenciada deve ser usada no aplicativo Web
- A identidade gerenciada deve ser usada no aplicativo de API

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Usar software antimalware gerenciado centralmente

- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

- O tipo de preço Standard da Central de Segurança deve ser selecionado

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Garantir que o software antimalware e as assinaturas sejam atualizados

- O Microsoft Antimalware para o Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Garantir backups automatizados regulares

- O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB
- O Backup do Azure deve ser habilitado para máquinas virtuais

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Executar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

- O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB
- O Backup do Azure deve ser habilitado para máquinas virtuais

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Garantir a proteção de backups e chaves gerenciadas pelo cliente

- Os objetos do Key Vault devem ser recuperáveis

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Criar um procedimento de pontuação e priorização de incidentes

- O tipo de preço Standard da Central de Segurança deve ser selecionado

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

- Um endereço de email de contato de segurança deve ser fornecido para a sua assinatura
- Um número de telefone de contato de segurança deve ser fornecido para a sua assinatura
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança
- As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server
- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou o mapeamento de controle de blueprint do Azure Security Benchmark, visite o Azure Policy na portal do Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).