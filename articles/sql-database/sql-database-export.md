---
title: Exportar um banco de dados SQL do Azure individual ou em pool para um arquivo BACPAC
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: f3f6071d42d77ffa07dd27080b1bc18d7bbc6952
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690075"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC

Quando for preciso exportar um banco de dados para arquivamento ou para mover para outra plataforma, você pode exportar os dados e o esquema do banco de dados para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Um arquivo BACPAC é um arquivo ZIP com uma extensão BACPAC que contém os metadados e dados de um banco de dados do SQL Server. Um arquivo BACPAC pode ser colocado no Armazenamento de Blobs do Azure ou em uma determinada localização no armazenamento local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerações ao exportar um banco de dados SQL do Azure

- Para que uma exportação seja transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados SQL do Azure.
- Se você estiver exportando para o armazenamento de blobs, o tamanho máximo de um arquivo BACPAC é de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
- Não há suporte para a exportação de um arquivo BACPAC no armazenamento Premium do Azure usando os métodos abordados neste artigo.
- No momento, não há suporte para o armazenamento por trás de um firewall.
- Se a operação de exportação do Banco de Dados SQL do Azure exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:

  - Aumente temporariamente o tamanho da computação.
  - Interromper toda a atividade de leitura e gravação durante a exportação.
  - Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, confira a [visão geral da continuidade dos negócios](sql-database-business-continuity.md) e os [backups do Banco de Dados SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportar para um arquivo BACPAC usando o portal do Azure

Atualmente, não há suporte para a exportação de um BACPAC de um banco de dados de uma [instância gerenciada](sql-database-managed-instance.md) usando o Azure PowerShell. Em vez disso, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir espaço em disco de até 3 vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Incentivamos o uso do [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) para importar/exportar bancos de dados maiores que 150 GB para evitar esse problema.

1. Para exportar um banco de dados usando o [Portal do Azure](https://portal.azure.com), abra a página do banco de dados e clique em **Exportar** na barra de ferramentas.

   ![Exportação de banco de dados](./media/sql-database-export/database-export1.png)

2. Especifique o nome do arquivo BACPAC, selecione uma conta de armazenamento do Azure existente e um contêiner para a exportação, em seguida, forneça as credenciais apropriadas para acessar o banco de dados de origem. Um **logon de administrador** do SQL Server é necessário aqui mesmo se você for o administrador do Azure, pois ser um administrador do Azure não equivale a ter permissões de administrador de SQL Server.

    ![Exportação de banco de dados](./media/sql-database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorar o progresso da operação de exportação, abra a página para o servidor do Banco de Dados SQL que contém o banco de dados que está sendo exportado. Em **Configurações**, clique em **Histórico de importação/exportação**.

   ![histórico de exportação](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportar para um arquivo BACPAC usando o utilitário SQLPackage

Para exportar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consulte [Exportar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SQLPackage acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Recomendamos o uso do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar um banco de dados usando SqlPackage.exe com Autenticação Universal do Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportar para um arquivo BACPAC usando o SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornecem um assistente para exportar um banco de dados SQL do Azure para um arquivo BACPAC. Consulte [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportar para um arquivo BACPAC usando o PowerShell

> [!NOTE]
> [Uma instância gerenciada](sql-database-managed-instance.md) não dá suporte atualmente à exportação de um banco de dados para um arquivo BACPAC usando o Azure PowerShell. Para exportar uma instância gerenciada para um arquivo BACPAC, use o SQL Server Management Studio ou o SQLPackage.

Use o cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) para enviar uma solicitação de exportação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o status da solicitação de exportação, use o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Executar isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: Êxito**, a exportação estará concluída.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a retenção do backup de longo prazo de um único banco de dados e em pool como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção do backup de longo prazo](sql-database-long-term-retention.md). Você pode usar os trabalhos do SQL Agent para agendar [backups do banco de dados de somente cópia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa à retenção do backup de longo prazo.
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Para saber mais sobre como exportar um BACPAC de um banco de dados do SQL Server, veja [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para saber mais sobre como usar o serviço de migração de dados para migrar um banco de dados, veja [Migrar o SQL Server para o banco de dados SQL do Azure offline usando o DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Se você estiver exportando do SQL Server como um prelúdio para a migração para o Banco de Dados SQL do Azure, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
