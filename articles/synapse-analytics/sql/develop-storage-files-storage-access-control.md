---
title: Controlar o acesso à conta de armazenamento para SQL sob demanda (versão prévia)
description: Descreve como o SQL sob demanda (versão prévia) acessa o Armazenamento do Azure e como você pode controlar o acesso ao armazenamento para SQL sob demanda no Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2d5d508afe81975cbeda448b497a098e8a3bbcf3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589271"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Controlar o acesso à conta de armazenamento para SQL sob demanda (versão prévia)

Uma consulta SQL sob demanda lê arquivos diretamente no Armazenamento do Azure. As permissões para acessar os arquivos no armazenamento do Azure são controladas em dois níveis:
- **Nível de armazenamento** – o usuário deve ter permissão para acessar os arquivos de armazenamento subjacentes. O administrador de armazenamento deve permitir que a entidade de segurança do Azure AD leia/grave arquivos ou gere uma chave SAS que será usada para acessar o armazenamento.
- **Nível de serviço do SQL** – o usuário deve ter a permissão `SELECT` para ler dados de [tabela externa](develop-tables-external-tables.md) ou a permissão `ADMINISTER BULK ADMIN` para executar `OPENROWSET`, bem como permissão para usar as credenciais necessárias para acessar o armazenamento.

Este artigo descreve os tipos de credenciais que você pode usar e como a pesquisa de credenciais é aplicada para usuários do SQL e do Azure AD.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento compatíveis

Um usuário que fez logon em um recurso SQL sob demanda deverá estar autorizado a acessar e consultar os arquivos no Armazenamento do Azure se os arquivos não estiverem disponíveis publicamente. Três tipos de autorização são compatíveis:

- [Assinatura de acesso compartilhado](?tabs=shared-access-signature)
- [Identidade do Usuário](?tabs=user-identity)
- [Identidade gerenciada](?tabs=managed-identity)

