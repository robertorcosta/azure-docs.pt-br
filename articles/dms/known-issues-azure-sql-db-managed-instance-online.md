---
title: Problemas e limitações conhecidas com migrações on-line para a instância gerenciada do Azure SQL Database
description: Saiba mais sobre problemas conhecidos/limitações de migração associadas a migrações on-line para a instância gerenciada do Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648658"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Problemas/limitações de migração conhecidas com migrações on-line para a instância gerenciada do Azure SQL Database

Problemas e limitações conhecidos que estão associados a migrações on-line do SQL Server para a instância gerenciada do Azure SQL Database são descritos abaixo.

> [!IMPORTANT]
> Com migrações on-line do SQL Server para o Azure SQL Database, a migração de SQL_variant tipos de dados não é suportada.

## <a name="backup-requirements"></a>Requisitos de backup

- **Backups com cheques**

    O Azure Database Migration Service usa o método de backup e restauração para migrar seus bancos de dados locais para a instância gerenciada do Banco de Dados SQL. O Azure Database Migration Service só suporta backups criados usando a soma de verificação.

    [Habilite ou desative as verificações de backup durante o backup ou restauração (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Se você pegar os backups do banco de dados com compactação, a soma de verificação será um comportamento padrão, a menos que seja explicitamente desativado.

    Com migrações offline, se você escolher **eu permitirei que o Azure Database Migration Service...**, então o Azure Database Migration Service fará o backup do banco de dados com a opção de soma de cheques ativada.

- **Mídia de backup**

    Certifique-se de fazer backup em uma mídia de backup separada (arquivos de backup). O Azure Database Migration Service não suporta backups anexados a um único arquivo de backup. Faça backup completo e faça backups para separar arquivos de backup.

## <a name="data-and-log-file-layout"></a>Layout de arquivos de dados e log

- **Número de arquivos de log**

    O Azure Database Migration Service não suporta bancos de dados com vários arquivos de log. Se você tiver vários arquivos de log, reduza e reorganize-os em um único arquivo de registro de transações. Como você não pode registrar arquivos que não estão vazios, você precisa fazer backup do arquivo de registro primeiro.

## <a name="sql-server-features"></a>recursos do SQL Server

- **FileStream/FileTables**

    A instância gerenciada do Banco de Dados SQL atualmente não suporta FileStream e FileTables. Para cargas de trabalho dependentes desses recursos, recomendamos que você opte por servidores SQL em execução em VMs Azure como seu alvo Azure.

- **Tabelas na memória**

    O OLTP na memória está disponível nos níveis Premium e Business Critical para instância gerenciada do Banco de Dados SQL; o nível De propósito geral não suporta OLTP de memória in-memory.

## <a name="migration-resets"></a>Redefinições de migração

- **Implantações**

    A instância gerenciada do Banco de Dados SQL é um serviço PaaS com patches automáticos e atualizações de versão. Durante a migração da instância gerenciada do Banco de Dados SQL, atualizações não críticas são ajuda até 36 horas. Posteriormente (e para atualizações críticas), se a migração for interrompida, o processo será reconfigurado para um estado de restauração completo.

    O cutover de migração só pode ser chamado depois que o backup completo for restaurado e acompanhar todos os backups de log. Se os cortes de migração de produção forem afetados, entre em contato com o [alias Azure DMS Feedback](mailto:dmsfeedback@microsoft.com).
