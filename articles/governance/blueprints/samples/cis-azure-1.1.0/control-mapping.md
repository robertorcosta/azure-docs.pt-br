---
title: Amostra – Blueprint do CIS Microsoft Azure Foundations Benchmark – mapeamento de recomendação
description: Mapeamento de recomendação da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark para o Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/01/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 9368ad220f1ded1a11360dbdf0af86c27277a207
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001286"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapeamento de recomendação da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark

O artigo a seguir fornece detalhes sobre como a amostra do blueprint do CIS Microsoft Azure Foundations Benchmark do Azure Blueprints é mapeada para as recomendações do CIS Microsoft Azure Foundations Benchmark. Para saber mais sobre as recomendações, confira [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Os mapeamentos a seguir são para as recomendações do **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Use a navegação no lado direito para ir diretamente para um mapeamento de recomendação específico.
Muitas das recomendações mapeadas são implementadas com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione as recomendações da **\[Versão Prévia\] de Audit CIS Microsoft Azure Foundations Benchmark v1.1.0 e implante Extensões de VM específicas para dar suporte aos requisitos de auditoria** da iniciativa de política interna.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência 1:1 ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Garantir que a autenticação multifator esteja habilitada para todos os usuários privilegiados

Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que ajudam você a monitorar quando a autenticação multifator não está habilitada em contas com privilégios do Azure Active Directory.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Garantir que a autenticação multifator esteja habilitada para todos os usuários não privilegiados

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a monitorar quando a autenticação multifator não está habilitada em contas sem privilégios do Azure Active Directory.

- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Garantir que não haja usuários convidados

Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que ajudam você a monitorar as contas convidado que podem precisar ser removidas.

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Verificar se o tipo de preço Standard foi selecionado

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a monitorar redes e máquinas virtuais em que a camada Standard da Central de Segurança não está habilitada.

 - O tipo de preço Standard da Central de Segurança deve ser selecionado

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Verificar se 'Provisionamento automático do agente de monitoramento' está definido como 'Ativo'

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o provisionamento automático do agente do Log Analytics esteja habilitado.

- O provisionamento automático do agente de monitoramento do Log Analytics deve estar habilitado na assinatura

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Garantir que a configuração padrão da política do ASC "Monitorar Atualizações do Sistema" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as atualizações do sistema sejam instaladas em máquinas virtuais.

- As atualizações do sistema devem ser instaladas em suas máquinas

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Garantir que a configuração padrão da política do ASC "Monitorar Vulnerabilidades do SO" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a monitorar vulnerabilidades não corrigidas de máquina virtual.

- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Garantir que a configuração padrão da política do ASC "Monitorar a Proteção do Ponto de Extremidade" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Endpoint Protection esteja habilitado nas máquinas virtuais.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Garantir que a configuração padrão da política do ASC "Monitorar Criptografia de Disco" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os discos de máquinas virtuais estejam criptografados.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Garantir que a configuração de política padrão do ASC "Monitorar Grupos de Segurança de Rede" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a proteger máquinas virtuais voltadas à Internet.

- As regras do grupo de segurança de rede para máquinas virtuais para a Internet devem ser protegidas

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Garantir que a configuração padrão da política do ASC "Monitorar o Firewall do Aplicativo Web" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a proteger máquinas virtuais executando aplicativos Web.

- As regras NSGs para aplicativos Web em IaaS devem ser protegidas

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Garantir que a configuração de política padrão do ASC "Habilitar Monitoramento de NGFW (Firewall de Próxima Geração)" não esteja "Desabilitada"

Esse blueprint atribui definições de política do [Azure Policy](../../../policy/overview.md) que, restringindo o acesso, ajudam a proteger sub-redes e máquinas virtuais contra ameaças. A política da Central de Segurança referenciada por esta recomendação do CIS Microsoft Azure Foundations Benchmark foi substituída por duas novas recomendações. As políticas referenciadas abaixo abordam as novas recomendações.

- As sub-redes devem ser associadas a um Grupo de Segurança de Rede
- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Garantir que a configuração padrão da política do ASC "Monitorar Avaliação de Vulnerabilidade" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a assegurar que vulnerabilidades sejam detectadas e corrigidas.

- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Garantir que a configuração padrão da política do ASC "Monitorar a Criptografia do Storage Blob" não esteja "Desabilitada"

A criptografia do Armazenamento do Azure está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. (Essa é uma funcionalidade padrão do Azure; não há nenhuma atribuição de política.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Garantir que a configuração padrão da política do ASC "Monitorar o Acesso à Rede JIT" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a controlar o acesso a máquinas virtuais.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Garantir que a configuração padrão da política do ASC "Monitorar a Inclusão de Aplicativos Adaptáveis à Lista de Permissões" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que Controles de Aplicativos Adaptáveis o Endpoint Protection esteja habilitado nas máquinas virtuais.

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Garantir que a configuração padrão da política do ASC "Monitorar a Auditoria do SQL" não esteja "Desabilitada"

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria de SQL Server esteja habilitada.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançada no SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Garantir que a configuração padrão da política do ASC "Monitorar a Criptografia do SQL" não esteja "Desabilitada"

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Transparent Data Encryption esteja habilitado em bancos de dados SQL.

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Garantir que a opção 'Emails de contato de segurança' esteja definida

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam adequadamente habilitadas

- Um endereço de email de contato de segurança deve ser fornecido para a sua assinatura

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Garantir que a opção 'Número de telefone de contato de segurança' esteja definida

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam adequadamente habilitadas

- Um número de telefone de contato de segurança deve ser fornecido para a sua assinatura

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Garantir que a opção 'Enviar notificação por email para alertas de severidade alta' seja definida como 'Ativado'

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam adequadamente habilitadas

- A notificação por email para alertas de severidade alta deve ser habilitada

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Garantir que a opção 'Enviar email também para proprietários de assinatura' seja definida como 'Ativado'

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam adequadamente habilitadas

- A notificação por email para o proprietário da assinatura para alertas de severidade alta deve ser habilitada

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Garantir que "Transferência segura necessária" esteja definido como "Habilitado"

Esse blueprint também atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar contas de armazenamento que permitem conexões não seguras.

- A transferência segura para contas de armazenamento deve ser habilitada

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Garantir que a regra de acesso de rede padrão para Contas de Armazenamento estejam definidas como negar

Esse blueprint também atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar contas de armazenamento que permitem acesso irrestrito.

- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 garantir que a opção 'Serviços Confiáveis da Microsoft' esteja habilitada para acesso à conta de armazenamento

Esse blueprint também atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar contas de armazenamento que não permitem acesso de serviços Microsoft confiáveis.

- As contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Garantir que "Auditoria" esteva definido como "Ativado"

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria de SQL Server esteja habilitada. 

- A auditoria deve ser habilitada nas configurações de segurança de dados avançada no SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Garantir que "AuditActionGroups" na política de "auditoria" de um SQL Server esteja definido corretamente

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria de SQL Server esteja configurada adequadamente.

- As configurações de Auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Garantir que a retenção de "Auditoria" seja "maior que 90 dias"

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs de SQL Server sejam retidos por pelo menos 90 dias.

- Os servidores SQL devem ser configurados com um período de retenção de auditoria superior a 90 dias.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Garantir que a "Segurança de Dados Avançada" em um SQL Server esteja definida como "Ativado"

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a segurança de dados avançada seja habilitada em SQL Servers e instâncias gerenciadas do SQL.

- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Garantir que 'Tipos de Detecção de Ameaças' esteja definido como 'Todos'

Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que ajudam você a garantir que a Proteção Avançada contra Ameaças esteja configurada adequadamente em SQL Servers e instâncias gerenciadas do SQL.

- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada do SQL Server
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada da instância gerenciada de SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Garantir que 'Enviar alertas para' esteja configurado

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações da Segurança de Dados Avançada estejam habilitadas adequadamente.

- As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Garantir que 'Serviço de email e coadministradores' esteja 'Habilitado'

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações da Segurança de Dados Avançada estejam habilitadas adequadamente.

- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL
- As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Garantir que o administrador do Azure Active Directory esteja configurado

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que um administrador de Azure Active Directory seja provisionado para SQL Servers.

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Garantir que a 'Criptografia de Dados' esteja definida como 'Ativado' em um Banco de Dados SQL

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Transparent Data Encryption esteja habilitado em bancos de dados SQL.

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Garantir que o protetor de TDE do SQL Server seja criptografado com BYOK (Bring Your Own Key)

Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que ajudam você a garantir que o protetor de TDE para SQL Servers e instâncias gerenciadas do SQL seja criptografado com sua própria chave.

- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- O protetor TDE do SQL Server deve ser criptografado com sua própria chave

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Garantir que a opção 'Impor conexão SSL' esteja definida para 'HABILITADO' para o servidor de banco de dados MySQL

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os servidores de banco de dados MySQL imponham conexões SSL.

- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Garantir que a opção 'Impor conexão SSL' esteja definida para 'HABILITADO' para o servidor de banco de dados PostgreSQL

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os servidores de banco de dados PostgreSQL imponham conexões SSL.

- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Garantir que o parâmetro de servidor 'connection_throttling' seja definido como 'ON' para o servidor de banco de dados PostgreSQL

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a mitigar ataques de força bruta em servidores de banco de dados PostgreSQL.

- A limitação de conexão deve estar habilitada para os servidores de banco de dados PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Garantir que o administrador do Azure Active Directory esteja configurado

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que um administrador de Azure Active Directory seja provisionado para SQL Servers. O CIS Microsoft Azure Foundations Benchmark inclui essa recomendação; no entanto, ela é uma duplicata da [recomendação 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Garantir que exista um perfil de log

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que um perfil de log exista para todas as assinaturas do Azure. 

- As assinaturas do Azure devem ter um perfil de log para o Log de Atividades

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Garantir que a retenção do log de atividades esteja definida para 365 dias ou mais

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs de atividade sejam retidos por pelo menos um ano.

- O log de atividades deve ser retido por pelo menos um ano

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Garantir que o perfil de auditoria capture todas as atividades

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o perfil de log esteja configurado adequadamente.

- O perfil de log do Azure Monitor deve coletar logs para as categorias 'gravar', 'excluir' e 'ação'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Garantir que o perfil de log capture logs de atividade para todas as regiões, incluindo global

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o perfil de log esteja configurado adequadamente.

- O Azure Monitor deve coletar os logs de atividades de todas as regiões

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Garantir que o log do Azure Key Vault esteja 'Habilitado'

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs de diagnóstico estejam habilitados para cofres de chaves.

- Os logs de diagnóstico no Key Vault deve estar habilitados

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Garantir que o Observador de Rede esteja 'Habilitado'

Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Observador de Rede esteja habilitado para todas as regiões em que os recursos estão implantados. Essa política requer uma matriz de parâmetros que especifica todas as regiões aplicáveis. O valor padrão nessa política é 'eastus'.

- O Observador de Rede deve ser habilitado

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Garantir que o 'Disco do SO' seja criptografado

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Disk Encryption esteja habilitado em máquinas virtuais.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Garantir que os 'Discos de dados' sejam criptografados

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Disk Encryption esteja habilitado em máquinas virtuais.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Garantir que os 'Discos não anexados' sejam criptografados

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os discos não anexados sejam criptografados.

- Os discos desconectados devem ser criptografados

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Garantir que apenas as extensões aprovadas sejam instaladas

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que apenas extensões de máquina virtual aprovadas sejam instaladas. Essa política requer uma matriz de parâmetros que especifica todas as extensões de máquina virtual aprovadas. Essa definição de iniciativa de política contém padrões sugeridos que os clientes devem validar. 

 - Somente as extensões aprovadas da VM devem ser instaladas

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Garantir que os Patches de SO mais recentes para todas as máquinas virtuais sejam aplicados

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as atualizações do sistema sejam instaladas em máquinas virtuais.

- As atualizações do sistema devem ser instaladas em suas máquinas

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Garantir que a proteção de ponto de extremidade para todas as máquinas virtuais esteja instalada

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Endpoint Protection esteja habilitado nas máquinas virtuais.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Garantir que o cofre de chaves seja recuperável

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os objetos do cofre de chaves sejam recuperáveis no caso de exclusão acidental.

- Os objetos do Key Vault devem ser recuperáveis

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Habilitar o RBAC (controle de acesso baseado em função) nos Serviços de Kubernetes do Azure

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o controle de acesso baseado em função seja usado para permissões gerenciadas em clusters do serviço do Kubernetes

- \[Versão Prévia\]: O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Garantir que o aplicativo Web redirecione todo o tráfego HTTP para HTTPS no Serviço de Aplicativo do Azure

Esse blueprint atribui uma definição de política do [Azure Policy](../../../policy/overview.md) que ajuda a garantir que aplicativos Web sejam acessíveis somente por conexões seguras.

- Aplicativo Web deve ser acessível somente por HTTPS

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle do blueprint CIS Microsoft Azure Foundations Benchmark, visite o seguinte artigo para saber mais sobre o blueprint ou visite o Azure Policy no portal do Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [Blueprint CIS Microsoft Azure Foundations Benchmark – Visão geral](./index.md)
> [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).