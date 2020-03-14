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
ms.openlocfilehash: 348b8fc44628437cbbcfbcd39a26d048284aa60e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208848"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma visão geral dos recursos de segurança do Banco de Dados SQL do Azure

Este artigo apresenta os conceitos básicos da proteção da camada de dados de um aplicativo que usa o Banco de Dados SQL do Azure. A estratégia de segurança descrita segue a abordagem de defesa em profundidade em camadas conforme mostrado na imagem abaixo e se movimenta de fora para dentro:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança de rede

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para aplicativos na nuvem e empresariais. Para ajudar a proteger os dados do cliente, os firewalls impedem o acesso de rede ao servidor de banco de dados até que ele seja concedido explicitamente com base no endereço IP ou na origem do tráfego de rede virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall de IP permitem acesso a bancos de dados com base no endereço IP de origem de cada solicitação. Para saber mais, confira [Overview of Azure SQL Database and SQL Data Warehouse firewall rules](sql-database-firewall-configure.md) (Visão geral de regras de firewall do Banco de Dados SQL do Azure e do SQL Data Warehouse).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Os [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) estendem a conectividade de rede virtual por meio do backbone do Azure e permitem que o Banco de Dados SQL do Azure identifique a sub-rede de rede virtual da qual o tráfego é originado. Para permitir que o tráfego alcance o Banco de Dados SQL do Azure, use as [marcas de serviço](../virtual-network/security-overview.md) do SQL para permitir o tráfego de saída por meio de Grupos de Segurança de Rede.

