---
title: Visão geral de segurança
description: Saiba mais sobre a segurança do SQL Server e Banco de Dados SQL do Azure, incluindo as diferenças entre a nuvem e o SQL Server local.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 2a4c6dd3dd6f1bb2f15e31226086c73fb8e63521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124840"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma visão geral dos recursos de segurança do Banco de Dados SQL do Azure

Este artigo apresenta os conceitos básicos da proteção da camada de dados de um aplicativo que usa o Banco de Dados SQL do Azure. A estratégia de segurança descrita segue a abordagem de defesa em profundidade em camadas conforme mostrado na imagem abaixo e se movimenta de fora para dentro:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança de rede

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para aplicativos na nuvem e empresariais. Para ajudar a proteger os dados do cliente, os firewalls impedem o acesso de rede ao servidor de banco de dados até que ele seja concedido explicitamente com base no endereço IP ou na origem do tráfego de rede virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall de IP permitem acesso a bancos de dados com base no endereço IP de origem de cada solicitação. Para saber mais, confira [Overview of Azure SQL Database and SQL Data Warehouse firewall rules](sql-database-firewall-configure.md) (Visão geral de regras de firewall do Banco de Dados SQL do Azure e do SQL Data Warehouse).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

[Os pontos finais de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) ampliam sua conectividade de rede virtual sobre o backbone do Azure e permitem que o Banco de Dados SQL do Azure identifique a sub-rede virtual da qual o tráfego se origina. Para permitir que o tráfego alcance o Banco de Dados SQL do Azure, use as [marcas de serviço](../virtual-network/security-overview.md) do SQL para permitir o tráfego de saída por meio de Grupos de Segurança de Rede.

