---
title: Copiar um banco de dados
description: Crie uma cópia consistente transacionalmente de um banco de dados SQL do Azure existente no mesmo servidor ou em um servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473737"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Fazer uma cópia consistente transicionalmente de um banco de dados SQL do Azure

O Azure SQL Database fornece vários métodos para criar uma cópia transactivamente consistente de um banco de dados Azure SQL[(banco de dados único)](sql-database-single-database.md)existente no mesmo servidor ou em um servidor diferente. Você pode copiar um Banco de Dados SQL usando o Portal do Azure, o PowerShell ou o T-SQL.

## <a name="overview"></a>Visão geral

Uma cópia do banco de dados é um instantâneo do banco de dados de origem no momento da solicitação de cópia. Você pode selecionar o mesmo servidor ou um servidor diferente. Além disso, você pode optar por manter seu nível de serviço e tamanho de computação, ou usar um tamanho de computação diferente dentro do mesmo nível de serviço (edição). Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Neste ponto, é possível atualizar ou fazer o downgrade para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados independentemente. A cópia é criada usando a tecnologia de geo-replicação e, uma vez que a semeadura é concluída, o link de geo-replicação é automaticamente encerrado. Todos os requisitos para usar a replicação geográfica aplicam-se à operação de cópia do banco de dados. Consulte [a visão geral da geo-replicação ativa](sql-database-active-geo-replication.md) para obter detalhes.

> [!NOTE]
> [Backups automatizados de banco de dados](sql-database-automated-backups.md) são usados quando você cria uma cópia de banco de dados.

## <a name="logins-in-the-database-copy"></a>Logons na cópia do banco de dados

Quando você copia um banco de dados no mesmo servidor do Banco de Dados SQL, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados do banco de dados. 

Quando você copia um banco de dados para um servidor de banco de dados SQL diferente, o princípio de segurança que iniciou a operação de cópia no servidor de destino torna-se o proprietário do novo banco de dados. 

Independentemente do servidor de destino, todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados. O uso [de usuários de banco de dados contidos](sql-database-manage-logins.md) para acesso a dados garante que o banco de dados copiado tenha as mesmas credenciais de usuário, para que após a cópia seja concluída, você possa acessá-la imediatamente com as mesmas credenciais.