> [!NOTE]
> [Passagem do Azure AD](#force-azure-ad-pass-through) é o comportamento padrão quando você cria um workspace. Se você usá-la, não precisará criar credenciais para cada conta de armazenamento acessada usando os logons do Azure AD. É possível [desabilitar esse comportamento](#disable-forcing-azure-ad-pass-through).

### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

A **SAS (Assinatura de Acesso Compartilhado)** fornece acesso delegado aos recursos de uma conta de armazenamento. Com a SAS, um cliente pode conceder acesso aos recursos em uma conta de armazenamento, sem compartilhar as chaves de conta. A SAS oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm uma SAS, incluindo intervalo de validade, permissões concedidas, intervalo de endereços IP aceitáveis e o protocolo aceitável (HTTPS/HTTP).

Você pode obter um token SAS navegando até o **Portal do Azure -> Conta de Armazenamento -> Assinatura de Acesso Compartilhado -> Configurar permissões -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando um token SAS é gerado, ele inclui um ponto de interrogação ("?") no início do token. Para usar o token no SQL sob demanda, você deve remover o ponto de interrogação ("?") ao criar uma credencial. Por exemplo:
>
> Token SAS: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Você precisa criar uma credencial no escopo do banco de dados ou no escopo do servidor para permitir o acesso usando o token SAS.

### <a name="user-identity"></a>[Identidade do Usuário](#tab/user-identity)

A **Identidade do Usuário**, também conhecida como "passagem", é um tipo de autorização em que a identidade do usuário do Azure AD que fez logon no SQL sob demanda é usada para autorizar o acesso a dados. Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões ao usuário do Azure AD. Conforme indicado na tabela acima, não é compatível para o tipo de usuário do SQL.

> [!IMPORTANT]
> Você precisa ter uma função de Proprietário/Colaborador/Leitor de dados do blob de armazenamento para usar sua identidade para acessar os dados.
> Mesmo se você for um Proprietário de uma Conta de Armazenamento, ainda precisará se adicionar a uma das funções de dados do blob de armazenamento.
>
> Para saber mais sobre o controle de acesso no Azure Data Lake Storage Gen2, examine o artigo [Controle de acesso no Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

Você precisa habilitar explicitamente a autenticação de passagem do Azure AD para permitir que os usuários do Azure AD acessem o armazenamento usando as identidades deles.

#### <a name="force-azure-ad-pass-through"></a>Forçar passagem do Azure AD

Forçar uma passagem do Azure AD é um comportamento padrão obtido por um CREDENTIAL NAME especial, `UserIdentity`, que é criado automaticamente durante o provisionamento do workspace do Azure Synapse. Ele força o uso de uma passagem do Azure AD para cada consulta de cada logon do Azure AD, o que ocorrerá apesar da existência de outras credenciais.

> [!NOTE]
> A passagem do Azure AD é um comportamento padrão. Você não precisa criar credenciais para cada conta de armazenamento acessada pelos logons do AD.

Caso você tenha [desabilitado forçar a passagem do Azure AD para cada consulta](#disable-forcing-azure-ad-pass-through) e deseja habilitá-la novamente, execute:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Para habilitar forçar uma passagem do Azure AD para um usuário específico, você pode conceder permissão REFERENCE na credencial `UserIdentity` para esse usuário específico. O seguinte exemplo permite forçar uma passagem do Azure AD para um user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Desabilitar forçar passagem do Azure AD

Você pode desabilitar [forçar a passagem do Azure AD para cada consulta](#force-azure-ad-pass-through). Para desabilitá-la, descarte a credencial `Userdentity` usando:

```sql
DROP CREDENTIAL [UserIdentity];
```

Se você quiser reabilitá-la, veja a seção [Forçar passagem do Azure AD](#force-azure-ad-pass-through).

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

A **Identidade Gerenciada** também é conhecida como MSI. É um recurso do Azure AD (Azure Active Directory) que fornece serviços do Azure para SQL sob demanda. Além disso, ele implanta uma identidade gerenciada automaticamente no Azure AD. Essa identidade pode ser usada para autorizar a solicitação de acesso a dados no Armazenamento do Azure.

Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões para a Identidade Gerenciada a fim de acessar os dados. A concessão de permissões para a Identidade Gerenciada é feita da mesma forma que a concessão de permissão para qualquer outro usuário do Azure AD.

### <a name="anonymous-access"></a>[Acesso anônimo](#tab/public-access)

Você pode acessar os arquivos publicamente disponíveis colocados em contas do Armazenamento do Azure que [permitem acesso anônimo](/azure/storage/blobs/storage-manage-access-to-resources.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Tipos de autorização compatíveis com usuários de bancos de dados

Na tabela abaixo, você pode encontrar os tipos de autorização disponíveis:

| Tipo de autorização                    | *Usuário do SQL*    | *Usuário do Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identidade do Usuário](?tabs=user-identity#supported-storage-authorization-types)       | Sem suporte | Com suporte      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Com suporte     | Com suporte      |
| [Identidade gerenciada](?tabs=managed-identity#supported-storage-authorization-types) | Sem suporte | Com suporte      |

### <a name="supported-storages-and-authorization-types"></a>Tipos de autorização e armazenamentos compatíveis

Você pode usar as seguintes combinações de autorização e tipos de Armazenamento do Azure:

|                     | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Com suporte      | Não compatível   | Com suporte     |
| *Identidade Gerenciada* | Com suporte      | Com suporte        | Com suporte     |
| *Identidade do Usuário*    | Com suporte      | Com suporte        | Com suporte     |

## <a name="credentials"></a>Credenciais

Para consultar um arquivo localizado no Armazenamento do Azure, seu ponto de extremidade do SQL sob demanda precisa de uma credencial que contenha as informações de autenticação. São usados dois tipos de credenciais:
- O argumento CREDENTIAL no nível do servidor é usado para consultas ad hoc executadas usando a função `OPENROWSET`. O nome da credencial deve corresponder à URL de armazenamento.
- DATABASE SCOPED CREDENTIAL é usada para tabelas externas. A tabela externa faz referência a `DATA SOURCE` com a credencial que deve ser usada para acessar o armazenamento.

Para permitir que um usuário crie ou remova uma credencial, o administrador pode conceder a permissão GRANT/DENY ALTER ANY CREDENTIAL a um usuário:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Os usuários de banco de dados que acessam o armazenamento externo devem ter permissão para usar credenciais.

### <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credencial

Para usar a credencial, um usuário deve ter a permissão `REFERENCES` em uma credencial específica. Para conceder uma permissão `REFERENCES` ON uma credencial_de_armazenamento para um usuário_específico, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Para garantir uma experiência de passagem do Azure AD suave, todos os usuários, por padrão, terão um direito de usar a credencial `UserIdentity`. Isso é obtido por uma execução automática da seguinte instrução no provisionamento do workspace do Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="server-scoped-credential"></a>Credencial no escopo do servidor

As credenciais no escopo do servidor são usadas quando o logon do SQL chama a função `OPENROWSET` sem `DATA_SOURCE` para ler arquivos em alguma conta de armazenamento. O nome da credencial no escopo do servidor **precisa** corresponder à URL do Armazenamento do Azure. Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Você precisará fornecer um argumento CREDENTIAL NAME. Ele deve corresponder a parte ou a todo o caminho para os dados no Armazenamento (veja abaixo).

> [!NOTE]
> Não há compatibilidade para o argumento FOR CRYPTOGRAPHIC PROVIDER.

O nome do argumento CREDENTIAL no nível do servidor deve corresponder ao caminho completo da conta de armazenamento (e, opcionalmente, do contêiner), no seguinte formato: `<prefix>://<storage_account_path>/<storage_path>`. Os caminhos de contas de armazenamento são descritos na tabela a seguir:

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento do Blobs do Azure         | HTTPS  | <conta_de_armazenamento>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <conta_de_armazenamento>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <conta_de_armazenamento>.dfs.core.windows.net              |

> [!NOTE]
> Há um CREDENTIAL `UserIdentity` especial no nível do servidor que [força a passagem do Azure AD](?tabs=user-identity#force-azure-ad-pass-through).

As credenciais no escopo do servidor permitem o acesso ao Armazenamento do Azure usando os seguintes tipos de autenticação:

### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

O script a seguir cria uma credencial no nível do servidor que pode ser usada pela função `OPENROWSET` para acessar qualquer arquivo no Armazenamento do Azure usando o token SAS. Crie essa credencial para habilitar a entidade de segurança SQL que executa a função `OPENROWSET` a fim de ler arquivos protegidos com a chave SAS no Armazenamento do Azure que corresponde à URL no nome da credencial.

Substitua <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="user-identity"></a>[Identidade do Usuário](#tab/user-identity)

O script a seguir cria uma credencial no nível do servidor que permite ao usuário representar alguém usando a identidade do Azure AD.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

O script a seguir cria uma credencial no nível do servidor que pode ser usada pela função `OPENROWSET` para acessar qualquer arquivo no Armazenamento do Azure usando a identidade gerenciada do workspace.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Acesso público](#tab/public-access)

O script a seguir cria uma credencial no nível do servidor que pode ser usada pela função `OPENROWSET` para acessar qualquer arquivo no Armazenamento do Azure disponível publicamente. Crie essa credencial para habilitar a entidade de segurança SQL que executa a função `OPENROWSET` a fim de ler arquivos disponíveis publicamente no Armazenamento do Azure que corresponde à URL no nome da credencial.

Você precisaria substituir <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```
---

## <a name="database-scoped-credential"></a>Credencial no escopo do banco de dados

As credenciais no escopo do banco de dados são usadas quando qualquer entidade de segurança chama a função `OPENROWSET` com `DATA_SOURCE` ou seleciona dados de [tabela externa](develop-tables-external-tables.md) que não acessam arquivos públicos. A credencial no escopo do banco de dados não precisa corresponder ao nome da conta de armazenamento, pois ela será usada explicitamente na fonte de dados (argumento DATA SOURCE) que define o local do armazenamento.

As credenciais no escopo do banco de dados permitem o acesso ao Armazenamento do Azure usando os seguintes tipos de autenticação:

### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

O script a seguir cria uma credencial que é usada para acessar arquivos no armazenamento usando o token SAS especificado na credencial.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="azure-ad-identity"></a>[Identidade do Azure AD](#tab/user-identity)

O script a seguir cria uma credencial no escopo do banco de dados que é usada pela [tabela externa](develop-tables-external-tables.md) e funções `OPENROWSET` que usam a fonte de dados com a credencial para acessar arquivos de armazenamento usando a identidade do Azure AD de cada função.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

O script a seguir cria uma credencial no escopo do banco de dados que pode ser usada para representar o usuário atual do Azure AD como identidade gerenciada do serviço. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

A credencial no escopo do banco de dados não precisa corresponder ao nome da conta de armazenamento, pois ela será usada explicitamente na fonte de dados (argumento DATA SOURCE) que define o local do armazenamento.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial no escopo do banco de dados não é necessária para permitir o acesso a arquivos disponíveis publicamente. Crie uma [fonte de dados sem credencial no escopo do banco de dados](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) para acessar arquivos publicamente disponíveis no Armazenamento do Azure.

---

As credenciais no escopo do banco de dados são usadas em fontes externas para especificar qual método de autenticação será usado para acessar esse armazenamento:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemplos

**Como acessar uma fonte de dados disponível publicamente**

Use o script a seguir para criar uma tabela que acessa uma fonte de dados disponível publicamente.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://****.blob.core.windows.net/public-access' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [publicData], FILE_FORMAT = [SynapseParquetFormat] )
```

O usuário do banco de dados pode ler o conteúdo dos arquivos da fonte de dados usando a tabela externa ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

**Como acessar uma fonte de dados usando uma credencial**

Modifique o script a seguir para criar uma tabela externa que acessa o Armazenamento do Azure usando o token SAS, a identidade do usuário do Azure AD ou a identidade gerenciada do workspace.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use User Identity, Managed Identity, or SAS. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL MyIdentity WITH IDENTITY = 'User Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = MyIdentity 
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FILE_FORMAT = [SynapseParquetFormat] )

```

O usuário do banco de dados pode ler o conteúdo dos arquivos da fonte de dados usando a [tabela externa](develop-tables-external-tables.md) ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

## <a name="next-steps"></a>Próximas etapas

Os artigos listados abaixo ajudarão você a aprender a consultar diferentes tipos de pasta, tipos de arquivo e criar e usar modos de exibição:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)
- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)
- [Consultar arquivos específicos](query-specific-files.md)
- [Consultar arquivos Parquet](query-parquet-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
- [Consultar arquivos JSON](query-json-files.md)
- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)
