---
title: Restaurar um banco de dados a partir de um backup
description: Saiba mais sobre a restauração point-in-time, que permite reverter um banco de dados SQL do Azure até 35 dias.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268737"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Recupere um banco de dados SQL do Azure usando backups automatizados de banco de dados

Por padrão, os backups do Banco de Dados Azure SQL são armazenados em armazenamento de blob geo-replicado (tipo de armazenamento RA-GRS). As seguintes opções estão disponíveis para recuperação de banco de dados usando [backups automatizados de banco de dados](sql-database-automated-backups.md). Você pode:

- Crie um novo banco de dados no mesmo servidor sql database, recuperado em um ponto especificado dentro do período de retenção.
- Crie um banco de dados no mesmo servidor sql database, recuperado ao tempo de exclusão de um banco de dados excluído.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL na mesma região, recuperado ao ponto dos backups mais recentes.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL em qualquer outra região, recuperado ao ponto dos backups replicados mais recentes.

Se você configurou [a retenção de backup a longo prazo,](sql-database-long-term-retention.md)você também pode criar um novo banco de dados a partir de qualquer backup de retenção de longo prazo em qualquer servidor do Banco de Dados SQL.

> [!IMPORTANT]
> Você não pode substituir um banco de dados existente durante a restauração.

Quando você estiver usando os níveis de serviço Padrão ou Premium, a restauração do banco de dados pode incorrer em um custo extra de armazenamento. O custo extra é incorrido quando o tamanho máximo do banco de dados restaurado é maior do que a quantidade de armazenamento incluída no nível de serviço e nível de desempenho do banco de dados-alvo. Para obter detalhes de preço do armazenamento extra, confira a página [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluída, você pode evitar esse custo extra definindo o tamanho máximo do banco de dados para o valor incluído.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups automatizados de banco de dados é afetado por vários fatores:

- O tamanho do banco de dados.
- O tamanho do cálculo do banco de dados.
- O número de registros de transações envolvidos.
- A quantidade de atividade que precisa ser reproduzida para recuperar até o ponto de restauração.
- A largura de banda da rede se a restauração for para uma região diferente.
- O número de solicitações simultâneas de restauração que estão sendo processadas na região de destino.

Para um banco de dados grande ou muito ativo, a restauração pode levar várias horas. Se houver uma paralisação prolongada em uma região, é possível que um alto número de pedidos de georestauração seja iniciado para recuperação de desastres. Quando há muitas solicitações, o tempo de recuperação para bancos de dados individuais pode aumentar. A maioria das restaurações de banco de dados é concluída em menos de 12 horas.

Para uma única assinatura, há limitações no número de solicitações de restauração simultâneas. Essas limitações se aplicam a qualquer combinação de restaurações point-in-time, georestaurações e restaurações a partir de backup de retenção a longo prazo.

| | **Número máximo de solicitações simultâneas que estão sendo processadas** | **Número máximo de solicitações simultâneas que estão sendo enviadas** |
| :--- | --: | --: |
|Banco de dados único (por assinatura)|10|60|
|Pool Elástico (por pool)|4|200|
||||

Não há um método embutido para restaurar todo o servidor. Para obter um exemplo de como realizar essa tarefa, consulte [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deve ser um membro da função contribuinte do SQL Server na assinatura, ou ser o proprietário da assinatura. Para obter mais informações, consulte [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md). Você pode se recuperar usando o portal Azure, PowerShell ou a API REST. Você não pode usar Transact-SQL.

## <a name="point-in-time-restore"></a>Restauração em um momento determinado

Você pode restaurar um banco de dados autônomo, agrupado ou instância em um ponto anterior usando o portal Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases). A solicitação pode especificar qualquer nível de serviço ou tamanho de computação para o banco de dados restaurado. Certifique-se de que você tem recursos suficientes no servidor para o qual você está restaurando o banco de dados. Quando concluída, a restauração cria um novo banco de dados no mesmo servidor do banco de dados original. O banco de dados restaurado é cobrado a taxas normais, com base em seu nível de serviço e tamanho de computação. Você não incorre em acusações até que a restauração do banco de dados esteja completa.

Um banco de dados geralmente é restaurado para um ponto anterior para fins de recuperação. Você pode tratar o banco de dados restaurado como uma substituição para o banco de dados original, ou usá-lo como uma fonte de dados para atualizar o banco de dados original.

- **Substituição de banco de dados**

  Se você pretende que o banco de dados restaurado seja uma substituição para o banco de dados original, você deve especificar o tamanho e o nível de serviço do banco de dados original. Em seguida, você pode renomear o banco de dados original e dar ao banco de dados restaurado o nome original usando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) no T-SQL.

