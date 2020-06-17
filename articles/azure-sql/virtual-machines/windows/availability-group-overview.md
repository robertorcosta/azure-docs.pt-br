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
ms.openlocfilehash: 62dce0204f77ab65473fc1735015e41f483dddb1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84036947"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introdução aos grupos de disponibilidade do SQL Server em máquinas virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure. 

Os grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes aos grupos de disponibilidade Always On locais. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um Grupo de Disponibilidade do SQL Server completo em Máquinas Virtuais do Azure.

![Grupo de disponibilidade](./media/availability-group-overview/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga contém o endereço IP do ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade cada grupo exige um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Além disso, em um cluster de failover convidado de VM IaaS do Azure, recomendamos um só NIC por servidor (nó de cluster) e uma só sub-rede. A rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster de convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

Para aumentar a redundância e a alta disponibilidade, as VMs do SQL Server devem estar no mesmo [conjunto de disponibilidade](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) ou em [zonas de disponibilidade](/azure/availability-zones/az-overview) diferentes. 

|  | Versão do Windows Server | Versão do SQL Server | Edição do SQL Server | Configuração de quorum do WSFC | DR com várias regiões | Suporte a várias sub-redes | Suporte para um AD existente | DR com várias zonas na mesma região | Suporte para AG de Distribuição sem domínio do AD | Suporte para AG de Distribuição sem cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI da VM do SQL](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | Ent | Testemunha da nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelos de início rápido](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | Ent | Testemunha da nuvem | Não | Sim | Sim | Sim | Não | Não |
| [Modelo do portal](availability-group-azure-marketplace-template-configure.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Compartilhamento de arquivo | Não | Não | Não | Não | Não | Não |
| [Manual](availability-group-manually-configure-prerequisites-tutorial.md) | Todos | Todos | Todos | Todos | Sim | Sim | Sim | Sim | Sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em Máquinas Virtuais do Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com a CLI do Azure
O uso da CLI do Azure para configurar e implantar um grupo de disponibilidade é a opção recomendada, pois é a melhor em termos de simplicidade e velocidade da implantação. Com a CLI do Azure, a criação do cluster de failover do Windows, o ingresso das VMs do SQL Server ao cluster, bem como a criação do ouvinte e do balanceador de carga interno, podem ser feitas em menos de 30 minutos. Essa opção ainda exige uma criação manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, confira [Usar a CLI da VM do SQL do Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com os modelos de início rápido do Azure
Os modelos de início rápido do Azure utilizam o provedor de recursos de VM do SQL para implantar o cluster de failover do Windows, ingressar as VMs do SQL Server nele, criar o ouvinte e configurar o Balanceador de Carga Interno. Essa opção ainda exige uma criação manual do grupo de disponibilidade e do ILB (Balanceador de Carga Interno), mas automatiza e simplifica todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, confira [Usar modelo de início rápido do Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo do portal do Azure

[Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal do Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e criar o grupo de disponibilidade. Consulte os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade do SQL Server Always On nas máquinas virtuais do Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Criar Grupo de Disponibilidade Always On para melhorar a disponibilidade e a recuperação de desastre](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Próximas etapas

[Configurar um Grupo de Disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure em diferentes regiões](availability-group-manually-configure-multiple-regions.md)
