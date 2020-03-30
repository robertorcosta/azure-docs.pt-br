---
title: Alterações de documentação para sql server em máquinas virtuais do Azure| Microsoft Docs
description: Conheça os novos recursos e melhorias para o SQL Server em uma VM Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201638"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações de documentação para sql server em máquinas virtuais do Azure

O Azure permite que você implante uma máquina virtual (VM) com uma imagem do SQL Server incorporada. Este artigo resume as alterações de documentação associadas a novos recursos e melhorias nas recentes versões do [SQL Server no Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Apoio do governo azure** | Agora é possível registrar máquinas virtuais SQL Server com o provedor de recursos SQL VM para máquinas virtuais hospedadas na nuvem [do Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica gratuita de DR no Azure** | Você pode hospedar uma [instância passiva gratuita](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) para recuperação de desastres no Azure para a instância do SQL Server no local se você tiver garantia de [software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registro de provedor de recursos em massa** | Agora você pode [registrar em massa](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) máquinas virtuais SQL com o provedor de recursos. | 
|**Configuração de armazenamento otimizado para desempenho** | Agora você pode [personalizar totalmente sua configuração](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) de armazenamento ao criar um novo VM do SQL Server. |
|**Compartilhamento de arquivos premium para FCI** | Agora você pode criar uma instância de cluster failover usando um [Compartilhamento de arquivo premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) em vez do método original do Storage Spaces [Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Anfitrião dedicado do Azure** | Você pode executar seu VM do Servidor SQL em um [host dedicado ao Azure](virtual-machines-windows-sql-dedicated-host.md). | 
| **Mova SQL VM para diferentes regiões** | Use o Azure Site Recovery para [migrar seu VM do Servidor SQL de uma região para outra](virtual-machines-windows-sql-move-different-region.md). |
|  **Novos modos de instalação SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS no [modo leve](virtual-machines-windows-sql-server-agent-extension.md) para evitar reiniciar o serviço do SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade edition](virtual-machines-windows-sql-change-edition.md) para o seu VM sql server. |
| **Alterações no provedor de recursos SQL VM** | Você pode [registrar seu VM do SQL Server com o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md) usando os novos modos SQL IaaS. Esse recurso inclui imagens [do Windows Server 2008.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Traga imagens de sua própria licença usando o Azure Hybrid Benefit** | As imagens de sua própria licença implantadas no Azure Marketplace agora podem mudar seu [tipo de licença para pay-as-you-go](virtual-machines-windows-sql-ahb.md#remarks).| 
| **Novo gerenciamento de VM do SQL Server no portal Azure** | Agora há uma maneira de gerenciar seu VM sql server no portal Azure. Para obter mais informações, consulte [Gerenciar VMs do Servidor SQL no portal Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| **Suporte estendido para SQL Server 2008/2008 R2** | [Amplie o suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) ao SQL Server 2008 e ao SQL Server 2008 R2 migrando *como é* para uma VM Azure. | 
| **Suporte personalizado de imagem** | Agora você pode instalar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) para imagens personalizadas do Sistema Operacional e SQL, que oferece a funcionalidade limitada do [licenciamento flexível.](virtual-machines-windows-sql-ahb.md) Quando estiver registrando sua imagem personalizada no provedor de recursos SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registro falhará. | 
| **Suporte a instância saqueado** | Agora você pode usar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) com uma instância nomeada, se a instância padrão tiver sido desinstalada corretamente. | 
| **Aprimoramento do portal** | A experiência do portal Azure para a implantação de um VM do SQL Server foi renovada para melhorar a usabilidade. Para obter mais informações, consulte o breve [quickstart](quickstart-sql-vm-create-portal.md) e o guia de [como usar](virtual-machines-windows-portal-sql-server-provision.md) para implantar um VM do SQL Server.|
| **Melhoria do portal** | Agora é possível alterar o modelo de licenciamento de um VM do Servidor SQL de pay-as-you-go para trazer sua própria licença usando o [portal Azure](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Simplificação da implantação do grupo de disponibilidade com o Azure SQL Server VM CLI** | Agora é mais fácil do que nunca implantar um grupo de disponibilidade em um VM do SQL Server no Azure. Você pode usar o [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o balanceador de carga interna e os ouvintes do grupo de disponibilidade, todos da linha de comando. Para obter mais informações, consulte [Use o Azure SQL Server VM CLI para configurar um grupo de disponibilidade Always On para SQL Server em uma VM Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo provedor de recursos para um cluster SQL Server** | Um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. A adesão a um VM do SQL Server ao *SqlVirtualMachineGroups* inicia o serviço Descembara do Cluster de Failover (WSFC) do Windows Server e junta a VM ao cluster.  |
| **Configuração automatizada de uma implantação de grupo de disponibilidade com modelos de quickstart do Azure** |Agora é possível criar o cluster de failover do Windows, juntar VMs do SQL Server a ele, criar o ouvinte e configurar o balanceador de carga interna com dois modelos de partida rápida do Azure. Para obter mais informações, consulte [Use os modelos quickstart do Azure para configurar um grupo de disponibilidade Always On para SQL Server em uma VM Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registro automático para o provedor de recursos SQL VM** | VMs do SQL Server implantadas depois deste mês são automaticamente registradas com o novo provedor de recursos do SQL Server. As VMs do SQL Server implantadas antes deste mês ainda precisam ser registradas manualmente. Para obter mais informações, consulte [Registre uma máquina virtual do SQL Server no Azure com o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Novo provedor de recursos de VM do SQL** |  Um novo provedor de recursos (Microsoft.SqlVirtualMachine) fornece um melhor gerenciamento de suas VMs do SQL Server. Para obter mais informações sobre o registro de suas VMs, consulte [Registre uma máquina virtual do SQL Server no Azure com o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Alternar o modelo de licenciamento** | Agora você pode alternar entre os modelos pay-per-use e trazer sua própria licença para o seu VM sql server usando o Azure CLI ou PowerShell. Para obter mais informações, consulte [Como alterar o modelo de licenciamento de uma máquina virtual SQL Server no Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrando um banco de dados para o SQL Server em uma VM Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas de desempenho para SQL Server em Máquinas Virtuais Do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de aplicativos e estratégias de desenvolvimento para SQL Server em Máquinas Virtuais Do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisão de uma máquina virtual SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
