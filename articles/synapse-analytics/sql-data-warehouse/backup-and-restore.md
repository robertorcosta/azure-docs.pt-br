---
title: Backup e restauração - instantâneos, geo-redundantes
description: Saiba como funciona o backup e a restauração no pool SQL do Azure Synapse Analytics. Use backups para restaurar seu data warehouse em um ponto de restauração na região principal. Use backups com redundância geográfica para restaurar para uma região geográfica diferente.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 1d82c7c22bb5aeb2740884b0d7ede4a4d8f07f86
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631212"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Backup e restauração no pool Synapse SqL do Azure Synapse

Aprenda a usar backup e restauração no pool Synapse Synapse Azure. Use pontos de restauração de pool SQL para recuperar ou copiar seu data warehouse para um estado anterior na região primária. Use backups de data warehouse com redundância geográfica para restaurar para uma região geográfica diferente.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo de data warehouse

Um *instantâneo de data warehouse* cria um ponto de restauração que pode ser aproveitado para recuperar ou copiar seu data warehouse para um estado anterior.  Como o pool SQL é um sistema distribuído, um instantâneo de data warehouse consiste em muitos arquivos que estão localizados no armazenamento Do Zure. Os instantâneos capturam as alterações incrementais dos dados armazenados no data warehouse.

A *restauração de um data warehouse* é um novo data warehouse criado por meio de um ponto de restauração de um data warehouse existente ou excluído. Restaurar o seu banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e de continuidade dos negócios, porque ela recria seus dados após uma exclusão ou corrupção acidental. O data warehouse também é um mecanismo eficiente para criar cópias do seu data warehouse para fins de teste ou desenvolvimento.  As taxas de restauração do pool SQL podem variar dependendo do tamanho do banco de dados e localização da fonte e do data warehouse de destino.

## <a name="automatic-restore-points"></a>Pontos de restauração automática

Os snapshots são um recurso incorporado do serviço que cria pontos de restauração. Não é necessário habilitar essa funcionalidade. No entanto, o pool SQL deve estar em um estado ativo para restaurar a criação de pontos. Se o pool SQL for pausado com freqüência, os pontos de restauração automáticos podem não ser criados, então certifique-se de criar um ponto de restauração definido pelo usuário antes de pausar o pool SQL. Os pontos de restauração automáticos atualmente não podem ser excluídos pelos usuários, pois o serviço usa esses pontos de restauração para manter Os SLAs para recuperação.

