---
title: Excluir uma sub-rede depois de excluir uma instância gerenciada do SQL Instância Gerenciada
description: Saiba como excluir uma rede virtual do Azure depois de excluir uma instância gerenciada do Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 87e73ed25ac33777c19c561223f0361d589282b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696993"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Excluir uma sub-rede depois de excluir uma instância gerenciada do SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece diretrizes sobre como excluir uma sub-rede manualmente depois de excluir a última instância gerenciada do Azure SQL Instância Gerenciada que a reside.

As instâncias gerenciadas são implantadas em [clusters virtuais](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Cada cluster virtual é associado a uma sub-rede. O cluster virtual persiste por design por 12 horas após a última exclusão da instância para permitir que você crie com mais rapidez as instâncias gerenciadas na mesma sub-rede. Não há nenhum encargo para manter um cluster virtual vazio. Durante esse período, a sub-rede associada ao cluster virtual não pode ser excluída.

Se você não quiser aguardar 12 horas e preferir excluir o cluster virtual e sua sub-rede mais cedo, poderá fazer isso manualmente. Exclua o cluster virtual manualmente usando a portal do Azure ou a API de clusters virtuais.

> [!IMPORTANT]
> - O cluster virtual não deve conter nenhuma instância gerenciada para que a exclusão seja bem-sucedida. 
> - A exclusão de um cluster virtual é uma operação de execução longa duradoura por cerca de 1,5 horas (consulte [operações de gerenciamento de instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) para tempo de exclusão de cluster virtual atualizado). O cluster virtual ainda estará visível no portal até que esse processo seja concluído.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Excluir um cluster virtual do portal do Azure

Para excluir um cluster virtual usando o portal do Azure, procure os recursos do cluster virtual.

![Captura de tela da portal do Azure, com a caixa de pesquisa realçada](./media/virtual-cluster-delete/virtual-clusters-search.png)

Depois de localizar o cluster virtual que você deseja excluir, selecione esse recurso e selecione **excluir**. Você será solicitado a confirmar a exclusão do cluster virtual.

![Captura de tela do painel portal do Azure clusters virtuais, com a opção Excluir realçada](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Portal do Azure notificações mostrarão uma confirmação de que a solicitação para excluir o cluster virtual foi enviada com êxito. A própria operação de exclusão durará por cerca de 1,5 horas, durante as quais o cluster virtual ainda estará visível no Portal. Quando o processo for concluído, o cluster virtual não ficará mais visível e a sub-rede associada a ele será liberada para reutilização.

> [!TIP]
> Se não houver nenhuma instância gerenciada mostrada no cluster virtual e você não puder excluir o cluster virtual, certifique-se de que você não tenha uma implantação de instância em andamento. Isso inclui implantações iniciadas e canceladas que ainda estão em andamento. Isso ocorre porque essas operações ainda usarão o cluster virtual, bloqueando-o da exclusão. Revisando a guia **implantações** do grupo de recursos em que a instância foi implantada indicará todas as implantações em andamento. Nesse caso, aguarde a conclusão da implantação, exclua a instância gerenciada e, em seguida, exclua o cluster virtual.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Excluir um cluster virtual usando a API

Para excluir um cluster virtual por meio da API, use os parâmetros de URI especificados no [método de exclusão de clusters virtuais](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Saiba mais sobre a [arquitetura de conectividade no SQL instância gerenciada](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para o SQL instância gerenciada](vnet-existing-add-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para problemas de DNS, consulte [configurar um DNS personalizado](custom-dns-configure.md).
