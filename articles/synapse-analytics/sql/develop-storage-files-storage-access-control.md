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
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420050"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Controlar o acesso à conta de armazenamento para SQL sob demanda (versão prévia) no Azure Synapse Analytics

Uma consulta SQL sob demanda (versão prévia) lê arquivos diretamente no Armazenamento do Azure. Como a conta de armazenamento é um objeto externo ao recurso SQL sob demanda, as credenciais apropriadas são necessárias. Um usuário precisa ter as permissões aplicáveis para usar a credencial necessária. Este artigo descreve os tipos de credenciais que você pode usar e como a pesquisa de credenciais é aplicada para usuários do SQL e do Azure AD.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento compatíveis

Um usuário que fez logon em um recurso SQL sob demanda deve estar autorizado a acessar e consultar os arquivos no Armazenamento do Azure. Três tipos de autorização são compatíveis:

- [Assinatura de acesso compartilhado](#shared-access-signature)
- [Identidade gerenciada](#managed-identity)
- [Identidade do Usuário](#user-identity)

> [!NOTE]
> [Passagem do Azure AD](#force-azure-ad-pass-through) é o comportamento padrão quando você cria um workspace. Se você usá-la, não precisará criar credenciais para cada conta de armazenamento acessada usando os logons do AD. É possível [desabilitar esse comportamento](#disable-forcing-azure-ad-pass-through).

Na tabela abaixo, você encontrará os diferentes tipos de autorização que são compatíveis ou que serão compatíveis em breve.

| Tipo de autorização                    | *Usuário do SQL*    | *Usuário do Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Com suporte     | Com suporte      |
| [Identidade gerenciada](#managed-identity) | Sem suporte | Sem suporte  |
| [Identidade do Usuário](#user-identity)       | Sem suporte | Com suporte      |

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

A **SAS (Assinatura de Acesso Compartilhado)** fornece acesso delegado aos recursos de uma conta de armazenamento. Com a SAS, um cliente pode conceder acesso aos recursos em uma conta de armazenamento, sem compartilhar as chaves de conta. A SAS oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm uma SAS, incluindo intervalo de validade, permissões concedidas, intervalo de endereços IP aceitáveis e o protocolo aceitável (HTTPS/HTTP).

Você pode obter um token SAS navegando até o **Portal do Azure -> Conta de Armazenamento -> Assinatura de Acesso Compartilhado -> Configurar permissões -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando um token SAS é gerado, ele inclui um ponto de interrogação ("?") no início do token. Para usar o token no SQL sob demanda, você deve remover o ponto de interrogação ("?") ao criar uma credencial. Por exemplo:
>
> Token SAS: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Identidade do Usuário

A **Identidade do Usuário**, também conhecida como "passagem", é um tipo de autorização em que a identidade do usuário do Azure AD que fez logon no SQL sob demanda é usada para autorizar o acesso a dados. Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões ao usuário do Azure AD. Conforme indicado na tabela acima, não é compatível para o tipo de usuário do SQL.

> [!NOTE]
> Se você usar a [passagem do Azure AD](#force-azure-ad-pass-through), não precisará criar credenciais para cada conta de armazenamento acessada usando logons do AD.

> [!IMPORTANT]
> Você precisa ter uma função de Proprietário/Colaborador/Leitor de dados do blob de armazenamento para usar sua identidade para acessar os dados.
> Mesmo se você for um Proprietário de uma Conta de Armazenamento, ainda precisará se adicionar a uma das funções de dados do blob de armazenamento.
>
> Para saber mais sobre o controle de acesso no Azure Data Lake Storage Gen2, examine o artigo [Controle de acesso no Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

### <a name="managed-identity"></a>Identidade Gerenciada

A **Identidade Gerenciada** também é conhecida como MSI. É um recurso do Azure AD (Azure Active Directory) que fornece serviços do Azure para SQL sob demanda. Além disso, ele implanta uma identidade gerenciada automaticamente no Azure AD. Essa identidade pode ser usada para autorizar a solicitação de acesso a dados no Armazenamento do Azure.

Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões para a Identidade Gerenciada a fim de acessar os dados. A concessão de permissões para a Identidade Gerenciada é feita da mesma forma que a concessão de permissão para qualquer outro usuário do Azure AD.

## <a name="create-credentials"></a>Criar credenciais

Para consultar um arquivo localizado no Armazenamento do Azure, seu ponto de extremidade do SQL sob demanda precisa de uma CREDENCIAL de nível de servidor que contenha as informações de autenticação. Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Você precisará fornecer um argumento CREDENTIAL NAME. Ele deve corresponder a parte ou a todo o caminho para os dados no Armazenamento (veja abaixo).

> [!NOTE]
> Não há compatibilidade para o argumento FOR CRYPTOGRAPHIC PROVIDER.

Para todos os tipos de autorização compatíveis, as credenciais podem apontar para uma conta, um contêiner, qualquer diretório (não raiz) ou um arquivo.

O CREDENTIAL NAME deve corresponder ao caminho completo para o contêiner, a pasta ou o arquivo, no seguinte formato: `<prefix>://<storage_account_path>/<storage_path>`

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento do Blobs do Azure         | HTTPS  | <conta_de_armazenamento>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <conta_de_armazenamento>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <conta_de_armazenamento>.dfs.core.windows.net              |

 "<caminho_de_armazenamento>" é um caminho do armazenamento que aponta para a pasta ou o arquivo que você deseja ler.

> [!NOTE]
> Há um CREDENTIAL NAME especial `UserIdentity` que [força a passagem do Azure AD](#force-azure-ad-pass-through). Leia sobre o efeito que ele tem na [pesquisa de credenciais](#credential-lookup) durante a execução de consultas.

Opcionalmente, para permitir que um usuário crie ou remova uma credencial, o administrador pode GRANT/DENY ALTER ANY CREDENTIAL permissão a um usuário:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Tipos de autorização e armazenamentos compatíveis

Você pode usar as seguintes combinações de autorização e tipos de Armazenamento do Azure:

|                     | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Com suporte      | Não compatível   | Com suporte     |
| *Identidade Gerenciada* | Sem suporte  | Sem suporte    | Sem suporte |
| *Identidade do Usuário*    | Com suporte      | Com suporte        | Com suporte     |

### <a name="examples"></a>Exemplos

Dependendo do [tipo de autorização](#supported-storage-authorization-types), você pode criar credenciais usando a sintaxe T-SQL abaixo.

**Assinatura de Acesso Compartilhado e Armazenamento de Blobs**

Substitua <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identidade do Usuário e Azure Data Lake Storage Gen1**

Substitua <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identidade do Usuário e Azure Data Lake Storage Gen2**

Substitua <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Forçar passagem do Azure AD

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

Para obter mais informações sobre como o SQL sob demanda localiza a credencial a ser usada, confira [pesquisa de credenciais](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Desabilitar forçar passagem do Azure AD

Você pode desabilitar [forçar a passagem do Azure AD para cada consulta](#force-azure-ad-pass-through). Para desabilitá-la, descarte a credencial `Userdentity` usando:

```sql
DROP CREDENTIAL [UserIdentity];
```

Se você quiser reabilitá-la, veja a seção [Forçar passagem do Azure AD](#force-azure-ad-pass-through).

Para desabilitar forçar passagem do Azure AD para um usuário específico, você pode negar a permissão REFERENCE na credencial `UserIdentity` para esse usuário específico. O seguinte exemplo desabilita forçar uma passagem do Azure AD para um user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Para obter mais informações sobre como o SQL sob demanda localiza credenciais a serem usadas, confira [pesquisa de credenciais](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credencial

Para usar a credencial, um usuário deve ter a permissão REFERENCES em uma credencial específica. Para conceder uma permissão REFERENCES em um storage_credential para um specific_user, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Para garantir uma experiência de passagem do Azure AD suave, todos os usuários, por padrão, terão um direito de usar a credencial `UserIdentity`. Isso é obtido por uma execução automática da seguinte instrução no provisionamento do workspace do Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Pesquisa de credenciais

Ao autorizar consultas, a pesquisa de credenciais é usada para acessar uma conta de armazenamento e baseia-se nas seguintes regras:

- O usuário está conectado como um logon do Azure AD

  - Se existir uma credencial UserIdentity e o usuário tiver permissões de referência nela, a passagem do Azure AD será usada, caso contrário use a [pesquisa de credencial por caminho](#lookup-credential-by-path)

- O usuário está conectado em um logon do SQL

  - Usar [pesquisa de credencial por caminho](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Pesquisar credencial por caminho

Se forçar passagem do Azure AD estiver desabilitada, a pesquisa de credenciais será baseada no caminho de armazenamento (profundidade primeiro) e na existência de uma permissão REFERENCES nessa credencial específica. Quando há várias credenciais que podem ser usadas para acessar o mesmo arquivo, o SQL sob demanda usará a mais específica.  

Abaixo está um exemplo de uma consulta no seguinte caminho de arquivo: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

A pesquisa de credenciais será concluída nesta ordem:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Se um usuário não tiver permissão REFERENCES na credencial número 5, o SQL sob demanda verificará se o usuário tem a permissão REFERENCES em uma credencial que seja um nível acima até localizar as credenciais em que o usuário tenha a permissão REFERENCES. Se essa permissão não for encontrada, uma mensagem de erro será retornada.

### <a name="credential-and-path-level"></a>Nível de caminho e credencial

Dependendo da forma de caminho que você deseja, os seguintes requisitos estarão em vigor para executar consultas:

- Se a consulta estiver destinada a vários arquivos (pastas, com ou sem curingas), um usuário precisará ter acesso a uma credencial, pelo menos, do nível de diretório raiz (nível de contêiner). Esse nível de acesso é necessário, pois a listagem de arquivos é relativa ao diretório raiz (limitações do Armazenamento do Azure)
- Se a consulta for direcionada a um arquivo, um usuário precisará ter acesso a uma credencial em qualquer nível, já que o SQL sob demanda acessa o arquivo diretamente, ou seja, sem listar pastas.

|                  | *Conta* | *Diretório raiz* | *Qualquer outro diretório* | *Arquivo*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Arquivo único*    | Com suporte | Com suporte        | Com suporte             | Com suporte     |
| *Vários arquivos* | Com suporte | Com suporte        | Sem suporte         | Sem suporte |

## <a name="next-steps"></a>Próximas etapas

Os artigos listados abaixo ajudarão você a aprender a consultar diferentes tipos de pasta, tipos de arquivo e criar e usar modos de exibição:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)
- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)
- [Consultar arquivos específicos](query-specific-files.md)
- [Consultar arquivos Parquet](query-parquet-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
- [Consultar arquivos JSON](query-json-files.md)
- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)
