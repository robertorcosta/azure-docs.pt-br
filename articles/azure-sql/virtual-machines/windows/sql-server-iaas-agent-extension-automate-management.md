---
title: Automatizar tarefas de gerenciamento com a Extensão do Agente IaaS
description: Este artigo descreve como gerenciar a Extensão SQL Server IaaS Agent, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 89210cda6390fd7c3cf4ca2877b8899559a41321
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668705"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gerenciamento em Máquinas Virtuais do Azure com a Extensão SQL Server IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Resource Manager](sql-server-iaas-agent-extension-automate-management.md)
> * [Clássico](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS Agent (SqlIaasExtension) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este artigo fornece uma visão geral dos serviços com os quais a extensão é compatível. Este artigo também fornece instruções para instalação, status e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, confira [Extensão SQL Server IaaS Agent para VMs do SQL Server (Clássico)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL Server** |Automatiza o agendamento de backups para todos os bancos de dados, seja da instância padrão ou então de uma instância nomeada [corretamente instalada](frequently-asked-questions-faq.md#administration) do SQL Server na VM. Para obter mais informações, confira [Backup automatizado para SQL Server em Máquinas Virtuais do Azure (Resource Manager)](automated-backup-sql-2014.md). |
| **Aplicação de patch automatizada do SQL Server** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para a VM podem ocorrer, evitando atualizações da carga de trabalho durante horários de pico. Para saber mais, veja [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)](automated-patching.md). |
| **Integração do Azure Key Vault** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a integração do Azure Key Vault para SQL Server em Máquinas Virtuais do Azure (Resource Manager)](azure-key-vault-integration-configure.md). |

Depois que a Extensão SQL Server IaaS Agent estiver instalada e em execução, ela disponibilizará os recursos de administração:

* No painel de SQL Server da máquina virtual na portal do Azure e por meio de Azure PowerShell para imagens do SQL Server no Azure Marketplace.
* Por meio de Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para uso da Extensão SQL Server IaaS Agent em sua VM:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versão do SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* Microsoft SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Baixar e configurar os comandos mais recentes do Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalação
A extensão de IaaS SQL Server é instalada quando você registra sua VM de SQL Server com o [provedor de recursos de VM SQL Server](sql-vm-resource-provider-register.md). Se necessário, instale o SQL Server IaaS Agent manualmente usando o comando do PowerShell abaixo: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar em uma VM com apenas uma instância do SQL Server nomeada
A extensão SQL Server IaaS funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para usar uma instância nomeada do SQL Server, siga estas etapas:
   1. Implante uma VM do SQL Server do Azure Marketplace. 
   1. Desinstale a extensão IaaS do [portal do Azure](https://portal.azure.com).
   1. Desinstale o SQL Server por completo de dentro da VM do SQL Server.
   1. Instale o SQL Server com uma instância nomeada dentro da VM do SQL Server. 
   1. Instale a extensão IaaS do portal do Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obter o status da extensão IaaS do SQL Server
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Selecione **Todas as configurações** na janela da máquina virtual e selecione **Extensões**. Você deverá ver a extensão **SqlIaasExtension** na lista.

![Status da Extensão SQL Server IaaS Agent no portal do Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzVMSqlServerExtension** do Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais sobre o status. Você pode obter informações específicas de status do backup automatizado e aplicação de patch usando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal do Azure, você pode desinstalar a extensão selecionando as reticências na janela **Extensões** das propriedades de sua máquina virtual. Em seguida, selecione **Excluir**.

![Desinstalar a Extensão SQL Server IaaS Agent no portal do Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet **Remove-AzVMSqlServerExtension** do PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços compatíveis com a extensão. Para obter mais informações, confira os artigos citados na seção [Serviços compatíveis](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, confira [O que é o SQL Server nas Máquinas Virtuais do Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md).
