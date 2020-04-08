---
title: Use o Azure Active Directory - Banco de dados Azure para PostgreSQL - Servidor Único
description: Saiba como configurar o AaD (AAD) Active Directory (AAD) para autenticação com o Banco de Dados Azure para PostgreSQL - Single Server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804700"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Use o Azure Active Directory para autenticar com postgreSQL

Este artigo irá mostrar as etapas de como configurar o acesso ao Azure Active Directory com o Azure Database para PostgreSQL e como se conectar usando um token Azure AD.

> [!IMPORTANT]
> A autenticação Azure AD para Banco de Dados Azure para PostgreSQL está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Configuração do usuário admin Azure AD

Apenas os usuários do administrador do Azure AD podem criar/habilitar usuários para autenticação baseada no Azure AD. Recomendamos não usar o administrador do Azure AD para operações regulares de banco de dados, pois possui permissões de usuário elevadas (por exemplo, CREATEDB).

Para definir o administrador azure AD (você pode usar um usuário ou um grupo), siga as seguintes etapas

1. No portal Azure, selecione a instância do Banco de Dados Azure para PostgreSQL que você deseja habilitar para o Azure AD.
2. Em Configurações, selecione O Admin de diretório ativo:

![definir administrador de anúncios azure][2]

3. Selecione um usuário Ad azure válido no inquilino do cliente para ser administrador aazure AD.

> [!IMPORTANT]
> Ao configurar o administrador, um novo usuário é adicionado ao banco de dados Do Zure para servidor PostgreSQL com permissões completas de administrador. O usuário do Azure AD Ad Ad min no Banco `azure_ad_admin`de Dados Azure para PostgreSQL terá a função .

Apenas um administrador Azure AD pode ser criado por servidor PostgreSQL e a seleção de outro substituirá o administrador Azure AD existente configurado para o servidor. Você pode especificar um grupo Azure AD em vez de um usuário individual para ter vários administradores. Observe que você entrará com o nome do grupo para fins de administração.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Conectando-se ao banco de dados do Azure para PostgreSQL usando o Azure AD

O diagrama de alto nível a seguir resume o fluxo de trabalho do uso da autenticação Azure AD com o Banco de Dados Azure para PostgreSQL:

![fluxo de autenticação][1]

Projetamos a integração azure AD para trabalhar com ferramentas postgresql comuns como psql, que não são cientes do Azure AD e apenas suportam especificar nome de usuário e senha ao se conectar ao PostgreSQL. Passamos o token Azure AD como a senha como mostrado na imagem acima.

Atualmente, testamos os seguintes clientes:

- linha de comando psql (utilizar a variável PGPASSWORD para passar o token, veja abaixo)
- Azure Data Studio (usando a extensão PostgreSQL)
- Outros clientes baseados em libpq (por exemplo, frameworks de aplicativos comuns e ORMs)

> [!NOTE]
> Por favor, esteja ciente de que o uso do token Azure AD com pgAdmin não é suportado no momento, uma vez que tem uma limitação codificada de 256 caracteres para senhas (que o token excede).

Estas são as etapas que um usuário/aplicativo precisará fazer para autenticar com o Azure AD descrito abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Passo 1: Autenticar com Azure AD

