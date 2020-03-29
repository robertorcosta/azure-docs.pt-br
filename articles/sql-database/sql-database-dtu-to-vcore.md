---
title: Migrar do DTU para o vCore
description: Migre do modelo DTU para o modelo vCore. Migrar para o vCore é semelhante à atualização ou rebaixamento entre os níveis padrão e premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945410"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migre do modelo baseado em DTU para o modelo baseado em vCore

## <a name="migrate-a-database"></a>Migrar um banco de dados

A migração de um banco de dados do modelo de compras baseado em DTU para o modelo de compras baseado em vCore é semelhante à atualização ou rebaixamento entre os níveis de serviço padrão e premium no modelo de compras baseado em DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrar bancos de dados que usam geo-replicação

Migrar do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou redução das relações de georeplicação entre bancos de dados nos níveis de serviço padrão e premium. Durante a migração, você não precisa parar a replicação geográfica, mas deve seguir essas regras de seqüenciamento:

- Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário.
- Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário.

Quando você estiver usando a geo-replicação entre duas piscinas elásticas, recomendamos que você designe uma piscina como principal e a outra como secundária. Nesse caso, quando você está migrando piscinas elásticas, você deve usar a mesma orientação de seqüência. No entanto, se você tiver piscinas elásticas que contenham bancos de dados primários e secundários, trate o pool com maior utilização como principal e siga as regras de seqüenciamento de acordo.  

A tabela a seguir fornece orientações para cenários de migração específicos:

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Propósito geral|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Premium|Comercialmente crítico|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Standard|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Standard|Downgrade|Deve migrar primeiro o primário|
|Premium|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Premium|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
||||

\*Como regra geral, cada 100 DTUs no nível padrão exigem pelo menos 1 vCore, e cada 125 DTUs no nível premium exigem pelo menos 1 vCore. Para saber mais, confira [Modelo de compra baseado em vCore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrar grupos failover

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados forem convertidos para o modelo de compra baseado em vCore, o grupo failover permanecerá em vigor com as mesmas configurações de diretiva.

### <a name="create-a-geo-replication-secondary-database"></a>Criar um banco de dados secundário de replicação geográfica

Você pode criar um banco de dados secundário de georeplicação (um geosecundário) usando apenas o mesmo nível de serviço que você usou para o banco de dados principal. Para bancos de dados com uma alta taxa de geração de log, recomendamos criar o geosecundário com o mesmo tamanho de computação que o principal.

Se você estiver criando um geosecundário no pool elástico para um `maxVCore` único banco de dados primário, certifique-se de que a configuração para o pool corresponda ao tamanho da computação do banco de dados principal. Se você está criando um geosecundário para um primário em outra piscina elástica, recomendamos que as piscinas tenham as mesmas `maxVCore` configurações.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Use cópia de banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore

É possível copiar qualquer banco de dados com um tamanho da computação baseado em DTU para um banco de dados com um tamanho da computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho da computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia do banco de dados cria um instantâneo dos dados a partir do momento inicial da operação de cópia e não sincroniza dados entre a origem e o destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados únicos, consulte [os limites de recursos baseados no SQL Database vCore para bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md).
- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados no SQL Database vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).
