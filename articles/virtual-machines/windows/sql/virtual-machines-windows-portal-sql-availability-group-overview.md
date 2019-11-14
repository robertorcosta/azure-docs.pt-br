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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037495"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Apresentando SQL Server grupos de disponibilidade em máquinas virtuais do Azure

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure. 

Os grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes aos grupos de disponibilidade Always On locais. Para obter mais informações, confira [Grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um Grupo de Disponibilidade do SQL Server completo em Máquinas Virtuais do Azure.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga contém o endereço IP do ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade cada grupo exige um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Além disso, em um cluster de failover convidado da VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster de convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

Para aumentar a redundância e a alta disponibilidade, as VMs SQL Server devem estar no mesmo [conjunto de disponibilidade](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)ou em diferentes [zonas de disponibilidade](/azure/availability-zones/az-overview). 

|  | Versão do Windows Server | Versão do SQL Server | Edição do SQL Server | Configuração de quorum do WSFC | DR com várias regiões | Suporte a várias sub-redes | Suporte para um AD existente | DR com a mesma região de várias zonas | Suporte do dist-AG sem domínio do AD | Suporte do dist-AG sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI DA VM DO SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Enviado | Testemunha da nuvem | Não | sim | sim | sim | Não | Não |
| [Modelos de início rápido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Enviado | Testemunha da nuvem | Não | sim | sim | sim | Não | Não |
| [Modelo de portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Enviado | Compartilhamento de arquivo | Não | Não | Não | Não | Não | Não |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | sim | sim | sim | sim | sim | sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em Máquinas Virtuais do Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com CLI do Azure
Usar CLI do Azure para configurar e implantar um grupo de disponibilidade é a opção recomendada, pois é o melhor em termos de simplicidade e velocidade de implantação. Com o CLI do Azure, a criação do cluster de failover do Windows, unindo SQL Server VMs ao cluster, bem como a criação do ouvinte e do Load Balancer interno, pode ser obtida em menos de 30 minutos. Essa opção ainda requer uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, consulte [usar a CLI de VM do SQL do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com os modelos de início rápido do Azure
Os modelos de início rápido do Azure utilizam o provedor de recursos da VM do SQL para implantar o cluster de failover do Windows, ingressar SQL Server VMs nele, criar o ouvinte e configurar o Load Balancer interno. Essa opção ainda requer uma criação manual do grupo de disponibilidade e Load Balancer interno (ILB), mas automatiza e simplifica todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, consulte [usar o modelo de início rápido do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de portal do Azure

[Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal do Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e criar o grupo de disponibilidade. Confira os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade do SQL Server Always On nas máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar Grupo de Disponibilidade Always On para melhorar a disponibilidade e a recuperação de desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Próximas etapas

[Configurar um Grupo de Disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md)
