---
title: Retenção de backup de longo prazo
description: Saiba como o Azure SQL Database suporta armazenar backups completos do banco de dados por até 10 anos através da política de retenção de longo prazo.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380752"
---
# <a name="azure-sql-database-long-term-retention"></a>Retenção de longo prazo do Banco de Dados SQL do Azure

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Azure. Usando o recurso de retenção de longo prazo (LTR), você pode armazenar backups completos do banco de dados SQL especificados no armazenamento Azure Blob com armazenamento geo-redundante de acesso de leitura por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados. Para obter mais informações sobre a redundância do Azure Storage, consulte [a redundância do Azure Storage](../storage/common/storage-redundancy.md). 

A retenção por tempo longo pode ser habilitada para bancos de dados únicos e agrupados e está em uma pré-visualização pública limitada para instâncias gerenciadas pelo Azure SQL Database. 

> [!NOTE]
> Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.


## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](sql-database-automated-backups.md) para habilitar a restauração pontual (PITR). Se uma diretiva LTR estiver configurada, esses backups serão copiados para diferentes blobs para armazenamento a longo prazo. A cópia é um trabalho de fundo que não tem impacto de desempenho na carga de trabalho do banco de dados. A política LTR para cada banco de dados SQL também pode especificar com que freqüência os backups LTR são criados.

Para habilitar o LTR, você pode definir uma política usando uma combinação de quatro parâmetros: retenção semanal de backup (W), retenção mensal de backup (M), retenção anual de backup (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, o primeiro backup de cada mês será copiado para o armazenamento a longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Se a SemanaOfYear especificada estiver no passado quando a diretiva estiver configurada, o primeiro backup LTR será criado no ano seguinte. Cada backup será mantido no armazenamento a longo prazo de acordo com os parâmetros de diretiva configurados quando o backup LTR é criado.

> [!NOTE]
> Qualquer alteração na política LTR se aplica apenas a backups futuros. Por exemplo, se a retenção semanal de backup (W), a retenção mensal de backup (M) ou a retenção anual de backup (Y) for modificada, a nova configuração de retenção só se aplicará a novos backups. A retenção dos backups existentes não será modificada. Se sua intenção é excluir backups LTR antigos antes que seu período de retenção expire, você precisará [excluir manualmente os backups](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exemplos da política LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O terceiro backup completo de cada ano será mantido por cinco anos.
   
- W=0, M=3, Y=0

   O primeiro backup completo de cada mês será mantido por três meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por seis semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)



Se você modificar a diretiva acima e definir W=0 (sem backups semanais), a cadência das cópias de backup mudará conforme mostrado na tabela acima pelas datas destacadas. A as quantidade de armazenamento necessária para manter esses backups reduziria adequadamente. 

> [!IMPORTANT]
> O tempo dos backups LTR individuais é controlado pelo Banco de Dados SQL do Azure. Não é possível criar manualmente um backup LTR ou controlar o tempo de criação do backup. Depois de configurar uma diretiva LTR, pode levar até 7 dias até que o primeiro backup da LTR apareça na lista de backups disponíveis.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se você estiver usando grupos ativos de georeplicação ou failover como sua solução de continuidade de negócios, você deve se preparar para eventuais failovers e configurar a mesma política de LTR no banco de dados geosecundário. O custo de armazenamento do LTR não aumentará, pois os backups não são gerados a partir dos secundários. Somente quando o secundário tornar-se primário, os backups serão criados. Ele garante a geração não interrompida dos backups LTR quando o failover é acionado e o principal se move para a região secundária. 

> [!NOTE]
> Quando o banco de dados principal original se recuperar de uma paralisação que causou o failover, ele se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 

## <a name="managed-instance-support"></a>Suporte à Instância Gerenciada

O uso de retenção de backup a longo prazo com um banco de dados SQL do Azure tem as seguintes limitações:

- **Pré-visualização limitada do público** - Esta pré-visualização está disponível apenas para assinaturas EA e CSP e está sujeita a disponibilidade limitada.  
- [**Somente PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) - Atualmente não há suporte ao portal Azure. A LTR deve ser habilitada usando o PowerShell. 

Para solicitar a inscrição, crie um [ticket de suporte do Azure](https://azure.microsoft.com/support/create-ticket/) sob o tópico de suporte **Backup, Restauração e continuidade de negócios / retenção de backup a longo prazo**.


## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção a longo prazo usando o portal Azure ou o PowerShell, consulte Gerenciar a [retenção de backup a longo prazo do Banco de Dados SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurar banco de dados do backup ltr

Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. Para saber como restaurar seu banco de dados a partir de um backup LTR, usando o portal Azure ou PowerShell, consulte [Gerenciar a retenção de backup a longo prazo do Banco de Dados SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
