---
title: Usar o Azure Active Directory-banco de dados do Azure para MySQL-servidor único
description: Saiba mais sobre como configurar o Azure Active Directory (Azure AD) para autenticação com o banco de dados do Azure para MySQL-servidor único
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 10dae81bf0ca8958f7c10aebef501fc604c4839c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706044"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usar Azure Active Directory para autenticar com o MySQL

Este artigo explicará as etapas de como configurar o acesso a Azure Active Directory com o banco de dados do Azure para MySQL e como se conectar usando um token do Azure AD.

> [!IMPORTANT]
> A autenticação do Azure AD para o banco de dados do Azure para MySQL está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Configurando o usuário administrador do Azure AD

Somente um usuário administrador do Azure AD pode criar/habilitar usuários para autenticação baseada no Azure AD. Para criar o e o usuário administrador do Azure AD, siga as etapas a seguir

1. Na portal do Azure, selecione a instância do banco de dados do Azure para MySQL que você deseja habilitar para o Azure AD.
2. Em configurações, selecione administrador de Active Directory:

![definir administrador do Azure AD][2]

3. Selecione um usuário válido do Azure AD no locatário do cliente para ser administrador do Azure AD.

> [!IMPORTANT]
> Ao definir o administrador, um novo usuário é adicionado ao banco de dados do Azure para servidor MySQL com permissões de administrador completo.

Somente um administrador do Azure AD pode ser criado por servidor MySQL e a seleção de outro substituirá o administrador do Azure AD existente configurado para o servidor.

Em uma versão futura, vamos dar suporte à especificação de um grupo do Azure AD em vez de um usuário individual para ter vários administradores. no entanto, atualmente, não há suporte para isso.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Criando usuários do Azure AD no banco de dados do Azure para MySQL

Para adicionar um usuário do Azure AD ao banco de dados do Azure para MySQL, execute as etapas a seguir após a conexão (consulte a seção mais adiante sobre como se conectar):

1. Primeiro, verifique se o usuário do Azure AD `<user>@yourtenant.onmicrosoft.com` é um usuário válido no locatário do Azure AD.
2. Entre em sua instância do banco de dados do Azure para MySQL como o usuário administrador do Azure AD.
3. Criar `<user>@yourtenant.onmicrosoft.com` de usuário no banco de dados do Azure para MySQL.

**Exemplo:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Para nomes de usuário que excedem 32 caracteres, é recomendável usar um alias em vez disso, para ser usado ao conectar: 

Exemplo:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> A autenticação de um usuário por meio do Azure AD não concede ao usuário nenhuma permissão para acessar objetos no banco de dados do Azure para MySQL. Você deve conceder ao usuário as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Criando grupos do Azure AD no banco de dados do Azure para MySQL

Para habilitar um grupo do Azure AD para acesso ao seu banco de dados, use o mesmo mecanismo para os usuários, mas em vez disso, especifique o nome do Grupo:

**Exemplo:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Ao fazer logon, os membros do grupo usarão seus tokens de acesso pessoal, mas assinarão com o nome do grupo especificado como o nome de usuário.

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Conectando-se ao banco de dados do Azure para MySQL usando o Azure AD

O diagrama de alto nível a seguir resume o fluxo de trabalho do uso da autenticação do Azure AD com o banco de dados do Azure para MySQL:

![fluxo de autenticação][1]

Criamos a integração do Azure AD para trabalhar com ferramentas comuns do MySQL, como a CLI do MySQL, que não são compatíveis com o Azure AD e só dão suporte à especificação de nome de usuário e senha ao se conectar ao MySQL. Passamos o token do Azure AD como a senha, conforme mostrado na imagem acima.

No momento, testamos os seguintes clientes:

- MySQLWorkbench 
- CLI do MySQL

Também testamos os drivers de aplicativos mais comuns, você pode ver os detalhes no final desta página.

Estas são as etapas que um usuário/aplicativo precisará fazer a autenticação com o Azure AD descrito abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Etapa 1: autenticar com o Azure AD

