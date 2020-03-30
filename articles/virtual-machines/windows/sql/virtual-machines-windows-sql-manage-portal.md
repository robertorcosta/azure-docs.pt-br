---
title: Gerencie VMs do SQL Server no Azure usando o portal Azure | Microsoft Docs
description: Saiba como acessar o recurso de máquina virtual SQL no portal Azure para um VM sql server hospedado no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243205"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gerencie VMs do SQL Server no Azure usando o portal Azure

No [portal Azure,](https://portal.azure.com)o recurso **de máquinas virtuais SQL** é um serviço de gerenciamento independente. Você pode usá-lo para visualizar todas as suas VMs do Servidor SQL simultaneamente e modificar as configurações dedicadas ao SQL Server: 

![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Comentários

- Recomendamos que você use o recurso **de máquinas virtuais SQL** para visualizar e gerenciar suas VMs do SQL Server no Azure. Mas, atualmente, o recurso **de máquinas virtuais SQL** não suporta o gerenciamento de VMs sql server [de fim de suporte.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) Para gerenciar as configurações das VMs do Servidor SQL de fim de suporte, use a guia de [configuração depreciada do SQL Server.](#access-the-sql-server-configuration-tab) 
- O recurso **de máquinas virtuais SQL** está disponível apenas para VMs do SQL Server que [se registraram no provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Acesse o recurso de máquinas virtuais SQL
Para acessar o recurso **de máquinas virtuais SQL,** faça o seguinte:

1. Abra o [portal Azure.](https://portal.azure.com) 
1. Selecione **Todos os serviços**. 
1. Digite **máquinas virtuais SQL** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais SQL** para adicionar essa opção ao menu **Favoritos.** 
1. Selecione **máquinas virtuais SQL**. 

   ![Encontre máquinas virtuais SQL Server em todos os serviços](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. O portal lista todas as VMs do SQL Server disponíveis na assinatura. Selecione o que deseja gerenciar para abrir o recurso **de máquinas virtuais SQL.** Use a caixa de pesquisa se o VM do Servidor SQL não estiver aparecendo. 

   ![Todas as VMs disponíveis do SQL Server](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   A seleção do VM do SQL Server abre o recurso **de máquinas virtuais SQL:** 


   ![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> O recurso **de máquinas virtuais SQL** é para configurações dedicadas do SQL Server. Selecione o nome da VM na caixa **de máquina virtual** para abrir configurações específicas da VM, mas não exclusivas do SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Acesse a guia de configuração do SQL Server
A guia **de configuração do SQL Server** foi depreciada. Neste momento, é o único método para gerenciar VMs sql server [de fim de suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) e VMs do SQL Server que não foram registrados no provedor de recursos [SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).

Para acessar a guia de **configuração preterida do SQL Server,** acesse o recurso **máquinas virtuais.** Use as seguintes etapas:

1. Abra o [portal Azure.](https://portal.azure.com) 
1. Selecione **Todos os serviços**. 
1. Digite **máquinas virtuais** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado **de máquinas virtuais** para adicionar essa opção ao menu **Favoritos.** 
1. Selecione **máquinas virtuais**. 

   ![Busca por máquinas virtuais](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. O portal lista todas as máquinas virtuais na assinatura. Selecione o que deseja gerenciar para abrir o recurso **de máquinas virtuais.** Use a caixa de pesquisa se o VM do Servidor SQL não estiver aparecendo. 
1. Selecione a **configuração do SQL Server** no painel **Configurações** para gerenciar seu VM do Servidor SQL. 

   ![Configuração do SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


