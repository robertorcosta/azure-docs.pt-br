---
title: Retenção de backup de longo prazo
description: Saiba como o Banco de Dados SQL do Azure dá suporte ao armazenamento de backups completos do banco de dados por até 10 anos por meio da política de retenção de longo prazo.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 6fc5fab9ae61c8c8ade9260b32078ffa430b077f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771393"
---
# <a name="azure-sql-database-long-term-retention"></a>Retenção de longo prazo do Banco de Dados SQL do Azure

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Azure. Usando o recurso LTR (retenção de longo prazo), é possível armazenar backups completos especificados do Banco de Dados SQL no armazenamento de blobs com armazenamento com redundância geográfica com acesso à leitura por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados. Para obter mais informações sobre a redundância do Armazenamento do Microsoft Azure, confira [Redundância no Armazenamento do Azure](../storage/common/storage-redundancy.md). 

A retenção de longo prazo pode ser habilitada para bancos de dados únicos e em pool e está em visualização pública limitada para instâncias gerenciadas do Banco de Dados SQL do Azure. 

> [!NOTE]
> Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.


## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](sql-database-automated-backups.md) para habilitar a restauração pontual (PITR). Se uma política de LTR for configurada, esses backups serão copiados para blobs diferentes para armazenamento de longo prazo. A cópia é um trabalho em segundo plano que não afeta o desempenho na carga de trabalho do banco de dados. A política de LTR de cada banco de dados SQL também pode especificar com que frequência os backups EPD são criados.

Para permitir a LTR, você pode definir uma política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, o primeiro backup de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Se a WeekOfYear especificada estiver no passado quando a política for configurada, o primeiro backup LTR será criado no ano seguinte. Cada backup será mantido no armazenamento de longo prazo de acordo com os parâmetros de política configurados quando o backup LTR é criado.

> [!NOTE]
> Qualquer alteração na política de LTR se aplica somente a backups futuros. Por exemplo, se a retenção de backup semanal (W), retenção de backup mensal (M) ou retenção de backup anual (Y) for modificada, a nova configuração de retenção será aplicada somente a novos backups. A retenção de backups existentes não será modificada. Se sua intenção for excluir backups LTR antigos antes do término do período de retenção, será necessário [excluir manualmente os backups](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exemplos da política de LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O 3º backup completo de cada ano será mantido por 5 anos.
   
- W=0, M=3, Y=0

   O 1º backup completo de cada mês será mantido por 3 meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por 6 semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)



Se você modificar a política acima e definir W=0 (sem backups semanais), a cadência das cópias de backup mudará, conforme mostrado na tabela acima pelas datas destacadas. A as quantidade de armazenamento necessária para manter esses backups reduziria adequadamente. 

> [!IMPORTANT]
> O tempo dos backups LTR individuais é controlado pelo Banco de Dados SQL do Microsoft Azure. Não é possível criar manualmente um backup LTR ou controlar o tempo de criação do backup. Depois de configurar uma política de LTR, pode levar até 7 dias para que o primeiro backup LTR apareça na lista de backups disponíveis.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se estiver usando grupos de failover ou de replicação geográfica ativos como a solução de continuidade de negócios, prepare-se para eventuais failovers e configure a mesma política de LTR no banco de dados geográfico secundário. O custo do seu armazenamento LTR não aumentará, pois os backups não serão gerados a partir dos secundários. Somente quando o secundário tornar-se primário, os backups serão criados. Isso garante a geração ininterrupta de backups de LTR quando o failover for acionado e o primário for movido para a região secundária. 

> [!NOTE]
> Quando o banco de dados primário original se recuperar da indisponibilidade que causou o failover, ele se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 

## <a name="managed-instance-support"></a>Suporte à Instância Gerenciada

O uso da retenção de backup de longo prazo com instâncias gerenciadas do Banco de Dados SQL do Azure tem as seguintes limitações:

- **Visualização pública limitada** - Essa visualização só está disponível para assinaturas EA e CSP e está sujeita à disponibilidade limitada.  
- [**Somente PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) - Não há atualmente nenhum suporte para o portal do Azure. No entanto, o LTR pode ser habilitado com o PowerShell. 

Para solicitar a inscrição, crie um [tíquete de Suporte do Azure](https://azure.microsoft.com/support/create-ticket/). Para tipo de problema, selecione problema técnico. Para serviço, escolha Instância Gerenciada do Banco de Dados SQL. Para tipo de problema, selecione **Backup, restauração e continuidade de negócios/retenção de backup de longo prazo**. Em sua solicitação, informe que você gostaria de ser inscrito na visualização pública limitada do LTR para a instância gerenciada.

## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção de longo prazo usando o portal do Azure ou o PowerShell, consulte [Gerenciar a retenção de longo prazo de backup do Banco de Dados SQL do Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurar banco de dados a partir do backup de LTR

Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. Para saber como restaurar seu banco de dados a partir de um backup de LTR, usando o portal do Azure ou o PowerShell, consulte [Gerenciar a retenção de longo prazo de backup do Banco de Dados SQL do Azure](sql-database-long-term-backup-retention-configure.md). Em sua solicitação, informe que você gostaria de ser inscrito na visualização pública limitada do LTR para a instância gerenciada.

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
