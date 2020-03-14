---
title: Autenticação Active Directory-banco de dados do Azure para MySQL
description: Saiba mais sobre os conceitos de Azure Active Directory para autenticação com o banco de dados do Azure para MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299015"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usar Azure Active Directory para autenticar com o MySQL

A autenticação do Microsoft Azure Active Directory (AD do Azure) é um mecanismo de conexão ao banco de dados do Azure para MySQL usando identidades definidas no Azure AD.
Com a autenticação do Azure AD, você pode gerenciar identidades de usuário de banco de dados e outros serviços da Microsoft em um local central, o que simplifica o gerenciamento de permissões.

> [!IMPORTANT]
> A autenticação do Azure AD para o banco de dados do Azure para MySQL está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os benefícios de usar o Azure AD incluem:

- Autenticação de usuários em serviços do Azure de forma uniforme
- Gerenciamento de políticas de senha e rotação de senha em um único local
- Várias formas de autenticação com suporte pelo Azure Active Directory, o que pode eliminar a necessidade de armazenar senhas
- Os clientes podem gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
- A autenticação do Azure AD usa usuários do banco de dados MySQL para autenticar identidades no nível do banco de dados
- Suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados do Azure para MySQL

Para configurar e usar a autenticação Azure Active Directory, use o seguinte processo:

1. Crie e popule Azure Active Directory com identidades de usuário, conforme necessário.
2. Opcionalmente, associe ou altere os Active Directory associados atualmente à sua assinatura do Azure.
3. Crie um administrador do Azure AD para o servidor de banco de dados do Azure para MySQL.
4. Crie usuários de banco de dados em seu banco de dados mapeado para identidades do Azure AD.
5. Conecte-se ao banco de dados recuperando um token para uma identidade do Azure AD e fazendo logon.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados do Azure para MySQL, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Arquitetura

O diagrama de alto nível a seguir resume como a autenticação funciona usando a autenticação do Azure AD com o banco de dados do Azure para MySQL. As setas indicam caminhos para comunicação.

![fluxo de autenticação][1]

## <a name="administrator-structure"></a>Estrutura do administrador

Ao usar a autenticação do Azure AD, há duas contas de administrador para o servidor MySQL; o administrador do MySQL original e o administrador do Azure AD. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo vários administradores do Azure AD para o servidor MySQL. O uso de uma conta de grupo como administrador aprimora a capacidade de gerenciamento, permitindo adicionar e remover centralmente membros do grupo no Azure AD sem alterar os usuários ou permissões no servidor MySQL. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][2]

## <a name="permissions"></a>Permissões

Para criar novos usuários que podem se autenticar com o Azure AD, você deve ser o administrador do Azure AD criado. Esse usuário é atribuído Configurando a conta de administrador do Azure AD para um servidor de banco de dados do Azure específico para MySQL.

Para criar um novo usuário de banco de dados do Azure AD, você deve se conectar como administrador do Azure AD. Isso é demonstrado em [configurar e fazer logon com o Azure ad para o banco de dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o banco de dados do Azure para MySQL. Se o administrador do Azure Active Directory tiver sido removido do servidor, Azure Active Directory usuários existentes criados anteriormente não poderão mais se conectar ao banco de dados usando suas credenciais de Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Conectar-se usando as identidades do Azure AD

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Universal do Azure Active Directory com o MFA
- Usando Active Directory certificados de aplicativo ou segredos de cliente

Depois de autenticado no Active Directory, você recupera um token. Esse token é sua senha para fazer logon.

> [!NOTE]
> Para obter mais detalhes sobre como se conectar com um token de Active Directory, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Considerações adicionais

- Somente um administrador do Azure AD pode ser configurado para um servidor de banco de dados do Azure para MySQL a qualquer momento.
- Somente um administrador do Azure AD para MySQL pode se conectar inicialmente ao banco de dados do Azure para MySQL usando uma conta de Azure Active Directory. O administrador do Active Directory pode configurar os próximos usuários do banco de dados do Azure AD.
- Se um usuário for excluído do Azure AD, esse usuário não poderá mais se autenticar com o Azure AD e, portanto, não será mais possível adquirir um token de acesso para esse usuário. Nesse caso, embora o usuário correspondente ainda esteja no banco de dados, não será possível se conectar ao servidor com esse usuário.
> [!NOTE]
> O logon com o usuário do Azure AD excluído ainda pode ser feito até que o token expire (até 60 minutos de emissão de tokens).  Se você também remover o usuário do banco de dados do Azure para MySQL, esse acesso será revogado imediatamente.
- Se o administrador do Azure AD for removido do servidor, o servidor não será mais associado a um locatário do Azure AD e, portanto, todos os logons do Azure AD serão desabilitados para o servidor. Adicionar um novo administrador do Azure AD do mesmo locatário reabilitará os logons do Azure AD.
- O banco de dados do Azure para MySQL corresponde a tokens de acesso para o usuário do banco de dados do Azure para MySQL usando a ID de usuário exclusiva do Azure AD do usuário, em vez de usar o nome de usuários. Isso significa que se um usuário do Azure AD for excluído do Azure AD e um novo usuário criado com o mesmo nome, o banco de dados do Azure para MySQL considerará que um usuário diferente. Portanto, se um usuário for excluído do Azure AD e um novo usuário com o mesmo nome for adicionado, o novo usuário não poderá se conectar com o usuário existente.

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados do Azure para MySQL, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Para obter uma visão geral de logons e usuários de banco de dados do Azure para MySQL, consulte [criar usuários no banco de dados do Azure para MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