- **Recuperação de dados**

  Se você planeja recuperar dados do banco de dados restaurado para recuperar de um erro de usuário ou aplicativo, você precisa escrever e executar um script de recuperação de dados que extrai dados do banco de dados restaurado e se aplica ao banco de dados original. Embora a operação de restauração possa demorar muito para concluir, o banco de dados em restauração é visível na lista de banco de dados por todo o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados que não completou a restauração.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauração point-in-time usando o portal Azure

Você pode recuperar um único banco de dados SQL ou banco de dados de instâncias a um ponto no tempo a partir da lâmina de visão geral do banco de dados que deseja restaurar no portal Dozure.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para recuperar um banco de dados único ou agrupado a um ponto no tempo, usando o portal Azure, abra a página de visão geral do banco de dados e selecione **Restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup point-in-time a partir do qual um novo banco de dados será criado. 

  ![Captura de tela das opções de restauração de banco de dados](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para recuperar um banco de dados de instância gerenciada a um ponto no tempo, usando o portal Azure, abra a página de visão geral do banco de dados e selecione **Restaurar** na barra de ferramentas. Escolha o ponto de backup point-in-time a partir do qual um novo banco de dados será criado. 

  ![Captura de tela das opções de restauração de banco de dados](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programáticamente um banco de dados a partir de um backup, consulte [Programmaticamente executando a recuperação usando backups automatizados](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluído

Você pode restaurar um banco de dados excluído ao tempo de exclusão ou um ponto anterior no tempo, no mesmo servidor do Banco de Dados SQL ou na mesma instância gerenciada. Você pode fazer isso através do portal Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Você restaura um banco de dados excluído criando um novo banco de dados a partir do backup.

> [!IMPORTANT]
> Se você excluir um servidor de banco de dados SQL do Azure ou instância gerenciada, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído ou uma instância gerenciada.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauração de banco de dados excluída usando o portal Azure

Você restaura bancos de dados excluídos do portal Azure a partir do recurso de servidor e instância.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para recuperar um banco de dados excluído único ou agrupado para o tempo de exclusão usando o portal Azure, abra a página de visão geral do servidor e selecione **bancos de dados excluídos**. Selecione um banco de dados excluído que deseja restaurar e digite o nome para o novo banco de dados que será criado com dados restaurados a partir do backup.

  ![Captura de tela do banco de dados Azure SQL excluído de restauração](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para recuperar um banco de dados gerenciado usando o portal Azure, abra a página de visão geral da instância gerenciada e selecione **Bancos de dados Excluídos**. Selecione um banco de dados excluído que deseja restaurar e digite o nome para o novo banco de dados que será criado com dados restaurados a partir do backup.

  ![Captura de tela do banco de dados de instância sql do Azure excluído](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restauração de banco de dados excluída usando o PowerShell

Use os scripts de exemplo a seguir para restaurar um banco de dados excluído para o Azure SQL Database e uma instância gerenciada usando o PowerShell.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para obter um script de amostra powerShell mostrando como restaurar um banco de dados Azure SQL excluído, consulte [Restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para obter um script de exemplo do PowerShell mostrando como restaurar um banco de dados de instância excluído, consulte [Restaurar banco de dados excluído em instância gerenciada usando o PowerShell](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Para restaurar programáticamente um banco de dados excluído, consulte [Programmaticamente executando a recuperação usando backups automatizados](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Restauração geográfica

Você pode restaurar um banco de dados SQL em qualquer servidor em qualquer região do Azure a partir dos backups replicados geograficamente mais recentes. A restauração geográfica usa um backup geo-replicado como sua fonte. Você pode solicitar a georestauração mesmo que o banco de dados ou o datacenter esteja inacessível devido a uma paralisação.

Geo-restore é a opção de recuperação padrão quando seu banco de dados não está disponível devido a um incidente na região de hospedagem. É possível restaurar o banco de dados para um servidor em qualquer outra região. Há um atraso entre o momento em que um backup é realizado e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Como resultado, o banco de dados restaurado pode estar até uma hora atrás do banco de dados original. A ilustração a seguir mostra uma restauração de banco de dados do último backup disponível em outra região.

![Gráfico de georestauração](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-restauração usando o portal Azure

No portal Azure, você cria um novo banco de dados de instância única ou gerenciada e seleciona um backup de georestauração disponível. O banco de dados recém-criado contém os dados de backup geo-restaurados.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para georestaurar um único banco de dados SQL do portal Azure na região e servidor de sua escolha, siga estas etapas:

1. No **painel,** selecione **Adicionar** > **criar banco de dados SQL**. Na guia **Noções Básicas,** digite as informações necessárias.
2. Selecione **Configurações adicionais**.
3. Para **usar dados existentes,** selecione **Backup**.
4. Em **Backup,** selecione um backup na lista de backups de georestauração disponíveis.

    ![Captura de tela de criar opções de banco de dados SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Complete o processo de criação de um novo banco de dados a partir do backup. Quando você cria o banco de dados SQL único do Azure, ele contém o backup de georestauração restaurado.

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para restaurar geo-um banco de dados de instância gerenciada do portal Azure para uma instância gerenciada existente em uma região de sua escolha, selecione uma instância gerenciada na qual deseja que um banco de dados seja restaurado. Siga estas etapas:

1. Selecione **Novo banco de dados**.
2. Digite um nome de banco de dados desejado.
3. Em **Usar dados existentes,** selecione **Backup**.
4. Selecione um backup na lista de backups de georestauração disponíveis.

    ![Captura de tela de novas opções de banco de dados](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Complete o processo de criação de um novo banco de dados. Quando você cria o banco de dados de instâncias, ele contém o backup de georestauração restaurado.

### <a name="geo-restore-by-using-powershell"></a>Geo-restauração usando PowerShell

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para obter um script PowerShell que mostre como executar a restauração geográfica para um único banco de dados SQL, consulte [Use PowerShell para restaurar um banco de dados único Do Azure SQL em um ponto anterior no tempo](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para obter um script powerShell que mostra como executar a restauração geográfica para um banco de dados de instância saqueado, consulte [Use PowerShell para restaurar um banco de dados de instância sumido para outra região geográfica](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações de georestauração

Você não pode executar uma restauração point-in-time em um banco de dados geosecundário. Você só pode fazê-lo em um banco de dados primário. Para obter informações detalhadas sobre como usar a restauração geográfica para se recuperar de uma interrupção, consulte [Recuperação de uma interrupção](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Geo-restore é a solução de recuperação de desastres mais básica disponível no Banco de Dados SQL. Ele conta com backups georeplicados automaticamente criados com RPO (Recovery Point Objective, objetivo de ponto de recuperação) igual a 1 hora, e o tempo estimado de recuperação de até 12 horas. Isso não garante que a região alvo terá a capacidade de restaurar seus bancos de dados após uma paralisação regional, porque é provável um aumento acentuado da demanda. Se o aplicativo usar bancos de dados relativamente pequenos e não for fundamental para os negócios, a georestauração é uma solução apropriada de recuperação de desastres. Para aplicativos essenciais aos negócios que requerem grandes bancos de dados e devem garantir a continuidade dos negócios, use [grupos de failover automáticos](sql-database-auto-failover-group.md). Ele oferece um RPO muito menor e objetivo de tempo de recuperação, e a capacidade é sempre garantida. Para obter mais informações sobre as opções de continuidade dos negócios, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Executando programáticamente a recuperação usando backups automatizados

Você também pode usar o Azure PowerShell ou a API REST para recuperação. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são em grande medida idênticos.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para restaurar um banco de dados independente ou pooled, consulte [Restaurar-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém um ou mais bancos de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém um banco de dados excluído que você pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém um backup com redundância geográfica de um banco de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura um banco de dados SQL. |

  > [!TIP]
  > Para obter um script de powershell de exemplo que mostra como executar uma restauração point-in-time de um banco de dados, consulte [Restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para restaurar um banco de dados de instância saqueado, consulte [Restaurar-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém uma ou mais instâncias gerenciadas. |
  | [Banco de dados Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtém um banco de dados de instâncias. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura um banco de dados de instâncias. |

### <a name="rest-api"></a>API REST

Para restaurar um banco de dados único ou agrupado usando a API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura um banco de dados. |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](https://docs.microsoft.com/rest/api/sql/operations) |Retorna o status durante uma operação de restauração. |

### <a name="azure-cli"></a>CLI do Azure

#### <a name="single-azure-sql-database"></a>Banco de dados SQL único

Para restaurar um banco de dados único ou agrupado usando o Azure CLI, consulte [a restauração az sql db](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Banco de dados de instâncias gerenciadas

Para restaurar um banco de dados de instância gerenciado usando o Azure CLI, consulte [a restauração az sql midb](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

Backups automáticos protegem seus bancos de dados contra erros de usuário e de aplicativo, exclusão acidental do banco de dados e interrupções prolongadas. Essa funcionalidade interna está disponível para todas as camadas de serviço e tamanhos da computação.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- [Retenção de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-active-geo-replication.md) ou [Grupos de failover automático](sql-database-auto-failover-group.md).
