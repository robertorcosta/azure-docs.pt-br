---
title: Gerenciar a disponibilidade de VMs do Windows no Azure
description: Aprenda como usar várias máquinas virtuais para garantir a alta disponibilidade do aplicativo do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561180"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Gerenciar a disponibilidade de máquinas virtuais Windows no Azure 

Aprenda como configurar e gerenciar várias máquinas virtuais para garantir a alta disponibilidade do aplicativo do Windows no Azure. Você também pode [gerenciar a disponibilidade das máquinas virtuais do Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter instruções sobre como criar e usar conjuntos de disponibilidade ao usar o modelo de implantação clássica, veja [Como configurar um conjunto de disponibilidade](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre o balanceamento de carga das máquinas virtuais, veja [Balanceamento de carga de máquinas virtuais](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Exibir arquiteturas de referência para executar aplicativos de N camadas no SQL Server no IaaS

* [Aplicativo de N camadas do Windows no Azure com SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar um aplicativo de N camadas em várias regiões do Azure para alta disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