As [regras da rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) permitem que o Banco de Dados SQL do Azure aceite apenas comunicações enviadas de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> O controle do acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Para saber mais sobre a configuração de rede necessária, confira [conectar-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gerenciamento de acesso

> [!IMPORTANT]
> O gerenciamento de bancos de dados e de servidores de banco de dados no Azure é controlado por atribuições de função da sua conta de usuário do portal. Para saber mais sobre esse artigo, confira [Controle de acesso baseado em função no Portal do Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem diz ser. O Banco de Dados SQL do Azure dá suporte a dois tipos de autenticação:

- **Autenticação do SQL**:

    A autenticação do banco de dados SQL refere-se à autenticação de um usuário ao se conectar ao [Banco de Dados SQL do Azure](sql-database-technical-overview.md) usando nome de usuário e senha. Durante a criação do servidor de banco de dados do banco de dados, o logon “Administrador do servidor” com um nome de usuário e senha deve ser especificado. Usando essas credenciais, um “administrador do servidor” pode autenticar-se em qualquer banco de dados nesse servidor de banco de dados como o proprietário do banco de dados. Depois disso, os usuários e logons do SQL adicionais podem ser criados pelo administrador do servidor, que permite que os usuários se conectem usando nome de usuário e senha.

- **Autenticação do Azure Active Directory**:

    A autenticação do Azure Active Directory é um mecanismo de conexão com o [Banco de Dados SQL](sql-database-technical-overview.md) do Azure e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure AD (Azure Active Directory). A autenticação do Azure AD permite que os administradores gerenciem centralmente as identidades e permissões de usuários de banco de dados juntamente com outros serviços da Microsoft em uma única localização central. Isso inclui a minimização do armazenamento de senha e habilita políticas centralizadas de rotação de senha.

     Um administrador do servidor chamado o **administrador do Active Directory** deve ser criado para usar autenticação do Azure AD com o Banco de Dados SQL. Para saber mais, confira [Connecting to SQL Database By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Conectando-se ao Banco de Dados SQL usando a Autenticação do Azure Active Directory). A autenticação do Azure AD dá suporte a contas gerenciadas e federadas. As contas federadas dão suporte a grupos e usuários do Windows para um domínio de cliente federado com o Azure AD.

    Mais opções de autenticação do Azure AD disponíveis são conexões de [Autenticação Universal do Active Directory para SQL Server Management Studio](sql-database-ssms-mfa-authentication.md), incluindo [Autenticação Multifator](../active-directory/authentication/concept-mfa-howitworks.md) e [Acesso Condicional](sql-database-conditional-access.md).

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores no Azure é controlado pelas atribuições de função da sua conta de usuário do portal. Para saber mais sobre esse artigo, confira [Controle de acesso baseado em função no Portal do Azure](../role-based-access-control/overview.md). O controle do acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Para obter mais informações sobre a configuração de rede necessária, confira o seguinte artigo sobre [conectar-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md).

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um usuário em um Banco de Dados SQL do Azure e determina o que o usuário tem permissão para fazer. As permissões são controladas adicionando contas de usuário a [funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles) e atribuindo permissões de nível de banco de dados a essas funções ou concedendo ao usuário determinadas [permissões de nível de objeto](/sql/relational-databases/security/permissions-database-engine). Para saber mais, confira [Logins and users](sql-database-manage-logins.md) (Logons e usuários)

Como prática recomendada, crie funções personalizadas quando necessário. Adicione usuários à função com os privilégios mínimos necessários para realizar sua função de trabalho. Não atribua permissões diretamente aos usuários. A conta do administrador do servidor é um membro da função de db_owner interna, que tem permissões extensivas e só deve ser concedida a poucos usuários com tarefas administrativas. Para aplicativos de banco de dados SQL do Azure, use [Executar como](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo chamado ou usar [funções de aplicativo](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Essa prática garante que o aplicativo que se conecta ao banco de dados tenha os privilégios mínimos necessários para o aplicativo. Seguir essas práticas recomendadas também promove a separação de tarefas.

### <a name="row-level-security"></a>Segurança em nível de linha

A segurança em nível de linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário executando uma consulta (por exemplo, associação a grupo ou contexto de execução). A segurança em nível de linha também pode ser usada para implementar conceitos de segurança personalizados baseados em rótulo. Para saber mais, confira [Segurança em nível de linha](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

O Banco de Dados SQL protege os dados do cliente fornecendo funcionalidades de auditoria e de detecção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria do SQL em logs de Azure Monitor e hubs de eventos

A Auditoria do Banco de Dados SQL rastreia as atividades do banco de dados e ajuda a manter a conformidade com os padrões de segurança registrando eventos de banco de dados em um log de auditoria em uma conta de Armazenamento do Azure de propriedade do cliente. A auditoria permite que os usuários monitorem as atividades do banco de dados em andamento, além de analisar e investigar a atividade de histórico para identificar possíveis ameaças ou suspeitas de violações de segurança e de abuso. Para saber mais, confira Introdução à [Auditoria do Banco de Dados SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada contra Ameaças

A proteção avançada contra ameaças está analisando seus logs de SQL Server para detectar comportamento incomum e tentativas potencialmente prejudiciais de acessar ou explorar bancos de dados. Os alertas são criados para atividades suspeitas, como injeção de SQL, potencial pós-infiltração de dados e ataques de força bruta ou para anomalias em padrões de acesso para capturar as progressão de privilégio e o uso de credenciais violadas. Os alertas são exibidos na [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), onde os detalhes das atividades suspeitas são fornecidos e recomendações para uma investigação mais detalhada fornecida junto com as ações para atenuar a ameaça. A proteção avançada contra ameaças pode ser habilitada por servidor por uma taxa adicional. Para obter mais informações, consulte Introdução [à proteção avançada contra ameaças do banco de dados SQL](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Protocolo TLS (Criptografia em trânsito)

O Banco de Dados SQL protege os dados do cliente por meio da criptografia de dados em movimento com o [protocolo TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

O SQL Server impõe a criptografia (SSL/TLS) em todos os momentos para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de **Encrypt** ou **TrustServerCertificate** na cadeia de conexão.

Como prática recomendada, recomende que, na cadeia de conexão do aplicativo, você especifique uma conexão criptografada e _**não**_ confie no certificado do servidor. Isso força seu aplicativo a verificar o certificado do servidor e, portanto, impede que o aplicativo fique vulnerável a ataques de tipo intermediário.

Por exemplo, ao usar o driver ADO.NET, isso é realizado por meio de **encrypt = true** e **TrustServerCertificate = false**. Se você obtiver sua cadeia de conexão do portal do Azure, ela terá as configurações corretas.

> [!IMPORTANT]
> Observe que alguns drivers que não são da Microsoft não podem usar o TLS por padrão ou contar com uma versão mais antiga do TLS (< 1.2) para funcionar. Nesse caso, SQL Server ainda permite que você se conecte ao seu banco de dados. No entanto, é recomendável que você avalie os riscos de segurança de permitir que esses drivers e aplicativos se conectem ao banco de dados SQL, especialmente se você armazená-los.
>
> Para obter mais informações sobre TLS e conectividade, consulte [Considerações sobre TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Criptografia em repouso)

A [TDE (Transparent Data Encryption) para Banco de Dados SQL do Azure](transparent-data-encryption-azure-sql.md) adiciona uma camada de segurança para ajudar a proteger os dados em repouso contra acesso não autorizado ou offline para backups ou arquivos brutos. Os cenários comuns incluem o roubo de datacenter ou a eliminação não segura de hardware ou de mídia como discos rígidos e fitas de backup. O TDE criptografa todo o banco de dados usando um algoritmo de criptografia AES, que não exige que os desenvolvedores de aplicativos façam alterações nos aplicativos existentes.

No Azure, todos os bancos de dados SQL recém-criados são criptografados por padrão e a chave de criptografia de banco de dados é protegida por um certificado de servidor interno.  A rotação e a manutenção de certificados são gerenciadas pelo serviço e não requerem a intervenção do usuário. Os clientes que preferirem assumir controle das chaves de criptografia podem gerenciá-las no [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gerenciamento de chaves com o Azure Key Vault

O suporte BYOK [(Bring Your Own Key)](transparent-data-encryption-byok-azure-sql.md) para TDE [(Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) permite que os clientes assumam a propriedade da rotação e gerenciamento de chaves usando o [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), sistema de gerenciamento de chaves externas baseado em nuvem do Azure. Se o acesso do banco de dados ao cofre de chaves for revogado, um banco de dados não poderá ser descriptografado nem lido na memória. O Azure Key Vault fornece uma plataforma de gerenciamento central de chaves, utiliza HSMs (Módulos de Segurança de Hardware) rigidamente monitorados e permite a separação de funções entre o gerenciamento de chaves e dados para ajudar nos requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Criptografia em uso)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso criado para proteger dados confidenciais armazenados em colunas de banco de dados específicas contra acesso (por exemplo, números de cartão de crédito, números de identificação nacional ou dados de _conhecimento obrigatório_). Isso inclui administradores de banco de dados ou outros usuários com privilégios que têm autorização para acessar o banco de dados para executar tarefas de gerenciamento, mas não têm a necessidade comercial de acessar os dados específicos nas colunas criptografadas. Os dados sempre são criptografados, o que significa que eles são descriptografados apenas para o processamento realizado por aplicativos cliente com acesso à chave de criptografia.  A chave de criptografia nunca é exposta ao SQL e pode ser armazenada no [Repositório de Certificados do Windows](sql-database-always-encrypted.md) ou no [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

A máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos detecta de forma automática os dados potencialmente confidenciais no Banco de Dados SQL do Azure e fornece recomendações viáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ocultar os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados. Para obter mais informações, consulte [Introdução à máscara de dados dinâmicos do Banco de Dados SQL](sql-database-dynamic-data-masking-get-started.md).

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

Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma discussão sobre o uso de logons, contas de usuário, funções de banco de dados e permissões no banco de dados SQL, consulte [gerenciar logons e contas de usuário](sql-database-manage-logins.md).
- Para uma discussão sobre auditoria de banco de dados, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).
- Para uma discussão sobre detecção de ameaças, consulte [Detecção de ameaças do Banco de Dados SQL](sql-database-threat-detection.md).
