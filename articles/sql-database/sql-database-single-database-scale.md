---
title: Recursos para dimensionar um banco de dados
description: Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061593"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Escalar recursos de banco de dados individual no Banco de Dados SQL do Azure

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para um Banco de Dados SQL do Azure no nível de computação provisionado. Alternativamente, o [nível de computação sem servidor](sql-database-serverless.md) fornece dimensionamento automático de cálculo e contas por segundo para cálculo usado.

Depois de escolher inicialmente o número de vCores ou DTUs, você pode escalar um único banco de dados para cima ou para baixo dinamicamente com base na experiência real usando o [portal Azure,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [a Azure CLI](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

O vídeo a seguir mostra como alterar dinamicamente o nível de serviço e o tamanho da computação para aumentar os DTUs disponíveis para um banco de dados individual.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

## <a name="impact"></a>Impacto

Alterar o nível de serviço ou o tamanho do cálculo envolve principalmente o serviço executando as seguintes etapas:

1. Criar nova instância de computação para o banco de dados  

    Uma nova instância de computação é criada com o nível de serviço solicitado e o tamanho da computação. Para algumas combinações de alterações de nível de serviço e tamanho de computação, uma réplica do banco de dados deve ser criada na nova instância de computação que envolve copiar dados e pode influenciar fortemente a latência geral. Independentemente disso, o banco de dados permanece on-line durante esta etapa, e as conexões continuam a ser direcionadas para o banco de dados na instância original da computação.

2. Alterne o roteamento de conexões para nova instância de computação

    As conexões existentes ao banco de dados na instância original da computação são descartadas. Quaisquer novas conexões são estabelecidas ao banco de dados na nova instância de computação. Para algumas combinações de alterações de nível de serviço e tamanho de computação, os arquivos do banco de dados são separados e reconectados durante o switch.  Independentemente disso, o switch pode resultar em uma breve interrupção do serviço quando o banco de dados está indisponível geralmente por menos de 30 segundos e muitas vezes por apenas alguns segundos. Se houver transações de longa duração em execução quando as conexões forem retiradas, a duração desta etapa pode levar mais tempo para recuperar transações abortadas. [A recuperação acelerada do banco](sql-database-accelerated-database-recovery.md) de dados pode reduzir o impacto de abortar transações de longo prazo.

> [!IMPORTANT]
> Nenhum dado é perdido durante qualquer etapa do fluxo de trabalho. Certifique-se de que você implementou alguma [lógica de repetição](sql-database-connectivity-issues.md) nos aplicativos e componentes que estão usando o Azure SQL Database enquanto o nível de serviço é alterado.

## <a name="latency"></a>Latency 

A latência estimada para alterar o nível de serviço ou redimensionar o tamanho da computação de um único banco de dados ou pool elástico é parametrizada da seguinte forma:

|Camada de serviço|Banco de dados único básico,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Padrão (S2-S12), </br>Hiperescala, </br>Banco de dados único de Uso Geral ou piscina elástica|Banco de dados único premium ou empresarial crítico ou pool elástico|
|:---|:---|:---|:---|
|**Banco de</br> dados único básico, Padrão (S0-S1)**|&bull;&nbsp;Latência temporal constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica básica, </br>Standard </br>(S2-S12), Hyperscale, </br>General Purpose banco de dados único ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência temporal constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Banco de dados único premium ou empresarial crítico ou pool elástico**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|

> [!TIP]
> Para monitorar as operações em andamento, consulte: [Gerenciar operações usando a API SQL REST,](https://docs.microsoft.com/rest/api/sql/operations/list) [Gerenciar operações usando CLI,](/cli/azure/sql/db/op) [monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Alterações de cancelamento

Uma alteração de nível de serviço ou uma operação de redimensionamento de cálculo podem ser canceladas.

#### <a name="azure-portal"></a>Portal do Azure

Na lâmina de visão geral do banco de dados, navegue até **Notificações** e clique no azulejo indicando que há uma operação em andamento:

![Operação em curso](media/sql-database-single-database-scale/ongoing-operations.png)

Em seguida, clique no botão rotulado **Cancel esta operação**.

![Cancelar a operação em andamento](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

A partir de um prompt `$resourceGroupName` `$serverName`de `$databaseName`comando PowerShell, defina o e execute o seguinte comando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Considerações adicionais

- Se você estiver atualizando para um nível de serviço ou tamanho da computação maior, o tamanho máximo do banco de dados não aumentará, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho da computação de destino.
- Ao fazer o downgrade da camada **Premium** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no tamanho da computação de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de tamanho da computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB é reduzido para S3, então um custo extra de armazenamento se aplica, uma vez que o S3 suporta um tamanho máximo de 1 TB e seu valor de armazenamento incluído é de apenas 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao atualizar para uma edição diferente, é um requisito que o banco de dados secundário seja atualizado primeiro.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao ser rebaixado para uma edição diferente, é um requisito que o banco de dados principal seja rebaixado primeiro.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para camada **Básica**, haverá um período de retenção de backup inferior. Consulte [Backups de Banco de Dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="billing"></a>Cobrança 

Você será cobrado pelas horas em que um banco de dados existir usando a camada de serviço mais alta mais o tamanho da computação aplicado durante essas horas, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

## <a name="change-storage-size"></a>Alterar o tamanho de armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até o limite máximo de tamanho de armazenamento de dados usando incrementos de 1 GB. O armazenamento mínimo de dados configuráveis é de 1 GB. Consulte páginas de documentação de limite de recursos para [bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md) e [pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md) para limites máximos de tamanho de armazenamento de dados em cada objetivo de serviço.
- O armazenamento de dados para um único banco de dados pode ser provisionado aumentando ou diminuindo seu tamanho máximo usando o [portal Azure,](https://portal.azure.com) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)ou [Rest API](https://docs.microsoft.com/rest/api/sql/databases/update). Se o valor de tamanho máximo for especificado em bytes, deve ser um múltiplo de 1 GB (1073741824 bytes).
- A quantidade de dados que podem ser armazenados nos arquivos de dados de um banco de dados é limitada pelo tamanho máximo de armazenamento de dados configurado. Além desse armazenamento, o SQL Database aloca automaticamente 30% mais armazenamento para ser usado no registro de transações.
- O Banco de Dados SQL aloca automaticamente `tempdb` 32 GB por vCore para o banco de dados. `tempdb`está localizado no armazenamento SSD local em todos os níveis de serviço.
- O preço do armazenamento para um único banco de dados ou um pool elástico é a soma dos valores de armazenamento de armazenamento de dados e registros de transações multiplicados pelo preço unitário de armazenamento do nível de serviço. O custo `tempdb` está incluído no preço. Para obter detalhes sobre o preço de armazenamento, consulte [os preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer as quantidades de armazenamento incluídas e os limites máximos de tamanho, confira [Banco de dados individual: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- O armazenamento extra para um único banco de dados pode ser provisionado aumentando seu tamanho máximo usando o portal Azure, [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [a Cli Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

### <a name="geo-replicated-database"></a>Banco de dados geo-replicado

Para alterar o tamanho do banco de dados de um banco de dados secundário replicado, altere o tamanho do banco de dados principal. Essa alteração também será replicada e implementada no banco de dados secundário. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições P11 e P15 quando tamanho máximo maior que 1 TB

Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regiões e US Government Central. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se o tamanho máximo de um banco de dados P11 ou P15 foi definido para um valor superior a 1 TB, então ele só pode ser restaurado ou copiado para um banco de dados P11 ou P15.  Posteriormente, o banco de dados pode ser redimensionado para um tamanho de computação diferente, desde que a quantidade de espaço alocado no momento da operação de redimensionamento não exceda os limites máximos de tamanho do novo tamanho da computação.
- Para cenários com replicação geográfica ativa:
  - Configurar uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15; os tamanhos da computação inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não oferece suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

Para saber os limites gerais de recursos, confira [Limites de recurso baseados em vCore do Banco de Dados SQL – bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso baseados em DTU do Banco de Dados SQL – pools elásticos](sql-database-dtu-resource-limits-single-databases.md).