Instantâneos do seu data warehouse são feitos ao longo do dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL suporta um RPO (Recovery Point Objective, objetivo de ponto de recuperação de oito horas). É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo foi iniciado, execute esta consulta no seu pool SQL on-line.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Esse recurso permite que você acione manualmente instantâneos para criar pontos de restauração do seu data warehouse antes e depois de grandes modificações. Esse recurso garante que os pontos de restauração sejam logicamente consistentes, o que fornece proteção adicional de dados em caso de interrupções de carga de trabalho ou erros do usuário para um tempo de recuperação rápido. Os pontos de restauração definidos pelo usuário ficam disponíveis por sete dias e são excluídos automaticamente em seu nome. Não é possível alterar o período de retenção de pontos de restauração definidos pelo usuário. Garantimos apenas **42 pontos de restauração definidos pelo usuário** a qualquer momento; é necessário [excluir](https://go.microsoft.com/fwlink/?linkid=875299) algum ponto antes da criação de outro ponto de restauração. É possível disparar instantâneos para criar pontos de restauração definidos pelo usuário por meio do [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) ou do Portal do Azure.

> [!NOTE]
> Caso precise de pontos de restauração de mais de 7 dias, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse. Depois de restaurado, você tem o pool SQL on-line e pode pausa-lo indefinidamente para economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. Se você precisar de uma cópia ativa do data warehouse restaurado, poderá retomar, o que deve levar apenas alguns minutos.

### <a name="restore-point-retention"></a>Retenção do ponto de restauração

As listas a seguir de detalhes para restaurar períodos de retenção de pontos:

1. O pool SQL exclui um ponto de restauração quando atinge o período de retenção de 7 dias **e** quando há pelo menos 42 pontos de restauração totais (incluindo definidos pelo usuário e automáticos).
2. Os instantâneos não são feitos quando uma piscina SQL é pausada.
3. A idade de um ponto de restauração é medida pelos dias de calendário absolutos a partir do momento em que o ponto de restauração é tomado, incluindo quando o pool SQL é pausado.
4. A qualquer momento, um pool SQL é garantido para ser capaz de armazenar até 42 pontos de restauração definidos pelo usuário e 42 pontos de restauração automáticos, desde que esses pontos de restauração não tenham atingido o período de retenção de 7 dias
5. Se um instantâneo for tirado, o pool SQL será pausado por mais de 7 dias e, em seguida, será retomado, o ponto de restauração persistirá até que haja 42 pontos de restauração totais (incluindo definidos pelo usuário e automáticos)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Retenção de instantâneoquando um pool SQL é descartado

Quando você solta um pool SQL, um instantâneo final é criado e salvo por sete dias. Você pode restaurar o pool SQL até o ponto de restauração final criado na exclusão. Se a piscina SQL for lançada em um estado pausado, nenhuma foto será tirada. Nesse cenário, certifique-se de criar um ponto de restauração definido pelo usuário antes de deixar cair o pool SQL.

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencerem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

## <a name="geo-backups-and-disaster-recovery"></a>Backups geográficos e recuperação de desastre

Um backup geográfico é criado uma vez por dia para um [data center emparelhado](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). O RPO de uma restauração geográfica é de 24 horas. Você pode restaurar o backup geográfico em um servidor em qualquer outra região onde o pool SQL é suportado. Um backup geográfico garante que você possa restaurar um data warehouse caso não seja possível acessar os pontos de restauração em sua região primária.

> [!NOTE]
> Se precisar de um RPO mais curto para backups geográficos, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse em uma região diferente. Após a restauração, seu data warehouse estará online e será possível pausá-lo indefinidamente para em economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. Caso você precise de uma cópia ativa do data warehouse, poderá retomar, o que deve levar apenas alguns minutos.

## <a name="backup-and-restore-costs"></a>Custos de backup e restauração

Você observará que a fatura do Azure tem um item de linha para Armazenamento e um item de linha para Armazenamento para Recuperação de Desastre. A carga de armazenamento é o custo total para armazenar seus dados na região primária, juntamente com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são cobrados, veja [Understanding how Snapshots Accrue Charges](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Noções básicas sobre como os instantâneos acumulam cobranças). O encargo com redundância geográfica abrange o custo para armazenar os backups geográficos.  

O custo total para o data warehouse primário e de sete dias de alterações de instantâneos é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos capturam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure.

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do Azure Synapse, consulte [os preços da Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Você não é cobrado pela saída de dados ao restaurar em todas as regiões.

## <a name="restoring-from-restore-points"></a>Restauração a partir dos pontos de restauração

Cada instantâneo cria um ponto de restauração que representa a hora em que o instantâneo é iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

É possível manter o data warehouse restaurado e o atual ou excluir um deles. Se você quiser substituir o data warehouse atual pelo data warehouse restaurado, você pode renomeá-lo usando [o grupo ALTER DATABASE (SQL pool)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) com a opção MODIFY NAME.

Para restaurar um data warehouse, consulte [Restaurar um pool SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Para restaurar um data warehouse excluído ou em pausa, será possível [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Restauração de assinatura cruzada

Se você precisar restaurar diretamente através da assinatura, vote neste recurso [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaure um servidor lógico diferente e ['Mova'](/azure/azure-resource-manager/resource-group-move-resources?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o servidor através de assinaturas para executar uma restauração de assinatura cruzada.

## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica

Você pode [restaurar seu pool SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) em qualquer região que suporte o pool SQL no nível de desempenho escolhido.

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre planejamento de desastre, consulte [Visão geral sobre a continuidade dos negócios](../../sql-database/sql-database-business-continuity.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