[As regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) permitem que o Banco de Dados SQL do Azure só aceite comunicações enviadas de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> O controle do acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Para saber mais sobre a configuração de rede necessária, confira [conectar-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gerenciamento de acesso

> [!IMPORTANT]
> O gerenciamento de bancos de dados e de servidores de banco de dados no Azure é controlado por atribuições de função da sua conta de usuário do portal. Para saber mais sobre esse artigo, confira [Controle de acesso baseado em função no Portal do Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem diz ser. O Banco de Dados SQL do Azure dá suporte a dois tipos de autenticação:

- **Autenticação SQL:**

    A autenticação do banco de dados SQL refere-se à autenticação de um usuário ao se conectar ao [Banco de Dados SQL do Azure](sql-database-technical-overview.md) usando nome de usuário e senha. Durante a criação do servidor de banco de dados do banco de dados, o logon “Administrador do servidor” com um nome de usuário e senha deve ser especificado. Usando essas credenciais, um “administrador do servidor” pode autenticar-se em qualquer banco de dados nesse servidor de banco de dados como o proprietário do banco de dados. Depois disso, os usuários e logons do SQL adicionais podem ser criados pelo administrador do servidor, que permite que os usuários se conectem usando nome de usuário e senha.

- **Autenticação do Azure Active Directory**:

    A autenticação do Azure Active Directory é um mecanismo de conexão ao Banco de [Dados SQL do Azure](sql-database-technical-overview.md) e [ao SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (Azure AD). A autenticação do Azure AD permite que os administradores gerenciem centralmente as identidades e permissões de usuários de banco de dados juntamente com outros serviços da Microsoft em uma única localização central. Isso inclui a minimização do armazenamento de senha e habilita políticas centralizadas de rotação de senha.

     Um administrador do servidor chamado o **administrador do Active Directory** deve ser criado para usar autenticação do Azure AD com o Banco de Dados SQL. Para saber mais, confira [Connecting to SQL Database By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Conectando-se ao Banco de Dados SQL usando a Autenticação do Azure Active Directory). A autenticação do Azure AD dá suporte a contas gerenciadas e federadas. As contas federadas dão suporte a grupos e usuários do Windows para um domínio de cliente federado com o Azure AD.

    Mais opções de autenticação do Azure AD disponíveis são conexões de [Autenticação Universal do Active Directory para SQL Server Management Studio](sql-database-ssms-mfa-authentication.md), incluindo [Autenticação Multifator](../active-directory/authentication/concept-mfa-howitworks.md) e [Acesso Condicional](sql-database-conditional-access.md).

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores no Azure é controlado pelas atribuições de função da sua conta de usuário do portal. Para saber mais sobre esse artigo, confira [Controle de acesso baseado em função no Portal do Azure](../role-based-access-control/overview.md). O controle do acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Consulte o artigo a seguir sobre [a conexão a uma instância gerenciada](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um usuário em um Banco de Dados SQL do Azure e determina o que o usuário tem permissão para fazer. As permissões são controladas adicionando contas de usuário às [funções do banco](/sql/relational-databases/security/authentication-access/database-level-roles) de dados e atribuindo permissões em nível de banco de dados a essas funções ou concedendo ao usuário certas [permissões de nível de objeto](/sql/relational-databases/security/permissions-database-engine). Para saber mais, confira [Logins and users](sql-database-manage-logins.md) (Logons e usuários)

Como uma prática recomendada, crie funções personalizadas quando necessário. Adicione os usuários à função com o mínimo de privilégios necessários para fazer sua função de trabalho. Não atribua permissões diretamente aos usuários. A conta administrativa do servidor é um membro da função db_owner incorporada, que possui permissões extensas e só deve ser concedida a poucos usuários com funções administrativas. Para aplicativos de banco de dados SQL do Azure, use o [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do chamado módulo ou usar [Funções](/sql/relational-databases/security/authentication-access/application-roles) de Aplicativo com permissões limitadas. Essa prática garante que o aplicativo que se conecta ao banco de dados tenha o menor privilégio necessário pelo aplicativo. Seguir essas melhores práticas também promove a separação de deveres.

### <a name="row-level-security"></a>Segurança em nível de linha

A segurança em nível de linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário executando uma consulta (por exemplo, associação a grupo ou contexto de execução). A Segurança em Nível de Linha também pode ser usada para implementar conceitos personalizados de segurança baseados em rótulos. Para obter mais informações, consulte [segurança no nível de linha](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

O Banco de Dados SQL protege os dados do cliente fornecendo funcionalidades de auditoria e de detecção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria SQL em logs do Monitor do Azure e hubs de eventos

A Auditoria do Banco de Dados SQL rastreia as atividades do banco de dados e ajuda a manter a conformidade com os padrões de segurança registrando eventos de banco de dados em um log de auditoria em uma conta de Armazenamento do Azure de propriedade do cliente. A auditoria permite que os usuários monitorem as atividades do banco de dados em andamento, além de analisar e investigar a atividade de histórico para identificar possíveis ameaças ou suspeitas de violações de segurança e de abuso. Para obter mais informações, consulte Iniciar com [auditoria de banco de dados SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada contra Ameaças

O Advanced Threat Protection está analisando seus registros do SQL Server para detectar comportamentos incomuns e tentativas potencialmente prejudiciais de acessar ou explorar bancos de dados. Alertas são criados para atividades suspeitas, como injeção de SQL, infiltração de dados potenciais e ataques de força bruta ou para anomalias em padrões de acesso para capturar escalações de privilégios e uso de credenciais violadas. Os alertas são vistos do Centro de [Segurança do Azure,](https://azure.microsoft.com/services/security-center/)onde os detalhes das atividades suspeitas são fornecidos e recomendações para novas investigações, juntamente com ações para mitigar a ameaça. O Advanced Threat Protection pode ser ativado por servidor por uma taxa adicional. Para obter mais informações, consulte [Iniciar com o SQL Database Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Protocolo TLS (Criptografia em trânsito)

O Banco de Dados SQL protege os dados do cliente por meio da criptografia de dados em movimento com o [protocolo TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

O Sql Server impõe a criptografia (SSL/TLS) o tempo todo para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de **Encrypt** ou **TrustServerCertificate** na seqüência de conexão.

Como uma prática recomendada, recomendo que na seqüência de conexões do seu aplicativo você especifique uma conexão criptografada e _**não**_ confie no certificado do servidor. Isso força seu aplicativo a verificar o certificado do servidor e, portanto, evita que seu aplicativo fique vulnerável a ataques do tipo do tipo intermediário.

Por exemplo, ao usar o driver ADO.NET, isso é realizado via **Encrypt=True** e **TrustServerCertificate=False**. Se você obtiver sua cadeia de conexão do portal do Azure, ela terá as configurações corretas.

> [!IMPORTANT]
> Observe que alguns drivers não-Microsoft não podem usar TLS por padrão ou confiar em uma versão mais antiga do TLS (<1.2) para funcionar. Neste caso, o SQL Server ainda permite que você se conecte ao seu banco de dados. No entanto, recomendamos que você avalie os riscos de segurança de permitir que esses drivers e aplicativos se conectem ao Banco de Dados SQL, especialmente se você armazenar dados confidenciais.
>
> Para obter mais informações sobre TLS e conectividade, consulte [considerações da TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Criptografia em repouso)

A [TDE (Transparent Data Encryption) para Banco de Dados SQL do Azure](transparent-data-encryption-azure-sql.md) adiciona uma camada de segurança para ajudar a proteger os dados em repouso contra acesso não autorizado ou offline para backups ou arquivos brutos. Os cenários comuns incluem o roubo de datacenter ou a eliminação não segura de hardware ou de mídia como discos rígidos e fitas de backup.A TDE criptografa todo o banco de dados usando um algoritmo de criptografia AES, que não exige que os desenvolvedores de aplicativos façam alterações em aplicativos existentes.

No Azure, todos os bancos de dados SQL recém-criados são criptografados por padrão e a chave de criptografia de banco de dados é protegida por um certificado de servidor interno.  A rotação e a manutenção de certificados são gerenciadas pelo serviço e não requerem a intervenção do usuário. Os clientes que preferirem assumir controle das chaves de criptografia podem gerenciá-las no [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gerenciamento de chaves com o Azure Key Vault

O suporte BYOK [(Bring Your Own Key)](transparent-data-encryption-byok-azure-sql.md) para TDE [(Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) permite que os clientes assumam a propriedade da rotação e gerenciamento de chaves usando o [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), sistema de gerenciamento de chaves externas baseado em nuvem do Azure. Se o acesso do banco de dados ao cofre de chaves for revogado, um banco de dados não poderá ser descriptografado nem lido na memória. O Azure Key Vault fornece uma plataforma de gerenciamento central de chaves, utiliza HSMs (Módulos de Segurança de Hardware) rigidamente monitorados e permite a separação de funções entre o gerenciamento de chaves e dados para ajudar nos requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Criptografia em uso)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso criado para proteger dados confidenciais armazenados em colunas de banco de dados específicas contra acesso (por exemplo, números de cartão de crédito, números de identificação nacional ou dados de _conhecimento obrigatório_). Isso inclui administradores de banco de dados ou outros usuários com privilégios que têm autorização para acessar o banco de dados para executar tarefas de gerenciamento, mas não têm a necessidade comercial de acessar os dados específicos nas colunas criptografadas. Os dados sempre são criptografados, o que significa que eles são descriptografados apenas para o processamento realizado por aplicativos cliente com acesso à chave de criptografia.  A chave de criptografia nunca é exposta ao SQL e pode ser armazenada no [Repositório de Certificados do Windows](sql-database-always-encrypted.md) ou no [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

A máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos detecta de forma automática os dados potencialmente confidenciais no Banco de Dados SQL do Azure e fornece recomendações viáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ocultar os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados. Para obter mais informações, consulte [Iniciar com a mascaração dinâmica de dados dinâmicos do Banco de Dados SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Gerenciamento de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidade

A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir vulnerabilidades potenciais do banco de dados com o objetivo de melhorar de maneira proativa a segurança geral do banco de dados. A avaliação de vulnerabilidade (VA) faz parte da oferta de segurança de dados avançada (ADS), um pacote unificado para funcionalidades avançadas de segurança do SQL. A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal central da ADS do SQL.

### <a name="data-discovery--classification"></a>Descoberta e classificação de dados

A descoberta e classificação de dados (atualmente em versão prévia) fornece recursos internos avançados no Banco de Dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Descobrir e classificar seus dados confidenciais mais importantes (negócios/financeiros, serviços de saúde, dados pessoais, etc.) pode desempenhar uma função essencial na estatura de proteção das informações organizacionais. Esse recurso pode funcionar como a infraestrutura para:

- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlando o acesso e fortalecendo a segurança de bancos de dados que contêm dados altamente confidenciais.
- Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.

Para obter mais informações, confira [Introdução à descoberta e classificação de dados](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformidade

Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

## <a name="next-steps"></a>Próximas etapas

- Para uma discussão sobre o uso de logins, contas de usuário, funções de banco de dados e permissões no Banco de Dados SQL, consulte [Gerenciar logins e contas de usuário](sql-database-manage-logins.md).
- Para uma discussão sobre auditoria de banco de dados, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).
- Para uma discussão sobre detecção de ameaças, consulte [Detecção de ameaças do Banco de Dados SQL](sql-database-threat-detection.md).
