---
title: Criar usuários - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484920"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Criar usuários no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)

> [!NOTE]
> O termo "usuários" refere-se aos usuários dentro de um grupo de servidores Hyperscale (Citus). Para aprender sobre os usuários de assinatura do Azure e seus privilégios, visite o [artigo rbac (controle de acesso baseado em função da azure)](../role-based-access-control/built-in-roles.md) ou [revise como personalizar funções](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>A conta do administrador do servidor

O mecanismo PostgreSQL usa [funções](https://www.postgresql.org/docs/current/sql-createrole.html) para controlar o acesso a objetos de banco de dados, e um grupo de servidores Hyperscale (Citus) recém-criado vem com várias funções pré-definidas:

* As [funções postgreSQL padrão](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Como o Hyperscale é um serviço PaaS gerenciado, `postgres` apenas a Microsoft pode fazer login com a função de super usuário. Para acesso administrativo limitado, `citus` a Hyperscale fornece a função.

Permissões para `citus` a função:

* Leia todas as variáveis de configuração, mesmo variáveis normalmente visíveis apenas para superusuários.
* Leia todas\_\_ \* as visualizações do pg stat e use várias extensões relacionadas a estatísticas -- até mesmo visualizações ou extensões normalmente visíveis apenas para superusuários.
* Execute funções de monitoramento que podem levar bloqueios DE COMPARTILHAMENTO DE ACESSO em tabelas, potencialmente por um longo tempo.
* [Crie extensões PostgreSQL](concepts-hyperscale-extensions.md) (porque a `azure_pg_admin`função é membro de ).

Notavelmente, `citus` o papel tem algumas restrições:

* Não pode criar papéis
* Não é possível criar bancos de dados

## <a name="how-to-create-additional-user-roles"></a>Como criar funções adicionais do usuário

Como mencionado, `citus` a conta de administração não tem permissão para criar usuários adicionais. Para adicionar um usuário, use a interface do portal Azure.

1. Vá para a página **Funções** para o grupo de servidor Hyperscale e clique **em + Adicionar**:

   ![A página de papéis](media/howto-hyperscale-create-users/1-role-page.png)

2. Digite o nome da função e a senha. Clique em **Salvar**.

   ![Adicionar função](media/howto-hyperscale-create-users/2-add-user-fields.png)

O usuário será criado no nó coordenador do grupo de servidores, e propagado para todos os nós do trabalhador. As funções criadas através do `LOGIN` portal Azure têm o atributo, o que significa que são usuários verdadeiros que podem fazer login no banco de dados.

## <a name="how-to-modify-privileges-for-user-role"></a>Como modificar privilégios para função de usuário

Novas funções de usuário são comumente usadas para fornecer acesso ao banco de dados com privilégios restritos. Para modificar privilégios de usuário, use comandos PostgreSQL padrão, usando uma ferramenta como PgAdmin ou psql. (Consulte [conectar-se com psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) no quickstart da Hyperscale (Citus).)

Por exemplo, `db_user` para `mytable`permitir a leitura, conceda a permissão:

```sql
GRANT SELECT ON mytable TO db_user;
```

A Hyperscale (Citus) propropaga as declarações de GRANT de tabela única através de todo o cluster, aplicando-as em todos os nós do trabalhador. No entanto, os GRANTs que são em todo o sistema (por exemplo, para todas as tabelas em um esquema) precisam ser executados em cada nó de data.  Use `run_command_on_workers()` a função auxiliar:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Como excluir uma função de usuário ou alterar sua senha

Para atualizar um usuário, visite a página **Funções** para o seu grupo de servidor Hyperscale e clique nas elipses **...** ao lado do usuário. As elipses abrirão um menu para excluir o usuário ou redefinir sua senha.

   ![Editar um papel](media/howto-hyperscale-create-users/edit-role.png)

O `citus` papel é privilegiado e não pode ser excluído.

## <a name="next-steps"></a>Próximas etapas

Abra o firewall para os endereços IP das máquinas dos novos usuários para permitir que eles se conectem: [Crie e gerencie as regras de firewall Hyperscale (Citus) usando o portal Azure](howto-hyperscale-manage-firewall-using-portal.md).

Para obter mais informações sobre o gerenciamento da conta de usuário do banco de dados, consulte a documentação do produto PostgreSQL:

* [Funções e privilégios do banco de dados](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html)