Certifique-se de ter [o Azure CLI instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta Azure CLI para autenticar com o Azure AD. Ele exige que você forneça seu ID de usuário Azure AD e a senha.

```azurecli-interactive
az login
```

Este comando iniciará uma janela do navegador para a página de autenticação do Azure AD.

> [!NOTE]
> Você também pode usar o Azure Cloud Shell para executar essas etapas.
> Por favor, esteja ciente de que ao recuperar o token de acesso a Azure AD no Azure Cloud Shell, você precisará ligar `az login` e fazer login explicitamente (na janela separada com um código). Depois desse sinal `get-access-token` no comando funcionará como esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passo 2: Recuperar o token de acesso ao Azure AD

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o usuário autenticado Azure AD a partir da etapa 1 para acessar o Banco de Dados Do Azure para PostgreSQL.

Exemplo (para Nuvem Pública):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor de recurso acima deve ser especificado exatamente como mostrado. Para outras nuvens, o valor do recurso pode ser analisado usando:

```azurecli-interactive
az cloud show
```

Para a versão 2.0.71 do Azure CLI e posterior, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Depois que a autenticação for bem sucedida, o Azure AD retornará um token de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O token é uma seqüência base 64 que codifica todas as informações sobre o usuário autenticado, e que é direcionada ao Banco de Dados Do Azure para o serviço PostgreSQL.

> [!NOTE]
> A validade do token de acesso é entre 5 minutos e 60 minutos. Recomendamos que você obtenha o token de acesso pouco antes de começar o login no Banco de Dados Do Azure para PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passo 3: Use o token como senha para fazer login com o PostgreSQL

Ao conectar, você precisa usar o token de acesso como a senha do usuário PostgreSQL.

Ao usar `psql` o cliente da linha de comando, `PGPASSWORD` o token de acesso precisa ser passado `psql` através da variável ambiente, uma vez que o token de acesso excede o comprimento de senha que pode aceitar diretamente:

Exemplo do Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Exemplo do Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Agora você pode iniciar uma conexão com o Banco de Dados Azure para PostgreSQL como você normalmente faria:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Agora você está autenticado no servidor PostgreSQL usando a autenticação Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Criando usuários azure AD no banco de dados Azure para PostgreSQL

Para adicionar um usuário Azure AD ao banco de dados Do Azure para banco de dados PostgreSQL, execute as seguintes etapas após a conexão (veja a seção posterior sobre como se conectar):

1. Primeiro certifique-se de que `<user>@yourtenant.onmicrosoft.com` o usuário Azure AD é um usuário válido no inquilino Azure AD.
2. Faça login no seu banco de dados do Azure para a ocorrência do PostgreSQL como usuário do AdMin Do Azure AD.
3. Criar `<user>@yourtenant.onmicrosoft.com` função no Banco de Dados Azure para PostgreSQL.
4. Faça `<user>@yourtenant.onmicrosoft.com` um membro do papel azure_ad_user. Isso só deve ser dado aos usuários do Azure AD.

**Exemplo:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> A autenticação de um usuário através do Azure AD não dá ao usuário quaisquer permissões para acessar objetos dentro do banco de dados Azure para banco de dados PostgreSQL. Você deve conceder manualmente ao usuário as permissões necessárias.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Criando grupos Ad do Azure no banco de dados Azure para PostgreSQL

Para habilitar um grupo Azure AD para acesso ao seu banco de dados, use o mesmo mecanismo que para usuários, mas, em vez disso, especifique o nome do grupo:

**Exemplo:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Ao fazer login, os membros do grupo usarão seus tokens de acesso pessoal, mas assinarão com o nome do grupo especificado como nome de usuário.

## <a name="token-validation"></a>Validação de tokens

A autenticação do Azure AD no Banco de Dados Azure para PostgreSQL garante que o usuário exista no servidor PostgreSQL e verifica a validade do token validando o conteúdo do token. As seguintes etapas de validação de token são executadas:

- Token é assinado pelo Azure AD e não foi adulterado
- O token foi emitido pelo Azure AD para o inquilino associado ao servidor
- O token não expirou
- O Token é para o banco de dados Do Zure para recurso PostgreSQL (e não outro recurso do Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migração de usuários postgreSQL existentes para autenticação baseada no Azure AD

Você pode habilitar a autenticação do Azure AD para usuários existentes. Há dois casos a considerar:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: O nome de usuário postgreSQL corresponde ao nome principal do usuário azure AD

No caso improvável de que seus usuários existentes já correspondam aos nomes `azure_ad_user` de usuários do Azure AD, você pode conceder a função a eles para habilitá-los para autenticação Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Agora, eles poderão fazer login com credenciais ad do Azure, em vez de usar sua senha de usuário PostgreSQL previamente configurada.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: O nome de usuário do PostgreSQL é diferente do nome principal do usuário do Azure AD

Se um usuário do PostgreSQL não existir no Azure AD ou tiver um nome de usuário diferente, você poderá usar grupos AD do Azure para autenticar como este usuário do PostgreSQL. Você pode migrar o Banco de Dados Azure existente para usuários do PostgreSQL para o Azure AD criando um grupo Azure AD com um nome que corresponda ao usuário postgreSQL e, em seguida, concedendo azure_ad_user de função ao usuário postgreSQL existente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Isso pressupõe que você criou um grupo "DBReadUser" em seu Azure AD. Os usuários pertencentes a esse grupo agora poderão entrar no banco de dados como este usuário.

## <a name="next-steps"></a>Próximas etapas

* Revise os conceitos gerais para [autenticação do Azure Active Directory com o Azure Database for PostgreSQL - Single Server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
