---
title: 'Tutorial: replicação geográfica & failover no portal'
description: Configure a replicação geográfica para um banco de dados usando o portal do Azure e inicie o failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 1beb2065f1823135981545e42d499c5429b87c0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84031607"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Tutorial: configurar a replicação geográfica ativa e o failover no portal do Azure (banco de dados SQL do Azure)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra como configurar a [replicação geográfica ativa para o banco de dados SQL do Azure](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) usando o [portal do Azure](https://portal.azure.com) e para iniciar o failover.

Para obter as práticas recomendadas usando grupos de failover automático, consulte [práticas recomendadas para o banco de dados SQL do Azure](auto-failover-group-overview.md#best-practices-for-sql-database) e [práticas recomendadas para o Azure SQL instância gerenciada](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação geográfica ativa usando o Portal do Azure, você precisa do seguinte recurso:

* Um banco de dados no banco de dados SQL do Azure: o banco de dados primário que você deseja replicar para uma região geográfica diferente.

> [!Note]
> Ao usar o portal do Azure, você só pode criar um banco de dados secundário na mesma assinatura que o primário. Se um banco de dados secundário precisar estar em uma assinatura diferente, use [CREATE DATABASE REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) ou [ALTER DATABASE TRANSACT-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Adicionar um banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um banco de dados secundário, você deve ser o proprietário ou coproprietário da assinatura.

O banco de dados secundário tem o mesmo nome do banco de dados primário e, por padrão, tem a mesma camada de serviço e tamanho da computação. O banco de dados secundário pode ser um banco de dados individual ou um banco de dados em pool. Para obter mais informações, consulte [Modelo de compra baseado em DTU](service-tiers-dtu.md) e [Modelo de compra baseado em vCore](service-tiers-vcore.md).
Depois que o banco de dados secundário for criado e propagado, os dados começarão a serem replicados desde o banco de dados primário até o novo banco de dados secundário.

> [!NOTE]
> O comando falhará se o banco de dados do parceiro já existir (por exemplo, como resultado do término de uma relação de replicação geográfica anterior).

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados que você deseja configurar para a replicação geográfica.
2. Na página banco de dados SQL, selecione **replicação geográfica**e, em seguida, selecione a região para criar o banco de dados secundário. É possível selecionar qualquer região além da região que hospeda o banco de dados primário, mas recomendamos a [região emparelhada](../../best-practices-availability-paired-regions.md).

    ![Configurar a replicação geográfica](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o tipo de preço do banco de dados secundário.

    ![Configurar secundário](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Opcionalmente, você pode adicionar um banco de dados secundário a um pool elástico. Para criar o banco de dados secundário em um pool, clique em **pool elástico** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino. Esse fluxo de trabalho não cria um pool.
5. Clique em **Criar** para adicionar o secundário.
6. O banco de dados secundário é criado e começa o processo de propagação.

    ![Configurar secundário](./media/active-geo-replication-configure-portal/seeding0.png)
7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status.

    ![Propagação concluída](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar primário.  

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na folha banco de dados SQL, selecione **todas as configurações**  >  **replicação geográfica**.
3. Na lista **secundários** , selecione o banco de dados que você deseja tornar o novo primário e clique em **failover forçado**.

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Clique em **Sim** para iniciar o failover.

O comando mudará imediatamente o banco de dados secundário para a função primária. Esse processo normalmente deve ser concluído dentro de 30 segundos ou menos.

Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectarem ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais.

> [!NOTE]
> Este comando destina-se à recuperação rápida do banco de dados em caso de interrupção. Ele dispara o failover sem sincronização de dados (failover forçado).  Se o primário estiver online e realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.

## <a name="remove-secondary-database"></a>Remover um banco de dados secundário

Essa operação termina permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for desfeita, o comando terá êxito, mas o secundário só se tornará de leitura/gravação quando a conectividade for restaurada.  

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na página banco de dados SQL, selecione **replicação geográfica**.
3. Na lista **secundários** , selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar Replicação**.

    ![Remover secundário](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Uma janela de confirmação é aberta. Clique em **Sim** para remover o banco de dados da parceria de replicação geográfica. (Defina-o como um banco de dados de leitura/gravação que não faz parte de nenhuma replicação.)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a replicação geográfica ativa, consulte [replicação geográfica ativa](active-geo-replication-overview.md).
* Para saber mais sobre grupos de failover automático, confira [Grupos de failover automático](auto-failover-group-overview.md)
* Para obter uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)
