---
title: Exclua uma sub-rede depois de excluir uma instância gerenciada
description: Saiba como excluir uma rede virtual do Azure depois de excluir uma instância gerenciada do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820457"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Exclua uma sub-rede depois de excluir uma instância gerenciada do Azure SQL Database

Este artigo fornece orientações sobre como excluir manualmente uma sub-rede depois de excluir a última instância gerenciada do Azure SQL Database que reside nele.

As instâncias gerenciadas são implantadas em [clusters virtuais](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Cada cluster virtual está associado a uma sub-rede. O cluster virtual persiste por design por 12 horas após a exclusão de última instância para permitir que você crie instâncias gerenciadas mais rapidamente na mesma sub-rede. Não há custo para manter um cluster virtual vazio. Durante esse período, a sub-rede associada ao cluster virtual não pode ser excluída.

Se você não quiser esperar 12 horas e preferir excluir o cluster virtual e sua sub-rede mais cedo, você pode fazê-lo manualmente. Exclua o cluster virtual manualmente usando o portal Azure ou a API de clusters virtuais.

> [!IMPORTANT]
> - O cluster virtual não deve conter instâncias gerenciadas para que a exclusão seja bem sucedida. 
> - A exclusão de um cluster virtual é uma operação de longa duração com duração de cerca de 1,5 horas (ver [Operações de gerenciamento de instâncias gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) para tempo de exclusão de cluster virtual atualizado) durante o qual o cluster virtual ainda estará visível no portal até que esse processo seja concluído.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Excluir cluster virtual do portal Azure

Para excluir um cluster virtual usando o portal Azure, procure os recursos do cluster virtual.

![Captura de tela do portal Azure, com caixa de pesquisa destacada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois de localizar o cluster virtual que deseja excluir, selecione esse recurso e **selecione Excluir**. Você é solicitado a confirmar a exclusão do cluster virtual.

![Captura de tela do painel de clusters virtuais do portal Azure, com opção Excluir destacada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

As notificações do portal Azure mostrarão uma confirmação de que a solicitação para excluir o cluster virtual foi enviada com sucesso. A operação de exclusão em si durará cerca de 1,5 horas durante as quais o cluster virtual ainda estará visível no portal. Uma vez que o processo seja concluído, o cluster virtual não será mais visível e a sub-rede associada a ele será liberada para reutilização.

> [!TIP]
> Se não houver instâncias gerenciadas mostradas no cluster virtual e você não conseguir excluir o cluster virtual, certifique-se de que você não tenha uma implantação de instância contínua em andamento. Isso inclui implantações iniciadas e canceladas que ainda estão em andamento. Isso porque essas operações ainda usarão o cluster virtual bloqueando-o da exclusão. A revisão da guia Implantações do grupo de recursos para o qual a instância foi implantada indicará quaisquer implantações em andamento. Neste caso, aguarde a implantação para concluir, excluir a instância gerenciada e, em seguida, o cluster virtual.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Excluir cluster virtual usando a API

Para excluir um cluster virtual através da API, use os parâmetros URI especificados no [método de exclusão de clusters virtuais](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para um tutorial que mostra como criar uma rede virtual, criar uma Instância Gerenciada e restaurar um banco de dados com base em um backup de banco de dados, confira [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte [Configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
