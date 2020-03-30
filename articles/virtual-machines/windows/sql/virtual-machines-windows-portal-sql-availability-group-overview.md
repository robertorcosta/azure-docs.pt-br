---
title: Visão geral dos grupos de disponibilidade
description: Este artigo apresenta os Grupos de Disponibilidade do SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037495"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introduzindo grupos de disponibilidade do SQL Server em máquinas virtuais do Azure

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure. 

Os grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes aos grupos de disponibilidade Always On locais. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um Grupo de Disponibilidade do SQL Server completo em Máquinas Virtuais do Azure.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga contém o endereço IP do ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade cada grupo exige um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Além disso, em um cluster de failover para hóspedes do Azure IaaS VM, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster de convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

Para aumentar a redundância e a alta disponibilidade, as VMs do SQL Server devem estar no mesmo [conjunto de disponibilidade](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)ou em [diferentes zonas de disponibilidade](/azure/availability-zones/az-overview). 

|  | Versão do Servidor do Windows | Versão do SQL Server | Edição do SQL Server | Config wsfc quórum | DR com Multi-região | Suporte a várias sub-redes | Suporte para um AD existente | DR com região da mesma região multi-zona | Suporte ao Dist-AG sem domínio AD | Suporte Dist-AG sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Testemunha da nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelos quickstart](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Testemunha da nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelo de Portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Compartilhamento de arquivo | Não | Não | Não | Não | Não | Não |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em Máquinas Virtuais do Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com a Cli Azure
Usar o Azure CLI para configurar e implantar um grupo de disponibilidade é a opção recomendada, pois é a melhor em termos de simplicidade e velocidade de implantação. Com o Azure CLI, a criação do Cluster Failover do Windows, unindo VMs do SQL Server ao cluster, bem como a criação do ouvinte e do Balancer de Carga Interna podem ser alcançadas em menos de 30 minutos. Essa opção ainda requer uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, consulte [Use Azure SQL VM CLI para configurar o grupo always on availability para SQL Server em uma VM Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com modelos Azure Quickstart
Os Modelos de Inicialinicial rápido do Azure utilizam o provedor de recursos SQL VM para implantar o Cluster Defailover do Windows, juntar VMs do SQL Server a ele, criar o ouvinte e configurar o Balancer de Carga Interna. Essa opção ainda requer uma criação manual do grupo de disponibilidade e do Balancer de Carga Interna (ILB), mas automatiza e simplifica todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, consulte [Use Azure Quickstart Template para configurar o grupo always on availability para SQL Server em uma VM Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de portal Azure

[Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e criar o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade do SQL Server Always On nas máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar Grupo de Disponibilidade Always On para melhorar a disponibilidade e a recuperação de desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Próximas etapas

[Configure um sql server sempre em grupo de disponibilidade em máquinas virtuais do Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md)
