---
title: Use o Azure Active Directory - Banco de dados Azure para MySQL
description: Saiba como configurar o Azure Active Directory (Azure AD) para autenticação com o Banco de Dados Azure para MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298998"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Use o Azure Active Directory para autenticar com o MySQL

Este artigo irá mostrar as etapas de como configurar o acesso ao Azure Active Directory com o Azure Database for MySQL e como se conectar usando um token Azure AD.

> [!IMPORTANT]
> A autenticação Azure AD para O Banco de Dados Azure para MySQL está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Configuração do usuário admin Azure AD

Apenas um usuário do AdMin Azure AD pode criar/habilitar usuários para autenticação baseada no Azure AD. Para criar e o usuário admin Addazure, siga as seguintes etapas

1. No portal Azure, selecione a instância do Banco de Dados Azure para MySQL que você deseja habilitar para o Azure AD.
2. Em Configurações, selecione O Admin de diretório ativo:

![definir administrador de anúncios azure][2]

3. Selecione um usuário Ad azure válido no inquilino do cliente para ser administrador aazure AD.

> [!IMPORTANT]
> Ao configurar o administrador, um novo usuário é adicionado ao banco de dados Azure para o servidor MySQL com permissões completas de administrador.

Apenas um administrador Azure AD pode ser criado por servidor MySQL e a seleção de outro substituirá o administrador Azure AD existente configurado para o servidor.

Em uma versão futura, suportaremos especificar um grupo Azure AD em vez de um usuário individual ter vários administradores, no entanto, isso ainda não é suportado.

Depois de configurar o administrador, agora você pode fazer login:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Conectando-se ao banco de dados Do Zure para MySQL usando o Azure AD

O diagrama de alto nível a seguir resume o fluxo de trabalho do uso da autenticação Azure AD com o Azure Database para MySQL:

![fluxo de autenticação][1]

Projetamos a integração azure AD para trabalhar com ferramentas MySQL comuns, como o mysql CLI, que não são cientes do Azure AD e só suportam especificar nome de usuário e senha ao se conectar ao MySQL. Passamos o token Azure AD como a senha como mostrado na imagem acima.

Atualmente, testamos os seguintes clientes:

- MySQLWorkbench 
- Mysql CLI

Também testamos os drivers de aplicativos mais comuns, você pode ver detalhes no final desta página.

Estas são as etapas que um usuário/aplicativo precisará fazer para autenticar com o Azure AD descrito abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Passo 1: Autenticar com Azure AD

Certifique-se de ter [o Azure CLI instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta Azure CLI para autenticar com o Azure AD. Ele exige que você forneça seu ID de usuário Azure AD e a senha.

```
az login
```

Este comando iniciará uma janela do navegador para a página de autenticação do Azure AD.

> [!NOTE]
> Você também pode usar o Azure Cloud Shell para executar essas etapas.
> Por favor, esteja ciente de que ao recuperar o token de acesso a Azure AD no Azure Cloud Shell, você precisará ligar `az login` e fazer login explicitamente (na janela separada com um código). Depois desse sinal `get-access-token` no comando funcionará como esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passo 2: Recuperar o token de acesso ao Azure AD

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o usuário autenticado Azure AD a partir da etapa 1 para acessar o Banco de Dados Azure para MySQL.

Exemplo (para Nuvem Pública):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor de recurso acima deve ser especificado exatamente como mostrado. Para outras nuvens, o valor do recurso pode ser analisado usando:

```shell
az cloud show
```

Para a versão 2.0.71 do Azure CLI e posterior, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```shell
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

O token é uma seqüência base 64 que codifica todas as informações sobre o usuário autenticado, e que é direcionada ao banco de dados Azure para o serviço MySQL.

> [!NOTE]
> A validade do token de acesso é entre 5 minutos e 60 minutos. Recomendamos que você obtenha o token de acesso pouco antes de começar o login no Azure Database para MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passo 3: Use o token como senha para fazer login com o MySQL

Ao conectar, você precisa usar o token de acesso como a senha do usuário MySQL. Ao usar clientes de GUI como MySQLWorkbench, você pode usar o método acima para recuperar o token. 

Ao usar o CLI, você pode usar esta abreviação para conectar: 

**Exemplo (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Observe a configuração "habilita-cleartext-plugin" – você precisa usar uma configuração semelhante com outros clientes para garantir que o token seja enviado para o servidor sem ser hashed.

Agora você está autenticado no seu servidor MySQL usando a autenticação Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Criando usuários azure AD no banco de dados Azure para MySQL

Para adicionar um usuário Azure AD ao banco de dados Azure para mySQL, execute as seguintes etapas após a conexão (veja a seção posterior sobre como se conectar):

1. Primeiro certifique-se de que `<user>@yourtenant.onmicrosoft.com` o usuário Azure AD é um usuário válido no inquilino Azure AD.
2. Faça login no seu banco de dados Do Azure para a instância MySQL como usuário do AdMin Do Azure AD.
3. Crie `<user>@yourtenant.onmicrosoft.com` o usuário no Banco de Dados Azure para MySQL.

**Exemplo:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Para nomes de usuários que excedam 32 caracteres, recomenda-se que você use um alias em vez disso, para ser usado ao conectar: 

Exemplo:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> A autenticação de um usuário através do Azure AD não dá ao usuário nenhuma permissão para acessar objetos dentro do banco de dados Azure para mysql. Você deve conceder manualmente ao usuário as permissões necessárias.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Criando grupos AD do Azure no banco de dados Azure para MySQL

Para habilitar um grupo Azure AD para acesso ao seu banco de dados, use o mesmo mecanismo que para usuários, mas, em vez disso, especifique o nome do grupo:

**Exemplo:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Ao fazer login, os membros do grupo usarão seus tokens de acesso pessoal, mas assinarão com o nome do grupo especificado como nome de usuário.

## <a name="token-validation"></a>Validação de tokens

A autenticação do Azure AD no Banco de Dados Azure para MySQL garante que o usuário exista no servidor MySQL e verifica a validade do token validando o conteúdo do token. As seguintes etapas de validação de token são executadas:

-   Token é assinado pelo Azure AD e não foi adulterado
-   O token foi emitido pelo Azure AD para o inquilino associado ao servidor
-   O token não expirou
-   O Token é para o banco de dados Do Zure para recursos MySQL (e não outro recurso do Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilidade com motoristas de aplicativos

A maioria dos drivers é suportada, no entanto, certifique-se de usar as configurações para enviar a senha em texto claro, para que o token seja enviado sem modificação.

* C/C++
  * libmysqlclient: Suportado
  * mysql-connector-c++: Suportado
* Java
  * Conector/J (mysql-connector-java): Suportado, `useSSL` deve utilizar a configuração
* Python
  * Conector/Python: Suportado
* Ruby
  * mysql2: Suportado
* .NET
  * mysql-connector-net: suportado, precisa adicionar plugin para mysql_clear_password
  * mysql-net/MySqlConnector: Suportado
* Node.js
  * mysqljs: Não suportado (não envia token em texto claro sem patch)
  * nó-mysql2: Suportado
* Perl
  * DBD::mysql: Suportado
  * Rede::MySQL: Não suportado
* Go
  * go-sql-driver: suportado, `?tls=true&allowCleartextPasswords=true` adicione à seqüência de conexão

## <a name="next-steps"></a>Próximas etapas

* Revise os conceitos gerais para [autenticação do Azure Active Directory com o Azure Database para MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
