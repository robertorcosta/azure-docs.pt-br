---
title: Alterações na documentação de máquinas virtuais do SQL Server no Azure | Microsoft Docs
description: Conheça os novos recursos e aprimoramentos do SQL Server em uma VM do Azure
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
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032467"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações na documentação das máquinas virtuais do SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Azure permite implantar uma VM (máquina virtual) com uma imagem do SQL Server inserida. Este artigo resume as alterações na documentação associadas aos novos recursos e aprimoramentos nas versões recentes das [Máquinas Virtuais do SQL Server no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Suporte do Azure Government** | Agora é possível registrar máquinas virtuais do SQL Server com o provedor de recursos de VM do SQL para máquinas virtuais hospedadas na nuvem do [Azure Government](https://azure.microsoft.com/global-infrastructure/government/). | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica de DR gratuita no Azure** | Você poderá hospedar uma [instância passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) para recuperação de desastre no Azure na sua instância do SQL Server local se tiver o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registro do provedor de recursos em massa** | Agora você pode [registrar em massa](sql-vm-resource-provider-bulk-register.md) máquinas virtuais do SQL no provedor de recursos. | 
|**Configuração de armazenamento otimizado para desempenho** | Agora você pode [personalizar totalmente a configuração de armazenamento](storage-configuration.md#new-vms) ao criar uma VM do SQL Server. |
|**Compartilhamento de arquivo Premium para a FCI** | Agora você pode criar uma instância de cluster de failover usando um [compartilhamento de arquivo Premium](failover-cluster-instance-premium-file-share-manually-configure.md) em vez do método original de [Espaços de Armazenamento Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Host Dedicado do Azure** | Você pode executar sua VM do SQL Server em um [Host Dedicado do Azure](dedicated-host.md). | 
| **Mover a VM do SQL para uma outra região** | Use o Azure Site Recovery para [migrar sua VM do SQL Server de uma região para outra](move-sql-vm-different-region.md). |
|  **Novos modos de instalação do SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS no [modo leve](sql-server-iaas-agent-extension-automate-management.md) para evitar a reinicialização do serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade de edição](change-sql-server-edition.md) da sua VM do SQL Server. |
| **Alterações no provedor de recursos de VM do SQL** | Você pode [registrar sua VM do SQL Server no provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md) usando os novos modos do SQL IaaS. Essa funcionalidade inclui imagens do [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes).|
| **Imagens do tipo traga sua própria licença por meio do Benefício Híbrido do Azure** | As imagens do tipo traga sua própria licença implantadas por meio do Azure Marketplace agora podem alternar o [tipo de licença para pagamento conforme o uso](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Novo gerenciamento da VM do SQL Server no portal do Azure** | Agora há uma forma de gerenciar sua VM do SQL Server no portal do Azure. Para obter mais informações, confira [Gerenciar VMs do SQL Server no portal do Azure](manage-sql-vm-portal.md).  | 
| **Suporte estendido para o SQL Server 2008/2008 R2** | [Estenda o suporte](sql-server-2008-extend-end-of-support.md) para o SQL Server 2008 e o SQL Server 2008 R2 fazendo a migração do *estado em que se encontra* para uma VM do Azure. | 
| **Capacidade de suporte a imagens personalizadas** | Agora você pode instalar a [extensão do SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) para imagens personalizadas do sistema operacional e do SQL, que oferece a funcionalidade limitada de [licenciamento flexível](licensing-model-azure-hybrid-benefit-ahb-change.md). Quando você estiver registrando sua imagem personalizada no provedor de recursos do SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registro falhará. | 
| **Capacidade de suporte à instância nomeada** | Agora você poderá usar a extensão [SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) com uma instância nomeada se a instância padrão tiver sido desinstalada corretamente. | 
| **Melhoria do portal** | A experiência do portal do Azure para implantação de uma VM do SQL Server foi renovada para aprimorar a usabilidade. Para obter mais informações, confira o breve [início rápido](sql-vm-create-portal-quickstart.md) e o [guia de instruções](create-sql-vm-portal.md) mais detalhado para implantar uma VM do SQL Server.|
| **Aprimoramento do portal** | Agora é possível alterar o modelo de licenciamento de uma VM do SQL Server de pagamento conforme o uso para traga sua própria licença usando o [portal do Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Simplificação da implantação do grupo de disponibilidade com a CLI da VM do SQL Server no Azure** | Agora ficou mais fácil do que nunca implantar um grupo de disponibilidade em uma VM do SQL Server no Azure. Use a [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o balanceador de carga interno e os ouvintes do grupo de disponibilidade, tudo isso na linha de comando. Para obter mais informações, confira [Usar a CLI da VM do SQL Server no Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo provedor de recursos para um cluster do SQL Server** | Um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. O ingresso de uma VM do SQL Server em *SqlVirtualMachineGroups* inicializa o serviço WSFC (Cluster de Failover do Windows Server) e ingressa a VM no cluster.  |
| **Configuração automatizada de uma implantação de grupo de disponibilidade com os modelos de início rápido do Azure** |Agora é possível criar o cluster de failover do Windows, ingressar VMs do SQL Server nele, criar o ouvinte e configurar o balanceador de carga interno com dois modelos de início rápido do Azure. Para obter mais informações, confira [Usar modelos de início rápido do Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-quickstart-template-configure.md). | 
| **Registro automático para o provedor de recursos de VM do SQL** | VMs do SQL Server implantadas depois deste mês são automaticamente registradas com o novo provedor de recursos do SQL Server. As VMs do SQL Server implantadas antes deste mês ainda precisarão ser registradas manualmente. Para obter mais informações, confira [Registrar uma máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md).|
|**Novo provedor de recursos de VM do SQL** |  Um novo provedor de recursos (Microsoft.SqlVirtualMachine) fornece um melhor gerenciamento das VMs do SQL Server. Para obter mais informações sobre como registrar suas VMs, confira [Registrar uma máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md). |
|**Alternar o modelo de licenciamento** | Agora você pode alternar entre o modelo de pagamento conforme o uso e traga sua própria licença para a sua VM do SQL Server usando a CLI do Azure ou o PowerShell. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisionar uma VM do Windows do SQL Server](create-sql-vm-portal.md)
* [Como migrar um banco de dados para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastre para Máquinas Virtuais do SQL Server no Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho para as Máquinas Virtuais do SQL Server no Azure](performance-guidelines-best-practices.md)
* [Padrões de aplicativo e estratégias de desenvolvimento para Máquinas Virtuais do SQL Server no Azure](application-patterns-development-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisionar uma máquina virtual do SQL Server no Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Perguntas Frequentes (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
