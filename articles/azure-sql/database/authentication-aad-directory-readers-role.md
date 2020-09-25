---
title: Função Leitores de Diretório no Azure Active Directory para o SQL do Azure
description: Saiba mais sobre a função do leitor de diretório no Azure AD para Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5a6fb2c1c539c5b8e353f5c3720cb9d001dcbbc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277928"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Função Leitores de Diretório no Azure Active Directory para o SQL do Azure

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Este recurso neste artigo está em **Visualização pública**.

O Azure Active Directory (Azure AD) introduziu o [uso de grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../../active-directory/users-groups-roles/roles-groups-concept.md). Isso permite que as funções do Azure AD sejam atribuídas a grupos.

Ao habilitar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para o banco de dados SQL do Azure, o SQL instância gerenciada do Azure ou o Azure Synapse Analytics, a função de [**leitores de diretório**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) do Azure ad deve ser atribuída à identidade para permitir o acesso de leitura ao [API do Graph do Azure ad](../../active-directory/develop/active-directory-graph-api.md). A identidade gerenciada do banco de dados SQL e do Azure Synapse é conhecida como a identidade do servidor. A identidade gerenciada do SQL Instância Gerenciada é conhecida como a identidade da instância gerenciada e é automaticamente atribuída quando a instância é criada. Para obter mais informações sobre como atribuir uma identidade de servidor ao banco de dados SQL ou ao Azure Synapse, consulte [habilitar entidades de serviço para criar usuários do Azure ad](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

A função **leitores de diretório** é necessária para:

- Criar logons do Azure AD para o SQL Instância Gerenciada
- Representar usuários do Azure AD no Azure SQL
- Migrar SQL Server usuários que usam a autenticação do Windows para o SQL Instância Gerenciada com a autenticação do Azure AD (usando o comando [ALTER User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Alterar o administrador do Azure AD para o SQL Instância Gerenciada
- Permitir que as [entidades de serviço (aplicativos)](authentication-aad-service-principal.md) criem usuários do Azure AD no Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Atribuindo a função leitores de diretório

Para atribuir a função de [**leitores de diretório**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) a uma identidade, um usuário com permissões de administrador [global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) ou de [administrador de função privilegiada](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) é necessário. Os usuários que geralmente gerenciam ou implantam o banco de dados SQL, o SQL Instância Gerenciada ou o Azure Synapse podem não ter acesso a essas funções altamente privilegiadas. Isso pode, muitas vezes, causar complicações para os usuários que criam recursos do SQL do Azure não planejados ou precisam de ajuda de membros com função altamente privilegiada que geralmente são inacessíveis em grandes organizações.

Para a Instância Gerenciada de SQL, atribua a função **Leitores de diretório** à identidade da instância gerenciada para [configurar um administrador do Azure AD para a instância gerenciada](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

A atribuição da função de **Leitores de diretório** à identidade do servidor não é necessária para o Banco de Dados SQL nem para o Azure Synapse ao configurar um administrador do Azure AD para o servidor lógico. No entanto, para habilitar a criação de um objeto do Azure AD no Banco de Dados SQL ou no Azure Synapse em nome de um aplicativo do Azure AD, a função **Leitores de diretório** é necessária. Se a função não for atribuída à identidade do servidor lógico do SQL, a criação de usuários do Azure AD no Azure SQL falhará. Para obter mais informações, confira [Entidade de serviço do Azure Active Directory com o SQL do Azure](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Concedendo a função leitores de diretório a um grupo do Azure AD

Atualmente em **Visualização pública**, agora você pode ter um administrador [global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) ou [administrador de função com privilégios](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) para criar um grupo do Azure AD e atribuir a permissão de [**leitores de diretório**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) ao grupo. Isso permitirá o acesso ao API do Graph do Azure AD para membros deste grupo. Além disso, os usuários do Azure AD que são proprietários desse grupo têm permissão para atribuir novos membros para esse grupo, incluindo identidades dos servidores lógicos do SQL Azure.

Essa solução ainda requer um usuário de alto privilégio (administrador global ou administrador de função com privilégios) para criar um grupo e atribuir usuários como uma atividade de uma vez, mas os proprietários do grupo do Azure AD poderão atribuir outros membros no futuro. Isso elimina a necessidade de envolver um usuário de alto privilégio no futuro para configurar todos os bancos de dados SQL, instâncias gerenciadas do SQL ou servidores Synapse do Azure em seu locatário do Azure AD.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Atribuir a função de Leitores de diretório a um grupo do Azure AD e gerenciar atribuições de função](authentication-aad-directory-readers-role-tutorial.md)