Se você usar logins de nível de servidor para acesso a dados e copiar o banco de dados para um servidor diferente, o acesso baseado em login pode não funcionar. Isso pode acontecer porque os logins não existem no servidor de destino, ou porque suas senhas e identificadores de segurança (SIDs) são diferentes. Para saber como gerenciar logons quando você copia um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre)](sql-database-geo-replication-security-config.md). Após o sucesso da operação de cópia para um servidor diferente, e antes que outros usuários sejam remnadas, apenas o login associado ao proprietário do banco de dados ou o administrador do servidor podem fazer login no banco de dados copiado. Para resolver logins e estabelecer o acesso aos dados após a operação de cópia ser concluída, consulte [Resolver logins](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Cópia do banco de dados usando o Portal do Azure

Para copiar um banco de dados usando o Portal do Azure, abra a página do banco de dados e clique em **Copiar**.

   ![Cópia do banco de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Copie um banco de dados usando PowerShell ou Azure CLI

Para copiar um banco de dados, use os seguintes exemplos.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para o PowerShell, use o [cmdlet New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente após a solicitação ser aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, solte o banco de dados de destino usando o [cmdlet Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Para obter uma amostra completa do script PowerShell, consulte [Copiar um banco de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente após a solicitação ser aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, solte o banco de dados de destino usando o comando [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Funções RBAC para gerenciar cópia de banco de dados

Para criar uma cópia de banco de dados, você precisará estar nas seguintes funções

- Proprietário da assinatura ou ter
- Função de contribuinte do servidor SQL ou
- Função personalizada nos bancos de dados de origem e destino com a seguinte permissão:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Para cancelar uma cópia do banco de dados, você precisará estar nas seguintes funções

- Proprietário da assinatura ou ter
- Função de contribuinte do servidor SQL ou
- Função personalizada nos bancos de dados de origem e destino com a seguinte permissão:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Para gerenciar a cópia do banco de dados usando o portal Azure, você também precisará das seguintes permissões:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatus/read

Se você quiser ver as operações em implantação no grupo de recursos no portal, operações em vários provedores de recursos, incluindo operações SQL, você precisará dessas funções adicionais de RBAC:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/deployments/operations/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Cópia de banco de dados usando o Transact-SQL

Faça login no banco de dados principal com o login do administrador do servidor ou o login que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem `dbmanager` sucedida, os logins que não são o administrador do servidor devem ser membros da função. Para saber mais sobre logons e como se conectar ao servidor, confira [Gerenciar logons](sql-database-manage-logins.md).

Comece a copiar o banco de dados de origem com o [BANCO DE DADOS CREATE ... COMO CÓPIA DA](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) DECLARAÇÃO. A declaração T-SQL continua em execução até que a operação de cópia do banco de dados esteja concluída.

> [!NOTE]
> O término da declaração T-SQL não encerra a operação de cópia do banco de dados. Para encerrar a operação, solte o banco de dados de alvos.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor

Faça login no banco de dados principal com o login do administrador do servidor ou o login que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem `dbmanager` sucedida, os logins que não são o administrador do servidor devem ser membros da função.

Esse comando copia o Banco de dados 1 para um novo banco de dados chamado Database2 (Banco de dados 2) no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Faça login no banco de dados mestre do servidor de destino onde o novo banco de dados deve ser criado. Use um login que tenha o mesmo nome e senha do proprietário do banco de dados do banco de dados de origem no servidor de origem. O login no servidor de destino também `dbmanager` deve ser um membro da função, ou ser o login do administrador do servidor.

Esse comando copia o Database1 no servidor 1- para um novo banco de dados chamado Database2 no servidor 2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Os firewalls de ambos os servidores devem ser configurados para permitir a conexão de entrada a partir do IP do cliente emissor do Banco de Dados De Criação T-SQL ... COMO CÓPIA DO comando.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copie um banco de dados SQL para uma assinatura diferente

Você pode usar as etapas do [banco de dados Copiar um SQL para uma](#copy-a-sql-database-to-a-different-server) seção de servidor diferente para copiar seu banco de dados para um servidor sql database em uma assinatura diferente usando T-SQL. Certifique-se de usar um login com o mesmo nome e senha do banco de dados do banco de dados de origem. Além disso, o login deve `dbmanager` ser um membro da função ou um administrador de servidor, tanto nos servidores de origem quanto de destino.

> [!NOTE]
> O [portal Azure,](https://portal.azure.com)powershell e Azure CLI não suportam cópia de banco de dados para uma assinatura diferente.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorar o andamento da operação de cópia

Monitore o processo de cópia consultando as [visualizações sys.,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Enquanto a cópia estiver em andamento, a coluna **state_desc** da exibição sys.databases para o novo banco de dados é definida como **COPYING**.

* Se a cópia falhar, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **SUSPECT**. Execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for bem-sucedida, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **ONLINE**. A cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.

> [!NOTE]
> Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) no novo banco de dados.

> [!IMPORTANT]
> Se você precisar criar uma cópia com um objetivo de serviço substancialmente menor do que a fonte, o banco de dados de destino pode não ter recursos suficientes para concluir o processo de semeação e pode fazer com que a operação de cópia falhe. Neste cenário, use uma solicitação de georestauração para criar uma cópia em um servidor diferente e/ou em uma região diferente. Consulte [Recuperar um banco de dados SQL do Azure usando backups de banco de dados](sql-database-recovery-using-backups.md#geo-restore) para obter mais informações.

## <a name="resolve-logins"></a>Resolver logons

Depois que o novo banco de dados estiver on-line no servidor de destino, use a declaração [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) para remapear os usuários do novo banco de dados para logins no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Veja também [Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados torna-se o proprietário do banco de dados do novo banco de dados. Depois que a cópia for bem sucedida e antes que outros usuários sejam remapeados, apenas o proprietário do banco de dados pode fazer login no novo banco de dados.

Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery](sql-database-geo-replication-security-config.md) (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre).

## <a name="database-copy-errors"></a>Erros de cópia de banco de dados

Os erros a seguir podem ser encontrados durante a cópia de um banco de dados no Banco de Dados SQL do Azure. Para saber mais, confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md).

| Código do erro | Severity | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |O cliente com endereço IP “%.&#x2a;ls” está desabilitado temporariamente. |
| 40637 |16 |A criação da cópia do banco de dados está desabilitada no momento. |
| 40561 |16 |Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia do banco de dados. O banco de dados de origem foi descartado. |
| 40563 |16 |Falha na cópia do banco de dados. O banco de dados de destino foi descartado. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia do banco de dados. Não é permitida mais de uma cópia simultânea do banco de dados com a mesma origem. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre logons, consulte [Gerenciar logons](sql-database-manage-logins.md) e [Como gerenciar a segurança de Banco de Dados SQL do Azure após a recuperação de desastres](sql-database-geo-replication-security-config.md).
- Para exportar um banco de dados, consulte [Exportar o banco de dados para um BACPAC](sql-database-export.md).