Verifique se você tem o [CLI do Azure instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta de CLI do Azure para autenticar com o Azure AD. Ele exige que você forneça a ID de usuário e a senha do Azure AD.

```
az login
```

Esse comando abrirá uma janela do navegador na página de autenticação do Azure AD.

> [!NOTE]
> Você também pode usar Azure Cloud Shell para executar essas etapas.
> Lembre-se de que, ao recuperar o token de acesso do Azure AD no Azure Cloud Shell você precisará chamar explicitamente `az login` e entrar novamente (na janela separada com um código). Depois disso, o comando `get-access-token` funcionará conforme o esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Etapa 2: recuperar o token de acesso do Azure AD

Invoque a ferramenta de CLI do Azure para adquirir um token de acesso para o usuário autenticado do Azure AD da etapa 1 para acessar o banco de dados do Azure para MySQL.

Exemplo (para nuvem pública):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor do recurso acima deve ser especificado exatamente como mostrado. Para outras nuvens, o valor do recurso pode ser pesquisado usando:

```shell
az cloud show
```

Para CLI do Azure versão 2.0.71 e posterior, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```shell
az account get-access-token --resource-type oss-rdbms
```

Depois que a autenticação for bem-sucedida, o Azure AD retornará um token de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O token é uma cadeia de caracteres base 64 que codifica todas as informações sobre o usuário autenticado e que é direcionado para o serviço de banco de dados do Azure para MySQL.

> [!NOTE]
> A validade do token de acesso é de qualquer lugar entre 5 minutos e 60 minutos. Recomendamos que você obtenha o token de acesso logo antes de iniciar o logon no banco de dados do Azure para MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Etapa 3: usar o token como senha para fazer logon com o MySQL

Ao se conectar, você precisa usar o token de acesso como a senha de usuário do MySQL. Ao usar clientes de GUI, como MySQLWorkbench, você pode usar o método acima para recuperar o token. 

Ao usar a CLI, você pode usar essa pequena mão para se conectar: 

**Exemplo (Linux/macOS):**

MySQL-h mydb.mysql.database.azure.com \--usuário user@tenant.onmicrosoft.com@mydb \--Enable-texto não criptografado-plugin \--senha =`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`  

Observe a configuração "Enable-untexto-plugin" – você precisa usar uma configuração semelhante com outros clientes para garantir que o token seja enviado ao servidor sem que haja hash.

Agora você está autenticado no servidor MySQL usando a autenticação do Azure AD.

## <a name="token-validation"></a>Validação de token

A autenticação do Azure AD no banco de dados do Azure para MySQL garante que o usuário exista no servidor MySQL e verifica a validade do token Validando o conteúdo do token. As seguintes etapas de validação de token são executadas:

-   O token é assinado pelo Azure AD e não foi violado
-   O token foi emitido pelo Azure AD para o locatário associado ao servidor
-   O token não expirou
-   O token é para o recurso de banco de dados do Azure para MySQL (e não outro recurso do Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilidade com drivers de aplicativos

A maioria dos drivers tem suporte, no entanto, certifique-se de usar as configurações para enviar a senha em texto não criptografado, para que o token seja enviado sem modificação.

* C/C++
  * libmysqlclient: com suporte
  * MySQL-Connector-c + +: com suporte
* Java
  * Connector/J (MySQL-Connector-Java): com suporte, deve utilizar `useSSL` configuração
* Python
  * Conector/Python: com suporte
* Ruby
  * mysql2: com suporte
* .NET
  * MySQL-Connector-Net: com suporte, é necessário adicionar o plug-in para mysql_clear_password
  * MySQL-net/MySqlConnector: com suporte
* Node.js
  * mysqljs: sem suporte (não envia token em texto não criptografado sem patch)
  * nó-mysql2: com suporte
* PERL
  * DBD:: MySQL: com suporte
  * NET:: MySQL: sem suporte
* Go
  * Go-SQL-Driver: com suporte, adicione `?tls=true&allowCleartextPasswords=true` à cadeia de conexão

## <a name="next-steps"></a>Próximos passos

* Examine os conceitos gerais para [Azure Active Directory autenticação com o banco de dados do Azure para MySQL-servidor único](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
