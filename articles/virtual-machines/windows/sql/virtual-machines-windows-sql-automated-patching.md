---
title: Aplicação de Patch automatizada VMs do SQL Server (Resource Manager) | Microsoft Docs
description: Explica o recurso de Aplicação de Patch Automatizada para Máquinas Virtuais do SQL Server em execução no Azure usando o Gerenciador de Recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127672"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Gerenciador de Recursos)
> [!div class="op_single_selector"]
> * [Gerenciador de recursos](virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

A aplicação de patch automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure que executa o SQL Server. Atualizações automáticas só podem ser instaladas durante esta janela de manutenção. Para o SQL Server, essa restrição garante que as atualizações do sistema e qualquer reinicialização associada ocorram no melhor momento possível para o banco de dados. 

> [!IMPORTANT]
> Apenas as atualizações do Windows e do SQL Server marcadas como **Importantes** ou **Críticas** são instaladas. Outras atualizações do SQL Server, como pacotes de serviços e atualizações cumulativas que não estão marcadas como **Importantes** ou **Críticas** devem ser instaladas manualmente. 

A aplicação de patch automatizada depende da [Extensão do Agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar a Aplicação de Patch Automatizada, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão do SQL Server:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* Microsoft SQL Server 2017

**Azure PowerShell**:

* [Instalar os comandos mais recentes do Azure PowerShell](/powershell/azure/overview) se você planeja configurar a Aplicação de Patch Automatizada com o PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> A aplicação de Patch automatizada depende da Extensão do Agente IaaS do SQL Server. As imagens atuais da galeria da máquina virtual do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para Aplicação de Patch Automatizada. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

| Configuração | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de patch automatizada** |Habilitar/desabilitar (Desabilitado) |Habilita ou desabilita a Aplicação de Patch Automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado e domingo |A agenda para baixar e instalar atualizações do Windows, do SQL Server e do Microsoft para sua máquina virtual. |
| **Hora de início da manutenção** |0h a 24h |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para concluir o download e a instalação de atualizações. |
| **Categoria de patch** |Importante | A categoria de atualizações do Windows a ser baixada e instalada.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Você pode usar o portal do Azure para configurar a aplicação de Patch automatizada durante o provisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Use o portal do Azure para configurar a aplicação de Patch automatizada quando criar uma nova máquina virtual do SQL Server no modelo de implantação do gerenciador de recursos.

Na guia **Configurações do SQL Server,** selecione **Alterar configuração** em **Patches Automatizado**. As capturas de tela do portal do Azure a seguir mostram a folha **Aplicação de Patch Automatizada** .

![Aplicação de Patch Automática do SQL no Portal do Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para ter contexto, consulte o tópico completo sobre [provisionamento de uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais SQL Server existentes, abra o recurso de [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **Patches** em **Configurações**. 

![Aplicação de Patch Automática do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Quando terminar, clique no botão **OK** na parte inferior da folha **Configuração do SQL Server** para salvar suas alterações.

Se você for habilitar a Aplicação de Patch Automatizada pela primeira vez, o Azure configurará o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure não mostrará que a Aplicação de Patch Automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure reflete as novas configurações.

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
Depois de provisionar sua VM do SQL, use o PowerShell para configurar a Aplicação de Patch Automatizada.

No exemplo a seguir, o PowerShell é usado para configurar a Aplicação de Patch Automatizada em uma VM existente do SQL Server. O comando **New-AzVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtensão -Remendar automaticamenteConfigurações $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Se a extensão já não estiver instalada, a instalação da extensão reiniciará o serviço do SQL Server.

Com base neste exemplo, a tabela a seguir descreve o efeito prático sobre a VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **Dayofweek** |Patches instalados toda quinta-feira. |
| **MaintenanceWindowStartingHour** |Inicia as atualizações às 11h. |
| **MaintenanceWindowsDuration** |Os patches devem ser instalados dentro de 120 minutos. Com base na hora de início, eles devem estar concluídos até 13h. |
| **PatchCategory** |A única configuração possível para este parâmetro é **importante**. Isso instala as atualizações do Windows marcadas como Importantes; não instala as atualizações do SQL Server que não estão incluídas nessa categoria. |

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para desativar o Patching automatizado, execute o mesmo script sem o parâmetro **-Habilitar** para o **New-AzVMSqlServerAutoPatchingConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

