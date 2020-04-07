---
title: Livro de jogadas para atender aos requisitos comuns de segurança | Microsoft Docs
titleSuffix: Azure SQL Database
description: Este artigo fornece requisitos comuns de segurança e práticas recomendadas no Banco de Dados SQL do Azure.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 39747ac0a7133562bed526f44e30bf4a656127c0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673602"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Livro de jogadas para atender aos requisitos comuns de segurança com o banco de dados SQL do Azure

> [!NOTE]
> Este documento fornece práticas recomendadas sobre como resolver requisitos comuns de segurança. Nem todos os requisitos são aplicáveis a todos os ambientes, e você deve consultar seu banco de dados e equipe de segurança sobre quais recursos implementar.

## <a name="solving-common-security-requirements"></a>Resolvendo requisitos comuns de segurança

Este documento fornece orientações sobre como resolver requisitos comuns de segurança para aplicativos novos ou existentes usando o Banco de Dados SQL do Azure. É organizado por áreas de segurança de alto nível. Para lidar com ameaças específicas, consulte a [seção de ameaças de segurança comuns e possíveis mitigações.](#common-security-threats-and-potential-mitigations) Embora algumas das recomendações apresentadas sejam aplicáveis ao migrar aplicativos de on-premises para o Azure, os cenários de migração não são o foco deste documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>A implantação do Banco de Dados SQL do Azure oferece acobertura deste guia

- [Bancos de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [bancos de dados únicos](sql-database-single-database.md) e [pools elásticos](sql-database-elastic-pool.md) em [servidores azure SQL Database](sql-database-servers.md)
- [Instâncias gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>A implantação do SQL oferece ofertas não cobertas neste guia

- SQL Data Warehouse do Azure
- VMs Azure SQL (IaaS)
- SQL Server local

### <a name="audience"></a>Público

Os públicos pretendidos para este guia são clientes que enfrentam perguntas sobre como proteger o Banco de Dados SQL do Azure. Os papéis interessados neste artigo de boas práticas incluem, mas não se limitando a:

- Arquitetos de Segurança
- Gerentes de Segurança
- Oficiais de Conformidade
- Oficiais de Privacidade
- Engenheiros de Segurança

### <a name="using-this-guide"></a><a id="using"></a>Usando este guia

Este documento destina-se como um companheiro à documentação de segurança do [banco de dados Azure SQL](sql-database-security-overview.md) existente.

Salvo disposição em contrário, recomendamos que você siga todas as melhores práticas listadas em cada seção para atingir o respectivo objetivo ou requisito. Para atender a padrões específicos de conformidade de segurança ou práticas recomendadas, importantes controles de conformidade normativa são listados na seção Requisitos ou Metas sempre que aplicável. Estas são as normas e regulamentos de segurança que são referenciados neste artigo:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Controle de acesso, criptografia
- [Práticas de Garantia de Segurança Operacional (OSA)](https://www.microsoft.com/en-us/securityengineering/osa/practices)da Microsoft : Pratique #1-6 e #9
- [Nist Publicação Especial 800-53 Controles de Segurança](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Comentários

Planejamos continuar atualizando as recomendações e melhores práticas listadas aqui. Forneça entrada ou quaisquer correções para este documento usando o **link Feedback** na parte inferior deste artigo.

## <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem diz ser. O Banco de Dados SQL do Azure dá suporte a dois tipos de autenticação:

- Autenticação SQL
- Autenticação do Azure Active Directory

> [!NOTE]
> A autenticação do Azure Active Directory pode não ser suportada para todas as ferramentas e aplicativos de terceiros.

### <a name="central-management-for-identities"></a>Gestão central de identidades

O gerenciamento central de identidade oferece os seguintes benefícios:

- Gerencie contas de grupo e controle permissões de usuário sem duplicar logins em servidores e bancos de dados do Azure SQL.
- Gestão de permissões simplificada e flexível.
- Gerenciamento de aplicações em escala.

**Como implementar:**

- Use a autenticação do Azure Active Directory (Azure AD) para gerenciamento centralizado de identidade.

**Melhores práticas:**

- Crie um inquilino AD do Azure e [crie usuários](../active-directory/fundamentals/add-users-azure-active-directory.md) para representar usuários humanos e criar [diretores de serviços](../active-directory/develop/app-objects-and-service-principals.md) para representar aplicativos, serviços e ferramentas de automação. Os princípios do serviço são equivalentes às contas de serviço no Windows e Linux. 

- Atribuir direitos de acesso aos recursos aos principais do Azure AD através da atribuição de grupo: Criar grupos AD do Azure, conceder acesso a grupos e adicionar membros individuais aos grupos. Em seu banco de dados, crie usuários de banco de dados contidos que mapeiam seus grupos AD do Azure. Para atribuir permissões dentro do banco de dados, coloque os usuários em funções de banco de dados com as permissões apropriadas.
  - Consulte os artigos, [Configure e gerencie a autenticação do Azure Active Directory com SQL](sql-database-aad-authentication-configure.md) e [Use a Azure AD para autenticação com SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > Em uma instância gerenciada, você também pode criar logins que mapeiam os principais do Azure AD no banco de dados principal. Consulte [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- O uso de grupos Azure AD simplifica o gerenciamento de permissões e o proprietário do grupo, e o proprietário dos recursos pode adicionar/remover membros para/do grupo. 

- Crie um grupo separado para o administrador azure AD para servidores SQL DB.

  - Consulte o [artigo, Provision um administrador do Azure Active Directory para o servidor de banco de dados SQL do Azure](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Monitore as alterações de membros do grupo Azure AD usando relatórios de atividade de auditoria do Azure AD. 

- Para uma instância gerenciada, uma etapa separada é necessária para criar o administrador do Azure AD. 
  - Consulte o [artigo, Provision um administrador do Diretório Ativo do Azure para sua instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - A autenticação do Azure AD é registrada em logs de auditoria Do Azure SQL, mas não nos logs de login do Azure AD.
> - As permissões RBAC concedidas no Azure não se aplicam às permissões Do Azure SQL DB. Essas permissões devem ser criadas/mapeadas manualmente no SQL DB usando permissões SQL existentes.
> - No lado do cliente, a autenticação azure AD precisa ter acesso à Internet ou via UDR (User Defined Route, rota definida pelo usuário) para um VNet.
> - O token de acesso Azure AD é armazenado em cache no lado do cliente e sua vida útil depende da configuração do token. Veja o artigo, [Vida útil do token configurável no Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Para obter orientações sobre a solução de problemas de autenticação do Azure AD, consulte o blog a seguir:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Autenticação Multifator (MFA)

> Mencionado em: OSA Practice #2, ISO Access Control (AC)

O Azure Multi-Factor Authentication (MFA) ajuda a fornecer segurança adicional, exigindo mais de uma forma de autenticação.

**Como implementar:**

- [Habilite o MFA](../active-directory/authentication/concept-mfa-howitworks.md) no Azure AD usando acesso condicional e use autenticação interativa. 

- A alternativa é habilitar o MFA para todo o domínio Azure AD ou AD.

**Melhores práticas:**

- Ativar o Acesso Condicional no Azure AD (requer assinatura Premium). 
  - Veja o artigo, [Conditional Access in Azure AD](../active-directory/conditional-access/overview.md).  

- Crie grupos AD do Azure e habilite a política MFA para grupos selecionados usando o Azure AD Conditional Access. 
  - Veja o artigo, [Plan Conditional Access Deployment](../active-directory/conditional-access/plan-conditional-access.md). 

- O MFA pode ser habilitado para todo o Azure AD ou para todo o Active Directory federado com o Azure AD. 

- Use o modo de autenticação Azure AD Interactive para SQL DB onde uma senha é solicitada interativamente, seguida de autenticação MFA:      
  - Use autenticação universal em SSMS. Veja o artigo, [Usando autenticação AAD multifatorial com banco de dados Azure SQL e Azure SQL Data Warehouse (suporte a SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
  - Use a Autenticação Interativa suportada no SQL Server Data Tools (SSDT). Veja o artigo, [suporte ao Azure Active Directory no SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Use outras ferramentas SQL que suportam MFA. 
    - Suporte ao SSMS Wizard para banco de dados de exportação/extração/implantação  
    - [sqlpackage.exe:](https://docs.microsoft.com/sql/tools/sqlpackage)opção '/ua' 
    - [utilitário sqlcmd:](https://docs.microsoft.com/sql/tools/sqlcmd-utility)opção -G (interativo)
    - [utilitário bcp:](https://docs.microsoft.com/sql/tools/bcp-utility)opção -G (interativo) 

- Implemente seus aplicativos para se conectar ao Banco de Dados SQL do Azure usando autenticação interativa com suporte ao MFA. 
  - Veja o artigo, [Conecte-se ao Banco de Dados SQL do Azure com autenticação multifatorial do Azure](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Este modo de autenticação requer identidades baseadas no usuário. Nos casos em que um modelo de identidade confiável é usado que está ignorando a autenticação individual do usuário Azure AD (por exemplo, usando identidade gerenciada para recursos do Azure), o MFA não se aplica.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimizar o uso da autenticação baseada em senha para usuários

> Mencionado em: OSA Practice #4, ISO Access Control (AC)

Os métodos de autenticação baseados em senha são uma forma mais fraca de autenticação. As credenciais podem ser comprometidas ou equivocadamente entregues.

**Como implementar:**

- Use uma autenticação integrada do Azure AD que elimina o uso de senhas.

**Melhores práticas:**

- Use autenticação de login único usando credenciais do Windows. Federar o domínio AD no local com o Azure AD e usar a autenticação integrada do Windows (para máquinas unidas por domínio com o Azure AD).
  - Veja o artigo, [suporte ao SSMS para autenticação Integrada Azure AD](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimizar o uso de autenticação baseada em senha para aplicativos 

> Mencionado em: OSA Practice #4, ISO Access Control (AC)

**Como implementar:**

- Habilite a identidade gerenciada do Azure. Você também pode usar autenticação integrada ou baseada em certificados. 

**Melhores práticas:**

- Use [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Use o Banco de Dados SQL do Azure do serviço de aplicativo com identidade gerenciada (sem alterações de código)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Use autenticação baseada em cert para um aplicativo. 
  - Veja esta [amostra de código](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Use a autenticação Azure AD para domínio federado integrado e máquina de domínio (ver seção acima).
  - Consulte o [aplicativo de amostra para autenticação integrada](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Proteger senhas e segredos

Para casos em que as senhas não são evitáveis, certifique-se de que elas estão seguras.

**Como implementar:**

- Use o Azure Key Vault para armazenar senhas e segredos. Sempre que aplicável, use MFA para banco de dados SQL Do Azure com usuários azure AD.

**Melhores práticas:**

- Se não for possível evitar senhas ou segredos, armazene senhas de usuário e segredos de aplicativos no Azure Key Vault e gerencie o acesso através de políticas de acesso do Key Vault. 

- Várias estruturas de desenvolvimento de aplicativos também podem oferecer mecanismos específicos para proteger segredos no aplicativo. Por exemplo: [ASP.NET aplicativo principal](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Use autenticação SQL para aplicativos legados 

A autenticação SQL refere-se à autenticação de um usuário ao se conectar ao Banco de Dados SQL do Azure usando nome de usuário e senha. Um login precisará ser criado em cada servidor do Banco de Dados SQL ou em uma instância gerenciada, e um usuário criado em cada banco de dados.

**Como implementar:**

- Use autenticação SQL.

**Melhores práticas:**

- Como um admin servidor, crie logins e usuários. A menos que o uso de usuários de banco de dados contidos com senhas, todas as senhas são armazenadas no banco de dados mestre.
  - Consulte o artigo, [Controlando e concedendo acesso ao banco de dados SQL e ao SQL Data Warehouse](sql-database-manage-logins.md).

## <a name="access-management"></a>Gerenciamento de acesso

O gerenciamento de acesso é o processo de controle e gerenciamento do acesso e privilégios dos usuários autorizados ao Banco de Dados SQL do Azure.

### <a name="implement-principle-of-least-privilege"></a>Implementar princípio de menor privilégio

> Mencionado em: FedRamp controla AC-06, NIST: AC-6, OSA Practice #3

O princípio do menor privilégio afirma que os usuários não devem ter mais privilégios do que o necessário para completar suas tarefas. Para obter mais informações, consulte o artigo [Apenas administração suficiente](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Como implementar:**

Atribua apenas as [permissões necessárias](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) para completar as tarefas necessárias:

- No Plano de Dados SQL: 
    - Use permissões granulares e funções de banco de dados definidas pelo usuário (ou funções de servidor no MI): 
        1. Crie as funções necessárias
            - [CRIAR PAPEL](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Criar usuários necessários
            - [CRIAR USUÁRIO](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Adicione usuários como membros às funções 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [FUNÇÃO DE SERVIDOR DE ALTERAÇÃO](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Em seguida, atribua permissões às funções. 
            - [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Certifique-se de não atribuir usuários a funções desnecessárias.

- No Azure Resource Manager:
  - Use funções incorporadas se houver funções RBAC disponíveis ou personalizadas e atribua as permissões necessárias.
    - [Papéis incorporados para a zure](../role-based-access-control/built-in-roles.md) 
    - [Funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md)

**Melhores práticas:**

As seguintes práticas recomendadas são opcionais, mas resultarão em melhor capacidade de gerenciamento e suporte de sua estratégia de segurança: 

- Se possível, comece com o menor conjunto possível de permissões e comece a adicionar permissões uma a uma se houver uma necessidade real (e justificativa) – ao contrário da abordagem oposta: tirar as permissões passo a passo. 

- Abstenha-se de atribuir permissões a usuários individuais. Use funções (funções de banco de dados ou servidor) de forma consistente. As funções ajudam muito com as permissões de emissão de relatórios e de solução de problemas. (O Azure RBAC só suporta a atribuição de permissão através de funções.) 

- Crie e use funções personalizadas com as permissões exatas necessárias. Funções típicas que são usadas na prática: 
  - Implantação de segurança 
  - Administrador 
  - Desenvolvedor 
  - Pessoal de apoio 
  - Auditor 
  - Processos automatizados 
  - Usuário final 
  
- Use funções incorporadas somente quando as permissões das funções corresponderem exatamente às permissões necessárias para o usuário. Você pode atribuir usuários a várias funções. 

- Lembre-se de que as permissões no SQL Server Database Engine podem ser aplicadas nos seguintes escopos. Quanto menor o escopo, menor o impacto das permissões concedidas: 
  - Servidor de banco de dados Azure SQL (funções especiais no banco de dados mestre) 
  - Banco de dados 
  - Esquema
      - É uma prática recomendada usar esquemas para conceder permissões dentro de um banco de dados. (veja também: [Schema-design for SQL Server: recomendações para o design do Schema com a segurança em mente](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objeto (tabela, exibição, procedimento, etc.) 
  > [!NOTE]
  > Não é recomendável aplicar permissões no nível do objeto porque esse nível adiciona complexidade desnecessária à implementação geral. Se você decidir usar permissões de nível de objeto, essas devem ser claramente documentadas. O mesmo se aplica às permissões de nível de coluna, que são ainda menos recomendáveis pelas mesmas razões. Também esteja ciente de que, por padrão, um [DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) de nível de tabela não se sobrepor a um GRANT de nível de coluna. Isso exigiria que os [critérios comuns de conformidade configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) fossem ativados.

- Realize verificações regulares usando [a Avaliação de Vulnerabilidade (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) para testar muitas permissões.

### <a name="implement-separation-of-duties"></a>Implementar separação de deveres

> Mencionado em: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A separação de deveres, também chamada segregação de deveres, descreve o requisito para dividir tarefas sensíveis em múltiplas tarefas que são atribuídas a diferentes usuários. A separação de deveres ajuda a evitar violações de dados.

**Como implementar:**

- Identifique o nível necessário de Separação de Deveres. Exemplos: 
  - Entre ambientes de desenvolvimento/teste e produção 
  - Tarefas sensíveis em termos de segurança versus tarefas de nível de gerenciamento do Administrador de Banco de Dados (DBA) versus tarefas do desenvolvedor. 
    - Exemplos: Auditor, criação de política de segurança para RLS (Role-level Security), implementação de objetos de banco de dados SQL com permissões de DDL.

- Identifique uma hierarquia abrangente de usuários (e processos automatizados) que acessem o sistema.

- Crie funções de acordo com os grupos de usuários necessários e atribua permissões às funções. 
  - Para tarefas de nível de gerenciamento no portal Azure ou via automação PowerShell, use funções RBAC. Encontre uma função incorporada que corresponda ao requisito ou crie uma função RBAC personalizada usando as permissões disponíveis 
  - Crie funções de servidor para tarefas em todo o servidor (criando novos logins, bancos de dados) em uma instância gerenciada. 
  - Crie funções de banco de dados para tarefas de nível de banco de dados.

- Para determinadas tarefas sensíveis, considere criar procedimentos armazenados especiais assinados por um certificado para executar as tarefas em nome dos usuários. 
  - Exemplo: [Tutorial: Assinando procedimentos armazenados com um certificado](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implemente o TDE (Transparent Data Encryption, criptografia de dados transparente) com chaves gerenciadas pelo cliente no Azure Key Vault para permitir a separação de deveres entre o proprietário de dados e o proprietário da segurança. 
  - Consulte o [artigo: Configure chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure a partir do portal Azure](../storage/common/storage-encryption-keys-portal.md). 

- Para garantir que um DBA não possa ver dados considerados altamente sensíveis e que ainda possam fazer tarefas de DBA, você pode usar Always Encrypted com a separação de papéis. 
  - Consulte os artigos, [visão geral do gerenciamento de chaves para sempre criptografado,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [provisionamento de chaves com separação de](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)papéis e rotação de [tecla mestre da coluna com separação de papéis](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- Nos casos em que não é viável, pelo menos, não sem grandes custos e esforços que possam tornar o sistema quase inutilizável, os compromissos podem ser feitos e mitigados através do uso de controles compensadores como: 
  - Intervenção humana nos processos. 
  - Trilhas de auditoria – para obter mais informações sobre Auditoria, veja, [Audite eventos críticos de segurança](#audit-critical-security-events).

**Melhores práticas:**

- Certifique-se de que diferentes contas sejam usadas para ambientes de desenvolvimento/teste e produção. Diferentes contas ajudam a cumprir a separação dos sistemas de produção de & de teste.

- Abstenha-se de atribuir permissões a usuários individuais. Use funções (funções de banco de dados ou servidor) de forma consistente. Ter funções ajuda muito com as permissões de emissão de relatórios e solução de problemas.

- Use funções incorporadas quando as permissões corresponderem exatamente às permissões necessárias – se a união de todas as permissões de várias funções incorporadas levar a uma correspondência de 100%, você também pode atribuir várias funções simultaneamente. 

- Criar e usar funções personalizadas quando funções incorporadas concedem muitas permissões ou permissões insuficientes. 

- As atribuições de funções também podem ser feitas temporariamente, também conhecidas como Separação Dinâmica de Funções (DSD), dentro das etapas de trabalho do Agente SQL no T-SQL ou usando o Azure PIM para funções RBAC. 

- Certifique-se de que os DBAs não tenham acesso às chaves de criptografia ou às lojas-chave e os administradores de segurança com acesso às chaves não têm acesso ao banco de dados por sua vez. 

- Certifique-se sempre de ter uma trilha de auditoria para ações relacionadas à segurança. 

- Você pode recuperar a definição das funções RBAC incorporadas para ver as permissões usadas e criar uma função personalizada com base em trechos e acumulações dessas via PowerShell.

- Uma vez que qualquer membro da função de banco de dados db_owner pode alterar as configurações de segurança, como o TDE (Transparent Data Encryption, criptografia de dados transparente), ou alterar o SLO, essa adesão deve ser concedida com cuidado. No entanto, existem muitas tarefas que exigem privilégios db_owner. Tarefa como alterar qualquer configuração de banco de dados, como alterar opções DB. A auditoria desempenha um papel fundamental em qualquer solução.

- Não é possível restringir as permissões de um db_owner e, portanto, impedir que uma conta administrativa veja os dados do usuário. Se houver dados altamente confidenciais em um banco de dados, o Always Encrypted pode ser usado para impedir que db_owners ou qualquer outro DBA os veja.

> [!NOTE]
> Alcançar a Separação de Deveres (SoD) é um desafio para tarefas relacionadas à segurança ou solucionador de problemas. Outras áreas, como desenvolvimento e funções de usuário final, são mais fáceis de segregar. A maioria dos controles relacionados à conformidade permite o uso de funções de controle alternativo, como auditoria, quando outras soluções não são práticas.

Para os leitores que desejam mergulhar mais fundo no SoD, recomendamos os seguintes recursos: 

- Para o Banco de Dados SQL do Azure:  
  - [Controlando e concedendo acesso de banco de dados a Banco de Dados SQL e SQL Data Warehouse](sql-database-manage-logins.md)
  - [Separação do mecanismo de deveres para o desenvolvedor de aplicativos](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Separação de Deveres no SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Assinando procedimentos armazenados no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Para gerenciamento de recursos do Azure:
  - [Papéis incorporados para a zure](../role-based-access-control/built-in-roles.md) 
  - [Funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md)
  - [Usando o Azure AD Privileged Identity Management para acesso elevado](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Realizar revisões regulares de código

> Mencionado em: PCI: 6.3.2, SOC: SDL-3 

A separação de deveres não se limita aos dados no banco de dados, mas inclui o código do aplicativo. O código malicioso pode potencialmente contornar controles de segurança. Antes de implantar código personalizado para produção, é essencial rever o que está sendo implantado.

**Como implementar:**

- Use uma ferramenta de banco de dados como o Azure Data Studio que suporta o controle de origem. 

- Implementar um processo de implantação de código segregado.

- Antes de se comprometer com o ramo principal, uma pessoa (diferente do autor do código em si) tem que inspecionar o código para potencial elevação de riscos de privilégios, bem como modificações de dados maliciosos para proteger contra fraudes e acesso desonesto. Isso pode ser feito usando mecanismos de controle de fonte.

**Melhores práticas:**

- Padronização: Ajuda a implementar um procedimento padrão que deve ser seguido para quaisquer atualizações de código. 

- A Avaliação de Vulnerabilidades contém regras que verificam permissões excessivas, o uso de algoritmos de criptografia antigos e outros problemas de segurança dentro de um esquema de banco de dados. 

- Verificações adicionais podem ser feitas em um qa ou ambiente de teste usando o Advanced Threat Protection que verifica o código que é vulnerável à injeção de SQL.

- Exemplos do que procurar: 
  - Criação de um usuário ou alterando configurações de segurança de dentro de uma implantação automatizada de atualização de código SQL. 
  - Um procedimento armazenado, que, dependendo dos parâmetros fornecidos, atualiza um valor monetário em uma célula de forma não conforme. 

- Certifique-se de que a pessoa que conduz a revisão é um indivíduo diferente do autor do código de origem e conhecedor de revisões de código e codificação segura.

- Certifique-se de conhecer todas as fontes de mudanças de código. O código pode estar em Scripts T-SQL. Podem ser comandos ad-hoc a serem executados ou implantados em formas de visualizações, funções, gatilhos e procedimentos armazenados. Ele pode fazer parte das definições de trabalho do agente SQL (Passos). Ele também pode ser executado a partir de pacotes SSIS, Azure Data Factory e outros serviços.

## <a name="data-protection"></a>Proteção de dados

A proteção de dados é um conjunto de recursos para proteger informações importantes de compromissos por criptografia ou ofuscação.

> [!NOTE]
> A Microsoft atesta o Banco de Dados SQL do Azure como sendo compatível com FIPS 140-2 Nível 1. Isso é feito depois de verificar o uso rigoroso de algoritmos aceitáveis fips 140-2 nível 1 e instâncias validadas fips 140-2 Nível 1 desses algoritmos, incluindo consistência com comprimentos de chave necessários, gerenciamento de chaves, geração de chaves e armazenamento de chaves. Este atestado destina-se a permitir que nossos clientes respondam à necessidade ou exigência do uso de instâncias validadas de Nível 1 do FIPS 140-2 no processamento de dados ou entrega de sistemas ou aplicativos. Definimos os termos "compatível com o Nível 1 do FIPS 140-2" e "conformidade fips 140-2 Nível 1" usados na declaração acima para demonstrar sua aplicabilidade pretendida ao uso do governo dos EUA e do Canadá do termo "FIPS 140-2 Nível 1 validado". 


### <a name="encrypt-data-in-transit"></a>Criptografe dados em trânsito

> Mencionado em: OSA Practice #6, ISO Control Family: Cryptography

Protege seus dados enquanto os dados se movem entre seu cliente e servidor. Consulte a [Segurança de Rede](#network-security).

### <a name="encrypt-data-at-rest"></a>Criptografar dados em repouso

> Mencionado em: OSA Practice #6, ISO Control Family: Cryptography

Criptografia em repouso é a proteção criptográfica dos dados quando ela é persistida em arquivos de banco de dados, log e backup.

**Como implementar:**

- [O TDE (Transparent Database Encryption, criptografia de banco de dados transparente)](transparent-data-encryption-azure-sql.md) com chaves gerenciadas por serviço é habilitado por padrão para quaisquer bancos de dados criados após 2017 no Banco de Dados SQL do Azure.
- Em uma instância gerenciada, se o banco de dados for criado a partir de uma operação de restauração usando um servidor local, a configuração TDE do banco de dados original será honrada. Se o banco de dados original não tiver o TDE ativado, recomendamos que o TDE seja ativado manualmente para a instância gerenciada.

**Melhores práticas:**

- Não armazene dados que exijam criptografia em repouso no banco de dados principal. O banco de dados mestre não pode ser criptografado com TDE.

- Use chaves gerenciadas pelo cliente no Azure Key Vault se você precisar de maior transparência e controle granular sobre a proteção TDE. O Azure Key Vault permite a capacidade de revogar permissões a qualquer momento para tornar o banco de dados inacessível. Você pode gerenciar centralmente protetores TDE junto com outras chaves ou girar o protetor TDE em seu próprio horário usando o Azure Key Vault.

- Se você estiver usando chaves gerenciadas pelo cliente no Azure Key Vault, siga os artigos, [diretrizes para configurar o TDE com o Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) e [como configurar o Geo-DR com o Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteja dados confidenciais em uso contra usuários não autorizados e altamente privilegiados

Os dados em uso são os dados armazenados na memória do sistema de banco de dados durante a execução de consultas SQL. Se seu banco de dados armazena dados confidenciais, sua organização pode ser necessária para garantir que usuários altamente privilegiados sejam impedidos de visualizar dados confidenciais em seu banco de dados. Usuários de alto privilégio, como operadores Microsoft ou DBAs em sua organização, devem ser capazes de gerenciar o banco de dados, mas impedidos de visualizar e potencialmente exfiltrar dados confidenciais da memória do processo sql server ou consultando o banco de dados.

As políticas que determinam quais dados são sensíveis e se os dados confidenciais devem ser criptografados na memória e não acessíveis aos administradores em texto simples, são específicas para a sua organização e regulamentos de conformidade que você precisa seguir. Consulte o requisito relacionado: [Identifique e marque dados confidenciais](#identify-and-tag-sensitive-data).

**Como implementar:**

- Use [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) para garantir que dados confidenciais não sejam expostos em texto simples no Banco de Dados SQL do Azure, mesmo na memória/em uso. O Always Encrypted protege os dados dos DBAs (Database Administrators, administradores de banco de dados) e administradores em nuvem (ou maus atores que podem se passar por usuários de alto privilégio, mas não autorizados) e dá mais controle sobre quem pode acessar seus dados.

**Melhores práticas:**

- Always Encrypted não é um substituto para criptografar dados em repouso (TDE) ou em trânsito (SSL/TLS). O Always Encrypted não deve ser usado para dados não sensíveis para minimizar o desempenho e o impacto da funcionalidade. O uso do Always Encrypted em conjunto com o TDE e o Transport Layer Security (TLS) é recomendado para proteção abrangente de dados em repouso, em trânsito e em uso. 

- Avalie o impacto de criptografar as colunas de dados confidenciais identificadas antes de implantar o Always Encrypted em um banco de dados de produção. Em geral, always encrypted reduz a funcionalidade de consultas em colunas criptografadas e tem outras limitações, listadas em [Always Encrypted - Feature Details](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Portanto, você pode precisar reprojetar seu aplicativo para reimplementar a funcionalidade, uma consulta não suporta, do lado do cliente ou/e refatora seu esquema de banco de dados, incluindo as definições de procedimentos, funções, visualizações e gatilhos armazenados. Os aplicativos existentes podem não funcionar com colunas criptografadas se não aderirem às restrições e limitações do Always Encrypted. Enquanto o ecossistema de ferramentas, produtos e serviços da Microsoft que suportam o Always Encrypted está crescendo, alguns deles não funcionam com colunas criptografadas. Criptografar uma coluna também pode afetar o desempenho da consulta, dependendo das características da sua carga de trabalho. 

- Gerencie chaves sempre criptografadas com a separação de papéis se estiver usando sempre criptografado para proteger dados de DBAs maliciosos. Com a separação do papel, um admin de segurança cria as chaves físicas. O DBA cria a chave mestre da coluna e os objetos de metadados da chave de criptografia da coluna, descrevendo as chaves físicas, no banco de dados. Durante esse processo, o administrador de segurança não precisa de acesso ao banco de dados, e o DBA não precisa acessar as chaves físicas em texto simples. 
  - Consulte o artigo, [Gerenciando chaves com separação de papéis](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) para obter detalhes. 

- Armazene as chaves principais da coluna no Azure Key Vault para facilitar o gerenciamento. Evite usar o Windows Certificate Store (e, em geral, soluções distribuídas de armazenamento de chaves, em oposição a soluções de gerenciamento de chaves centrais) que dificultem o gerenciamento de chaves. 

- Pense cuidadosamente através das trocas de uso de várias chaves (chave mestre de coluna ou chaves de criptografia de coluna). Mantenha o número de chaves pequeno para reduzir o custo de gerenciamento das chaves. Uma chave-mestre de coluna e uma chave de criptografia de coluna por banco de dados é tipicamente suficiente em ambientes de estado estável (não no meio de uma rotação de chave). Você pode precisar de chaves adicionais se tiver diferentes grupos de usuários, cada um usando chaves diferentes e acessando dados diferentes.  

- Gire as chaves-mestre da coluna de acordo com os requisitos de conformidade. Se você também precisar girar as chaves de criptografia da coluna, considere usar criptografia on-line para minimizar o tempo de inatividade do aplicativo. 
  - Consulte o artigo, [Considerações de Desempenho e Disponibilidade](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Use criptografia determinística se os cálculos (igualdade) nos dados precisarem ser suportados. Caso contrário, use criptografia aleatória. Evite usar criptografia determinística para conjuntos de dados de baixa entropia ou conjuntos de dados com distribuição publicamente conhecida. 

- Se você está preocupado com o acesso de terceiros aos seus dados legalmente sem o seu consentimento, certifique-se de que todos os aplicativos e ferramentas que tenham acesso às chaves e dados em texto simples sejam executados fora do Microsoft Azure Cloud. Sem acesso às chaves, o terceiro não terá como descriptografar os dados a menos que eles contornem a criptografia.

- Sempre criptografado não suporta facilmente a concessão de acesso temporário às chaves (e aos dados protegidos). Por exemplo, se você precisar compartilhar as chaves com um DBA para permitir que o DBA faça algumas operações de limpeza em dados confidenciais e criptografados. A única maneira de revogar a confiabilidade do acesso aos dados do DBA será girar tanto as chaves de criptografia da coluna quanto as chaves-mestre da coluna protegendo os dados, o que é uma operação cara. 

- Para acessar os valores de texto simples em colunas criptografadas, o usuário precisa ter acesso ao CMK que protege as colunas, que está configurada na loja de chaves que segura o CMK. O usuário também precisa ter a **DEFINIÇÃO DE CHAVE MASTER DE QUALQUER COLUNA** e visualizar quaisquer permissões de banco de dados de definição de chave de criptografia de **coluna.**

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controle o acesso dos usuários de aplicativos a dados confidenciais através da criptografia

A criptografia pode ser usada como uma maneira de garantir que apenas usuários específicos de aplicativos que tenham acesso a chaves criptográficas possam visualizar ou atualizar os dados.

**Como implementar:**

- Use a ClC (Cell-level Encryption). Veja o artigo, [Criptografe uma Coluna de Dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) para obter detalhes. 
- Use Sempre Criptografado, mas esteja ciente de sua limitação. As limitações estão listadas abaixo.

**Práticas recomendadas**

Ao usar o CLE:

- Controle o acesso às chaves através de permissões e funções SQL. 

- Use AES (AES 256 recomendado) para criptografia de dados. Algoritmos, tais como RC4, DES e TripleDES, são preteridos e não devem ser usados por causa de vulnerabilidades conhecidas. 

- Proteja as chaves simétricas com chaves/certificados assimétricos (não senhas) para evitar o uso do 3DES. 

- Tenha cuidado ao migrar um banco de dados usando criptografia de nível celular através de exportação/importação (arquivos bacpac). 
  - Veja o artigo, [Recomendações para usar criptografia de nível celular no Banco de Dados SQL do Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) sobre como evitar a perda de chaves ao migrar dados e para outras orientações de boas práticas.

Tenha em mente que o Always Encrypted foi projetado principalmente para proteger dados confidenciais em uso de usuários de alto privilégio do Azure SQL Database (operadores de nuvem, DBAs) - consulte [Proteger dados confidenciais em uso de usuários altamente privilegiados e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Esteja ciente dos seguintes desafios ao usar o Always Encrypted para proteger dados de usuários de aplicativos:

- Por padrão, todos os drivers clientes da Microsoft que suportam always encrypted mantêm um cache global (um por aplicativo) de chaves de criptografia de coluna. Uma vez que um driver cliente adquire uma chave de criptografia de coluna de texto simples entrando em contato com uma loja de chaves segurando uma chave-mestre da coluna, a chave de criptografia da coluna plaintext é armazenada em cache. Isso torna desafiador o isolamento de dados dos usuários de um aplicativo multiusuário. Se o aplicativo se passar por usuários finais ao interagir com uma loja-chave (como o Azure Key Vault), depois que uma consulta do usuário preencher o cache com uma chave de criptografia de coluna, uma consulta subsequente que requer a mesma chave, mas é acionada por outro usuário, usará a chave armazenada em cache. O driver não ligará para a loja de chaves e não verificará se o segundo usuário tem permissão para acessar a chave de criptografia da coluna. Como resultado, o usuário poderá ver os dados criptografados mesmo que o usuário não tenha acesso às chaves. Para alcançar o isolamento dos usuários dentro de um aplicativo multiusuário, você pode desativar o cache da chave de criptografia da coluna. A desativação do cache causará sobrecargas adicionais de desempenho, pois o driver precisará entrar em contato com o armazenamento de chaves para cada operação de criptografia ou descriptografia de dados.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteger os dados contra visualizações não autorizadas por usuários de aplicativos, preservando o formato de dados
Outra técnica para impedir que usuários não autorizados visualizam dados é ofuscar ou mascarar os dados, preservando os tipos e formatos de dados para garantir que os aplicativos do usuário possam continuar a manusear e exibir os dados.

**Como implementar:**

- Use [o Mascaramento dinâmico de dados](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) para ofuscar colunas de tabela.

> [!NOTE]
> Sempre criptografado não funciona com mascaramento dinâmico de dados. Não é possível criptografar e mascarar a mesma coluna, o que implica que você precisa priorizar a proteção de dados em uso versus mascarar os dados para os usuários do seu aplicativo via Mascaramento dinâmico de dados.

**Melhores práticas:**

> [!NOTE]
> O mascaramento dinâmico de dados não pode ser usado para proteger dados de usuários de alto privilégio. As políticas de mascaramento não se aplicam a usuários com acesso administrativo, como db_owner.

- Não permita que os usuários de aplicativos executem consultas ad-hoc (pois eles podem ser capazes de trabalhar em torno do Mascaramento dinâmico de dados).  
  - Veja o artigo, [ignorando o mascaramento usando técnicas de inferência ou força bruta](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) para detalhes.  

- Use uma política de controle de acesso adequada (via permissões SQL, funções, RLS) para limitar as permissões do usuário para fazer atualizações nas colunas mascaradas. Criar uma máscara em uma coluna não impede atualizações dessa coluna. Os usuários que recebem dados mascarados ao consultar a coluna mascarada, podem atualizar os dados se tiverem permissões de gravação.    

-  Mascarar dados dinâmicos não preserva as propriedades estatísticas dos valores mascarados. Isso pode afetar os resultados da consulta (por exemplo, consultas que contêm predicados de filtragem ou adesão aos dados mascarados).

## <a name="network-security"></a>Segurança de rede
Segurança da rede refere-se aos controles de acesso e às práticas recomendadas para proteger seus dados em trânsito para o Banco de Dados SQL do Azure.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Configure meu cliente para se conectar com segurança ao Banco de Dados SQL do Azure 
Práticas recomendadas sobre como impedir que máquinas e aplicativos clientes com vulnerabilidades bem conhecidas (por exemplo, usando protocolos TLS mais antigos e suítes de cifras) se conectem ao Banco de Dados SQL do Azure.

**Como implementar:**

- Certifique-se de que as máquinas clientes conectadas ao Banco de Dados SQL do Azure estejam usando [tLS (Transport Layer Security, segurança de camada de transporte)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Melhores práticas:**

- Configure todos os seus aplicativos e ferramentas para se conectar ao SQL DB com criptografia ativada 
  - Criptografar = On, TrustServerCertificate = Off (ou equivalente com drivers não-Microsoft). 

- Se o aplicativo usar um driver que não suporta TLS ou suportar uma versão mais antiga do TLS, substitua o driver, se possível. Se não for possível, avalie cuidadosamente os riscos de segurança. 

- Reduza os vetores de ataque por meio de vulnerabilidades nas configurações de registro SSL 2.0, SSL 3.0, TLS 1.0 e TLS 1.1, desativando-as em máquinas clientes conectadas ao Banco de Dados SQL do Azure por [configurações de registro TLS (Transport Layer Security) (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Verifique as suítes de cifradisponíveis disponíveis no cliente: [Cipher Suites in TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Especificamente, desabilite o 3DES por [configuração do pedido de suíte de cifra TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Para o Banco de Dados SQL do Azure, a criptografia é aplicada para os tipos de conexão Proxy e Redirect. Se você estiver usando uma instância gerenciada, use o tipo de conexão **Proxy** (padrão) à medida que isso impõe a criptografia do lado do servidor. O tipo de conexão **Redirecionamento** atualmente não suporta a aplicação da criptografia e só está disponível em conexões IP privadas. 

- Para obter mais informações, consulte [Azure SQL Connectivity Architecture - Política de conexão](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Minimizar a superfície de ataque
Minimize o número de recursos que podem ser atacados por um usuário mal-intencionado. Implementar controles de acesso à rede para o Banco de Dados SQL do Azure.

> Mencionado em: Osa Practice #5

**Como implementar:**

Em um servidor de banco de dados SQL do Azure (contendo banco de dados singleton ou pools elásticos):
- Defina permitir o acesso aos serviços do Azure para OFF.

- Use os pontos finais do VNet Service e as regras de firewall do VNet.

- Use o Private Link (visualização).

Em uma instância gerenciada:
- Siga as diretrizes dos [requisitos da Rede.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) 

**Melhores práticas:**

- Restringir o acesso ao Banco de Dados SQL do Azure conectando-se em um ponto final privado (por exemplo, usando um caminho de dados privado): 
  - Uma instância gerenciada pode ser isolada dentro de um VNet para impedir o acesso externo. Aplicativos e ferramentas que estejam no mesmo VNet ou peered na mesma região poderiam acessá-lo diretamente. Aplicativos e ferramentas que estão em diferentes regiões podem usar a conexão VNet-to-VNet ou o peering do circuito ExpressRoute para estabelecer a conexão. O cliente deve usar o NSG (Network Security Groups, grupos de segurança de rede) para restringir o acesso sobre a porta 1433 apenas a recursos que requerem acesso a uma instância gerenciada 
  - Para um servidor sql database (contendo bancos de dados únicos ou pools elásticos), use o recurso [Private Link](sql-database-private-endpoint-overview.md) que fornece um IP privado dedicado para o servidor SQL Database dentro do seu VNet. Você também pode usar [os pontos finais do VNet Service com as Regras de Firewall VNet](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso aos seus servidores do Banco de Dados SQL.
  - Os usuários móveis devem usar conexões VPN ponto a ponto para se conectar sobre o caminho de dados.
  - Os usuários conectados à sua rede local devem usar a conexão VPN local-a-site ou o ExpressRoute para se conectar sobre o caminho de dados.

- Você pode acessar o Banco de Dados SQL do Azure conectando-se a um ponto final público (por exemplo, usando um caminho de dados público). Devem ser consideradas as seguintes práticas recomendadas: 
  - Para um servidor sql database, use [regras de firewall IP](sql-database-firewall-configure.md) para restringir o acesso apenas a endereços IP autorizados.
  - Para uma instância gerenciada, use o NSG (Network Security Groups, grupos de segurança de rede) para restringir o acesso sobre a porta 3342 apenas aos recursos necessários. Para obter mais informações, consulte [Use uma instância gerenciada do Azure SQL Database com segurança com pontos finais públicos](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Um ponto final público de instância gerenciada não é habilitado por padrão e deve ser explicitamente habilitado. Se a política da empresa não permitir o uso de pontos finais públicos, use a [Política do Azure](../governance/policy/overview.md) para evitar a habilitação de pontos finais públicos em primeiro lugar.

- Configurar componentes de rede Azure: 
  - Siga [as melhores práticas do Azure para segurança de rede.](../security/fundamentals/network-best-practices.md)
  - Planejar a configuração da Rede Virtual (VNet) por práticas recomendadas descritas na [Rede Virtual Do Azure frequentemente fez perguntas (FAQ)](../virtual-network/virtual-networks-faq.md) e planejar. 
  - Segmente um VNet em várias sub-redes e atribua recursos para função semelhante à mesma sub-rede (por exemplo, recursos front-end vs back-end).
  - Use [NSGs (Network Security Groups, grupos de segurança de rede)](../virtual-network/security-overview.md) para controlar o tráfego entre sub-redes dentro do limite Do Azure VNet.
  - Habilite [o Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) para sua assinatura para monitorar o tráfego de rede de entrada e saída.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Configure o Power BI para conexões seguras no Banco de Dados SQL do Azure

**Melhores práticas:**

- Para o Power BI Desktop, use o caminho de dados privados sempre que possível. 

- Certifique-se de que o Power BI Desktop está se conectando usando o TLS1.2 definindo a chave de registro na máquina cliente de acordo com as configurações de registro [TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Restringir o acesso de dados para usuários específicos via [RLS (Row-level security, segurança em nível de linha) com power bi](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Para o Serviço power bi, use o [gateway de dados no local,](https://docs.microsoft.com/power-bi/service-gateway-onprem)tendo em mente [limitações e considerações](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Configure o Serviço de Aplicativos para conexões seguras no Banco de Dados SQL do Azure

**Melhores práticas:**

- Para um aplicativo web simples, conectar-se sobre o ponto final público requer a configuração **Permitir que os Serviços Do Zure on.** 

- [Integre seu aplicativo com uma Rede Virtual Do Azure](../app-service/web-sites-integrate-with-vnet.md) para conectividade de caminho de dados privado satisco a uma instância gerenciada. Opcionalmente, você também pode implantar um Aplicativo Web com [Ambientes de Serviço de Aplicativo (ASE).](../app-service/environment/intro.md) 

- Para o Aplicativo Web com aplicativo web integrado ASE ou VNet conectado a um banco de dados no servidor SQL Database, você pode usar [os pontos finais do VNet Service e as Regras](sql-database-vnet-service-endpoint-rule-overview.md) de Firewall VNet para limitar o acesso a partir de uma VNet e sub-rede específicas. Em seguida, **defina Permitir que os serviços do Azure** desatireem. Você também pode conectar a ASE a uma instância gerenciada em um caminho de dados privado.  

- Certifique-se de que seu Aplicativo Web está configurado de acordo com o artigo, [Práticas recomendadas para proteger aplicativos Web e móveis Do PaaS usando o Azure App Service](../security/security-paas-applications-using-app-services.md). 

- Instale [o WAF (Web Application Firewall, firewall de aplicativos da Web)](../application-gateway/waf-overview.md) para proteger seu aplicativo web contra explorações e vulnerabilidades comuns.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Configure a hospedagem do Azure VM para conexões seguras no Banco de Dados SQL do Azure

**Melhores práticas:**

- Use uma combinação de regras de Permitir e Negar nos NSGs das VMs do Azure para controlar quais regiões podem ser acessadas a partir da VM.

- Certifique-se de que sua VM está configurada de acordo com o artigo, [práticas recomendadas de segurança para cargas de trabalho IaaS no Azure](../security/azure-security-iaas.md).

- Certifique-se de que todas as VMs estão associadas a uma VNet e uma sub-rede específicas. 

- Avalie se você precisa da rota padrão 0.0.0.0/Internet por orientação sobre [o túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Se sim – por exemplo, sub-rede front-end – mantenha a rota padrão.
  - Se não – por exemplo, sub-rede de nível intermediário ou back-end – então habilitar o túnel de força para que nenhum tráfego entre na Internet para chegar ao local (também conhecido como cross-premises). 

- Implemente [rotas padrão opcionais](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se você estiver usando peering ou conectando-se a locais. 

- Implemente [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md#user-defined) se você precisar enviar todo o tráfego no VNet para um dispositivo virtual de rede para inspeção de pacotes. 

- Use [os pontos finais do VNet Service](sql-database-vnet-service-endpoint-rule-overview.md) para acesso seguro a serviços PaaS, como o Azure Storage, através da rede backbone do Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Proteger contra ataques de Negação distribuída de Serviço (DDoS)
Os ataques de Negação distribuída de Serviço (DDoS) são tentativas de um usuário mal-intencionado de enviar uma inundação de tráfego de rede para o Azure SQL Database com o objetivo de sobrecarregar a infra-estrutura do Azure e fazê-lo rejeitar logins e cargas de trabalho válidos.

> Mencionado em: OSA Practice #9

**Como implementar:**

A proteção DDoS é habilitada automaticamente como parte da Plataforma Azure. Inclui monitoramento de tráfego sempre ligado e mitigação em tempo real de ataques em nível de rede em pontos finais públicos. 

- Use [o Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) para monitorar endereços IP públicos associados aos recursos implantados em VNets.

- Use [o Advanced Threat Protection for Azure SQL Database](sql-database-threat-detection-overview.md) para detectar ataques de Negação de Serviço (DoS) contra bancos de dados.

**Melhores práticas:**

- Seguir as práticas descritas no [Minimize Attack Surface](#minimize-attack-surface) ajuda a minimizar as ameaças de ataque DDoS. 

- O alerta de **credenciais SQL** de força avançada de proteção contra ameaças ajuda a detectar ataques de força bruta. Em alguns casos, o alerta pode até distinguir cargas de trabalho de teste de penetração. 

- Para aplicativos de hospedagem azure VM conectados ao Banco de Dados SQL: 
  - Siga a recomendação para restringir o acesso através de pontos finais voltados para a Internet no Azure Security Center. 
  - Use conjuntos de escala de máquina virtual para executar várias instâncias do seu aplicativo em VMs do Azure. 
  - Desabilite RDP e SSH da Internet para evitar ataques de força bruta. 

## <a name="monitoring-logging-and-auditing"></a>Monitoramento, Registro e Auditoria  
Esta seção refere-se a recursos para ajudá-lo a detectar atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Ele também descreve as práticas recomendadas para configurar a auditoria de banco de dados para rastrear e capturar eventos de banco de dados.

### <a name="protect-databases-against-attacks"></a>Proteger bancos de dados contra ataques 
A proteção avançada contra ameaças permite detectar e responder a possíveis ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anômalas.

**Como implementar:**

- Use [o Advanced Threat Protection for SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) para detectar tentativas incomuns e potencialmente prejudiciais para acessar ou explorar bancos de dados, incluindo:
  - Ataque de injeção SQL.
  - Roubo/vazamento de credenciais.
  - Abuso de privilégios.
  - Exfiltração de dados.

**Melhores práticas:**

- Configure [ADS (Advanced Data Security, segurança de dados avançados)](sql-database-advanced-data-security.md#getting-started-with-ads) para o Banco de Dados SQL do Azure para um servidor específico do Banco de Dados SQL ou uma instância gerenciada. Você também pode configurar o ADS para todos os servidores do Banco de Dados SQL e instâncias gerenciadas em uma assinatura, mudando para o [nível Azure Security Center Standard](../security-center/security-center-pricing.md). 

- Para uma experiência completa de investigação, recomenda-se habilitar a [Auditoria de Banco de Dados SQL.](sql-database-auditing.md) Com a auditoria, você pode rastrear eventos de banco de dados e escrevê-los em um registro de auditoria em uma conta do Azure Storage ou no espaço de trabalho do Azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Audite eventos críticos de segurança
O rastreamento de eventos de banco de dados ajuda a entender a atividade do banco de dados. Você pode obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou suspeitas de violações de segurança. Também permite e facilita a adesão às normas de conformidade. 

**Como implementar:**

- Habilite a [Auditoria de Banco de Dados SQL](sql-database-auditing.md) para rastrear eventos de banco de dados e escrevê-los em um registro de auditoria em sua conta de armazenamento Azure, espaço de trabalho do Log Analytics (visualização) ou Hubs de Eventos (visualização). 

- Os logs de auditoria podem ser gravados em uma conta do Azure Storage, em um espaço de trabalho do Log Analytics para consumo por logs do Monitor Do Azure ou no hub de eventos para consumo usando o hub de eventos. Você pode configurar qualquer combinação dessas opções, e os logs de auditoria serão gravados em cada uma. 

**Melhores práticas:**

- Ao configurar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md) em seu servidor de banco de dados para auditar eventos, todos os bancos de dados existentes e recém-criados nesse servidor serão auditados.
- A política de auditoria padrão inclui todas as ações (consultas, procedimentos armazenados e logins bem-sucedidos e com falha) contra os bancos de dados, o que pode resultar em um alto volume de logs de auditoria. Recomenda-se que os clientes configurem auditoria [para diferentes tipos de ações e grupos de ação usando o PowerShell](sql-database-auditing.md#manage-auditing). A configuração disso ajudará a controlar o número de ações auditadas e minimizará o risco de perda de eventos. A configuração de auditoria personalizada permite que os clientes capturem apenas os dados de auditoria necessários.
- Os registros de auditoria podem ser consumidos diretamente no [portal do Azure](https://portal.azure.com/)ou no local de armazenamento configurado. 


> [!NOTE]
> Permitir a auditoria no Log Analytics incorrerá em custos com base nas taxas de ingestão. Por favor, esteja ciente do custo associado com o uso dessa [opção](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os registros de auditoria em uma conta de armazenamento DoZure. 

**Outros recursos**:

- [Auditoria do banco de dados SQL](sql-database-auditing.md)
- [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Registros de auditoria seguros
Restringir o acesso à conta de armazenamento para apoiar a separação de deveres e separar o DBA dos Auditores. 

**Como implementar:**

- Ao salvar os logs de auditoria no Armazenamento Azure, certifique-se de que o acesso à conta de armazenamento esteja restrito aos princípios mínimos de segurança. Controle quem tem acesso à conta de armazenamento.
    - Para obter mais informações, consulte [Autorizando o acesso ao Armazenamento Azure](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Melhores práticas:**

- Controlar o acesso à meta de auditoria é um conceito-chave na separação do DBA dos Auditores. 

- Ao auditar o acesso a dados confidenciais, considere proteger os dados com criptografia de dados para evitar vazamento de informações ao Auditor. Para obter mais informações, consulte a seção [Proteja dados confidenciais em uso de usuários não autorizados e altamente privilegiados.](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)

## <a name="security-management"></a>Gerenciamento de Segurança

Esta seção descreve os diferentes aspectos e melhores práticas para gerenciar a postura de segurança de seus bancos de dados. Ele inclui práticas recomendadas para garantir que seus bancos de dados sejam configurados para atender aos padrões de segurança, para descobrir e classificar e rastrear o acesso a dados potencialmente sensíveis em seus bancos de dados. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Certifique-se de que o banco de dados está configurado para atender às práticas recomendadas de segurança 

Melhore proativamente a segurança do banco de dados, descobrindo e corrigindo possíveis vulnerabilidades do banco de dados.

**Como implementar:**

- Habilite o [VA (SQL Vulnerability Assessment, avaliação de vulnerabilidades sql)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) para escanear seu banco de dados para obter problemas de segurança e executar automaticamente em seus bancos de dados.

**Melhores práticas:**

- Inicialmente, execute va em seus bancos de dados e iterado remediando verificações falhadas que se opõem às práticas recomendadas de segurança. Configure linhas de base para configurações aceitáveis até que a varredura saia _limpa_ou todas as verificações já passaram.  

- Configure varreduras periódicas recorrentes para executar uma vez por semana e configure a pessoa relevante para receber e-mails sumários. 

- Revise o resumo do VA após cada varredura semanal. Para quaisquer vulnerabilidades encontradas, avalie a deriva do resultado da varredura anterior e determine se a verificação deve ser resolvida. Revise se há uma razão legítima para a mudança na configuração.   

- Resolver verificações e atualizar linhas de base quando relevante. Crie itens de ticket para resolver ações e rastreie-os até que sejam resolvidos. 

**Outros recursos**:

- [Avaliação de Vulnerabilidades SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [O serviço de Avaliação de Vulnerabilidades do SQL ajuda a identificar vulnerabilidades de banco de dados](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificar e marcar dados confidenciais 

Descubra colunas que potencialmente contenham dados confidenciais. O que é considerado dados confidenciais depende fortemente do cliente, da regulação da conformidade, etc., e precisa ser avaliado pelos usuários responsáveis por esses dados. Classifique as colunas para usar cenários avançados de auditoria e proteção baseados em sensibilidade. 

**Como implementar:**

- Use [a SQL Data Discovery and Classification](sql-database-data-discovery-and-classification.md) para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. 
  - Veja as recomendações de classificação criadas pela descoberta automatizada no painel de classificação e de detecção de dados SQL. Aceite as classificações relevantes, de forma que seus dados confidenciais sejam marcados persistentemente com rótulos de classificação. 
  - Adicione manualmente classificações para quaisquer campos de dados confidenciais adicionais que não foram descobertos pelo mecanismo automatizado. 
- Para obter mais informações, consulte [SQL Data Discovery & Classification](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Melhores práticas:**

- Monitore o painel de classificação regularmente para uma avaliação precisa do estado de classificação do banco de dados. Um relatório sobre o estado de classificação do banco de dados pode ser exportado ou impresso para fins de conformidade e auditoria.

- Monitore continuamente o status dos dados confidenciais recomendados na Avaliação de Vulnerabilidade sql. Rastreie a regra de detecção de dados confidenciais e identifique qualquer deriva nas colunas recomendadas para classificação.  

- Use a classificação de forma adaptada às necessidades específicas da sua organização. Personalize sua política de proteção de informações (rótulos de sensibilidade, tipos de informações, lógica de detecção) na política [de proteção de informações SQL](../security-center/security-center-info-protection-policy.md) no Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Rastrear o acesso a dados confidenciais 
Monitore quem acessa dados confidenciais e capture consultas sobre dados confidenciais em registros de auditoria.

**Como implementar:**

- Use a auditoria SQL e a classificação de dados em combinação. 
  - No seu registro [de auditoria do banco de dados SQL,](sql-database-auditing.md) você pode rastrear o acesso especificamente a dados confidenciais. Você também pode visualizar informações como os dados que foram acessados, bem como seu rótulo de sensibilidade. Para obter mais informações, consulte [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) and [Auditing access to sensitive data](sql-database-data-discovery-and-classification.md#audit-sensitive-data). 

**Melhores práticas:**

- Veja as melhores práticas para as seções Auditoria e Classificação de Dados: 
  - [Audite eventos críticos de segurança](#audit-critical-security-events) 
  - [Identificar e marcar dados confidenciais](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualize o status de segurança e conformidade 

Use um sistema unificado de gerenciamento de segurança de infra-estrutura que fortaleça a postura de segurança de seus data centers (incluindo bancos de dados SQL). Veja uma lista de recomendações sobre a segurança de seus bancos de dados e status de conformidade.

**Como implementar:**

- Monitore recomendações de segurança relacionadas ao SQL e ameaças ativas no [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Ameaças comuns à segurança e potenciais atenuações

Esta seção ajuda você a encontrar medidas de segurança para proteger contra certos vetores de ataque. Espera-se que a maioria das mitigações possam ser alcançadas seguindo uma ou mais das diretrizes de segurança acima.

### <a name="security-threat-data-exfiltration"></a>Ameaça de segurança: exfiltração de dados

Exfiltração de dados é a cópia, transferência ou recuperação não autorizada de dados de um computador ou servidor. Veja uma definição para [exfiltração de dados](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipédia.

A conexão com o servidor Azure SQL Database em um ponto final público apresenta um risco de exfiltração de dados, pois exige que os clientes abram seus firewalls para IPs públicos.  

**Cenário 1**: Um aplicativo em uma VM Azure se conecta a um banco de dados em um servidor de banco de dados SQL Do Azure. Um ator desonesto tem acesso à VM e a compromete. Nesse cenário, a exfiltração de dados significa que uma entidade externa que usa o VM desonesto se conecta ao banco de dados, copia dados pessoais e os armazena em um armazenamento blob ou em um Banco de Dados SQL diferente em uma assinatura diferente.

**Cenário 2**: A Rouge DBA. Esse cenário é frequentemente levantado por clientes sensíveis à segurança de indústrias regulamentadas. Neste cenário, um usuário de alto privilégio pode copiar dados do Azure SQL Database para outra assinatura não controlada pelo proprietário dos dados.

**Mitigações potenciais:**

Hoje, o Azure SQL Database oferece as seguintes técnicas para mitigar ameaças de exfiltração de dados: 

- Use uma combinação de regras de Permitir e Negar nos NSGs das VMs do Azure para controlar quais regiões podem ser acessadas a partir da VM. 
- Se estiver usando um servidor de banco de dados SQL do Azure (contendo bancos de dados singleton ou pools elásticos), defina as seguintes opções:
  - Permitir que os serviços do Azure se desliguem.
  - Apenas permita o tráfego da sub-rede que contém sua VM Do Azure, configurando uma regra do Firewall VNet.
  - Use [o Private Link](sql-database-private-endpoint-overview.md)
- Para uma instância gerenciada, o uso de acesso IP privado por endereços padrão é a primeira preocupação de exfiltração de dados de uma VM desonesto. Ative o recurso de delegação de sub-rede em uma sub-rede para definir automaticamente a política mais restritiva em uma sub-rede de instância gerenciada.
- A preocupação do Rogue DBA é mais exposta com uma instância gerenciada, pois possui uma área de superfície maior e os requisitos de rede são visíveis para os clientes. A melhor atenuação para isso é aplicar todas as práticas neste guia de segurança para evitar o cenário Rogue DBA em primeiro lugar (não apenas para exfiltração de dados). Sempre Criptografado é um método para proteger dados confidenciais, criptografando-os e mantendo a chave inacessível para o DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspectos de segurança da continuidade e disponibilidade de negócios

A maioria dos padrões de segurança abordam a disponibilidade de dados em termos de continuidade operacional, alcançada susceptindo a implementação de recursos de redundância e fail-over para evitar pontos únicos de falha. Para cenários de desastres, é uma prática comum manter backups de arquivos de dados e log.A seção a seguir fornece uma visão geral de alto nível dos recursos incorporados ao Azure. Ele também fornece opções adicionais que podem ser configuradas para atender a necessidades específicas: 

- O Azure oferece banco de dados de alta disponibilidade incorporada: [alta disponibilidade e Banco de Dados SQL do Azure](sql-database-high-availability.md) 

- O nível Business Critical inclui grupos de failover, zonas de multidisponibilidade, backups de log completos e diferenciais e backups de restauração de ponto no tempo habilitados por padrão:  
  - [Banco de dados SQL de alta disponibilidade e Azure - Configuração redundante da zona](sql-database-high-availability.md#zone-redundant-configuration)
  - [Backups automatizados](sql-database-automated-backups.md)
  - [Recuperar um banco de dados SQL do Azure usando backups automatizados de banco de dados - Restauração point-in-time](sql-database-recovery-using-backups.md#point-in-time-restore)

- Recursos adicionais de continuidade de negócios, como grupos de failover automáticos em diferentes geos do Azure, podem ser configurados conforme descrito aqui: [Visão geral da continuidade de negócios com o Banco de Dados SQL do Azure](sql-database-business-continuity.md)

## <a name="next-steps"></a>Próximas etapas

- Veja [uma visão geral dos recursos de segurança do Banco de Dados SQL do Azure](sql-database-security-overview.md)
