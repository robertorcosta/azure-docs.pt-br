---
title: Alterações na documentação de máquinas virtuais do SQL Server no Azure | Microsoft Docs
description: Saiba mais sobre os novos recursos e aprimoramentos para SQL Server em máquinas virtuais do Azure.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 9f41cb11960dbe9487231f14b005376261075a83
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231498"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações na documentação das máquinas virtuais do SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Azure permite implantar uma VM (máquina virtual) com uma imagem do SQL Server inserida. Este artigo resume as alterações na documentação associadas aos novos recursos e aprimoramentos nas versões recentes das [Máquinas Virtuais do SQL Server no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="july-2020"></a>Julho de 2020


| Alterações | Detalhes |
| --- | --- |
| **Migrar log para ultra Disk** | Saiba como você pode [migrar seu arquivo de log para um ultra Disk](storage-migrate-to-ultradisk.md) para aproveitar o alto desempenho e baixa latência. | 




## <a name="june-2020"></a>Junho de 2020


| Alterações | Detalhes |
| --- | --- |
| **Nome de rede distribuída (DNN)** | SQL Server 2019 no Windows Server 2016 + agora está visualizando o suporte para roteamento de tráfego para a FCI (instância de cluster de failover) usando um [nome de rede distribuída](hadr-distributed-network-name-dnn-configure.md) em vez de usar Azure Load Balancer. Esse suporte simplifica e simplifica a conexão à sua solução HA (alta disponibilidade) no Azure. | 
| **FCI com discos compartilhados do Azure** | Agora é possível implantar sua [FCI (instância de cluster de failover)](failover-cluster-instance-overview.md) usando [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) com o SQL Server 2019 em uma máquina virtual do Windows Server 2016 +. |
| **Documentos reorganizados do FCI** | A documentação sobre as [instâncias de cluster de failover com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) foi reescrita e reorganizada para fins de clareza. Separamos alguns dos conteúdos de configuração, como as [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md), como preparar uma [máquina virtual para um SQL Server FCI](failover-cluster-instance-prepare-vm.md)e como configurar [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Maio de 2020 

| Alterações | Detalhes |
| --- | --- |
| **Família do Azure SQL** | SQL Server em máquinas virtuais do Azure agora é uma parte da [família de produtos SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md). Confira nossa [nova aparência](../index.yml)! Nada mudou no produto, mas a documentação tem o objetivo de tornar a decisão do produto SQL do Azure mais fácil. | 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Suporte do Azure Government** | Agora é possível registrar SQL Server máquinas virtuais com o provedor de recursos de VM do SQL para máquinas virtuais hospedadas na nuvem [do Azure governamental](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica de DR gratuita no Azure** | Você poderá hospedar uma [instância passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) para recuperação de desastre no Azure na sua instância do SQL Server local se tiver o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registro do provedor de recursos em massa** | Agora você pode registrar SQL Server máquinas virtuais [em massa](sql-vm-resource-provider-bulk-register.md) com o provedor de recursos. | 
|**Configuração de armazenamento com otimização de desempenho** | Agora você pode [personalizar totalmente a configuração de armazenamento](storage-configuration.md#new-vms) ao criar uma VM do SQL Server. |
|**Compartilhamento de arquivo Premium para a FCI** | Agora você pode criar uma instância de cluster de failover usando um [compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) em vez do método original de [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Host Dedicado do Azure** | Você pode executar sua VM SQL Server no [host dedicado do Azure](dedicated-host.md). | 
| **SQL Server migração de VM para uma região diferente** | Use o Azure Site Recovery para [migrar sua VM do SQL Server de uma região para outra](move-sql-vm-different-region.md). |
|  **Novos modos de instalação do SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS no [modo leve](sql-server-iaas-agent-extension-automate-management.md) para evitar a reinicialização do serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade de edição](change-sql-server-edition.md) da sua VM do SQL Server. |
| **Alterações no provedor de recursos da VM do SQL** | Você pode [registrar sua VM do SQL Server no provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md) usando os novos modos do SQL IaaS. Essa funcionalidade inclui imagens do [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes).|
| **Imagens do tipo traga sua própria licença por meio do Benefício Híbrido do Azure** | As imagens do tipo traga sua própria licença implantadas por meio do Azure Marketplace agora podem alternar o [tipo de licença para pagamento conforme o uso](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Novo SQL Server gerenciamento de VM no portal do Azure** | Agora há uma forma de gerenciar sua VM do SQL Server no portal do Azure. Para obter mais informações, confira [Gerenciar VMs do SQL Server no portal do Azure](manage-sql-vm-portal.md).  | 
| **Suporte estendido para SQL Server 2008 e 2008 R2** | [Estenda o suporte](sql-server-2008-extend-end-of-support.md) para o SQL Server 2008 e o SQL Server 2008 R2 fazendo a migração do *estado em que se encontra* para uma VM do Azure. | 
| **Capacidade de suporte a imagens personalizadas** | Agora você pode instalar a [extensão SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) para o sistema operacional personalizado e SQL Server imagens, o que oferece a funcionalidade limitada de [licenciamento flexível](licensing-model-azure-hybrid-benefit-ahb-change.md). Quando você estiver registrando sua imagem personalizada com o provedor de recursos de VM do SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registro falhará. | 
| **Capacidade de suporte à instância nomeada** | Agora você poderá usar a extensão [SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) com uma instância nomeada se a instância padrão tiver sido desinstalada corretamente. | 
| **Melhoria do portal** | A experiência do portal do Azure para implantação de uma VM do SQL Server foi renovada para aprimorar a usabilidade. Para obter mais informações, confira o breve [início rápido](sql-vm-create-portal-quickstart.md) e o [guia de instruções](create-sql-vm-portal.md) mais detalhado para implantar uma VM do SQL Server.|
| **Aprimoramento do portal** | Agora é possível alterar o modelo de licenciamento de uma VM do SQL Server de pagamento conforme o uso para traga sua própria licença usando o [portal do Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Simplificação da implantação do grupo de disponibilidade para uma VM SQL Server por meio do CLI do Azure** | Agora ficou mais fácil do que nunca implantar um grupo de disponibilidade em uma VM do SQL Server no Azure. Você pode usar o [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o balanceador de carga interno e os ouvintes do grupo de disponibilidade, tudo na linha de comando. Para obter mais informações, consulte [usar o CLI do Azure para configurar um grupo de disponibilidade Always on para SQL Server em uma VM do Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo provedor de recursos para um cluster do SQL Server** | Um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. O ingresso de uma VM do SQL Server em *SqlVirtualMachineGroups* inicializa o serviço WSFC (Cluster de Failover do Windows Server) e ingressa a VM no cluster.  |
| **Configuração automatizada de uma implantação de grupo de disponibilidade com os modelos de início rápido do Azure** |Agora é possível criar o cluster de failover do Windows, ingressar SQL Server VMs a ele, criar o ouvinte e configurar o balanceador de carga interno usando dois modelos de início rápido do Azure. Para obter mais informações, confira [Usar modelos de início rápido do Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-quickstart-template-configure.md). | 
| **Registro automático para o provedor de recursos de VM do SQL** | SQL Server VMs implantadas após este mês são automaticamente registradas com o novo provedor de recursos de VM do SQL. As VMs do SQL Server implantadas antes deste mês ainda precisarão ser registradas manualmente. Para obter mais informações, confira [Registrar uma máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md).|
|**Novo provedor de recursos de VM do SQL** |  Um novo provedor de recursos (Microsoft.SqlVirtualMachine) fornece um melhor gerenciamento das VMs do SQL Server. Para obter mais informações sobre como registrar suas VMs, confira [Registrar uma máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md). |
|**Alternar o modelo de licenciamento** | Agora você pode alternar entre o modelo de pagamento conforme o uso e traga sua própria licença para a sua VM do SQL Server usando a CLI do Azure ou o PowerShell. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisionar SQL Server em uma VM do Windows](create-sql-vm-portal.md)
* [Migrar um banco de dados para SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastre para Máquinas Virtuais do SQL Server no Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho para as Máquinas Virtuais do SQL Server no Azure](performance-guidelines-best-practices.md)
* [Padrões de aplicativo e estratégias de desenvolvimento para Máquinas Virtuais do SQL Server no Azure](application-patterns-development-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisionar SQL Server em uma máquina virtual Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Perguntas Frequentes (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
