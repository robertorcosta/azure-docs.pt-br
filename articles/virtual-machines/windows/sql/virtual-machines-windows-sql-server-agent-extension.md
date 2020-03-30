---
title: Automatize tarefas de gerenciamento com a extensão do agente IaaS
description: Este artigo descreve como gerenciar o SQL Server IaaS Agent Extension, que automatiza tarefas específicas de administração do SQL Server. Isso inclui backup automatizado, patches automatizados e integração do Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
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
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030771"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatize tarefas de gerenciamento em máquinas virtuais do Azure usando a extensão do agente IaaS do SQL Server
> [!div class="op_single_selector"]
> * [Gerenciador de recursos](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS Agent (SqlIaasExtension) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este artigo fornece uma visão geral dos serviços que a extensão suporta. Este artigo também fornece instruções para instalação, status e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte [SQL Server IaaS Agent Extension for SQL Server VMs (clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL Server** |Automatiza o agendamento de backups para todos os bancos de dados para a instância padrão ou uma instância nomeada [corretamente](virtual-machines-windows-sql-server-iaas-faq.md#administration) do SQL Server na VM. Para obter mais informações, consulte [backup automatizado para SQL Server em máquinas virtuais Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Patches automatizados do SQL Server** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para a VM podem ocorrer, evitando atualizações da carga de trabalho durante horários de pico. Para obter mais informações, consulte [Patches automatizados para SQL Server em máquinas virtuais Do Zure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Azure Key Vault** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para obter mais informações, consulte [Configure Azure Key Vault integration for SQL Server on Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois que o SQL Server Iaas Agent Extension é instalado e executado, ele disponibiliza os recursos de administração:

* No painel SQL Server da máquina virtual no portal Azure e através do Azure PowerShell para imagens do SQL Server no Azure Marketplace.
* Através do Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para usar a extensão do agente IaaS do Servidor SQL em sua VM:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versão do SQL Server:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* Microsoft SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Baixe e configure os mais recentes comandos Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalação
A extensão SQL Server IaaS é instalada quando você registra seu VM do Servidor SQL com o [provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessário, você pode instalar o agente SQL Server IaaS manualmente usando o comando PowerShell abaixo: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instale em uma VM com uma única instância chamada SQL Server
A extensão SQL Server IaaS funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para usar uma instância nomeada do SQL Server, siga estas etapas:
   1. Implante um VM do SQL Server do Azure Marketplace. 
   1. Desinstale a extensão IaaS do [portal Azure](https://portal.azure.com).
   1. Desinstale o SQL Server completamente dentro do VM do Servidor SQL.
   1. Instale o SQL Server com uma instância nomeada dentro do VM do Servidor SQL. 
   1. Instale a extensão IaaS do portal Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obtenha o status da extensão SQL Server IaaS
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Selecione **Todas as configurações** na janela da máquina virtual e, em seguida, **selecione Extensões**. Você deverá ver a extensão **SqlIaasExtension** na lista.

![Status da extensão do agente IaaS do servidor SQL no portal Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o **cmdlet Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais de status. Você pode obter informações específicas de status sobre backup e patches automatizados usando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal Azure, você pode desinstalar a extensão selecionando as elipses na janela **Extensões** de suas propriedades de máquina virtual. Em seguida, **selecione Excluir**.

![Desinstalação da extensão do agente IaaS do servidor SQL no portal Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o **cmdlet Remove-AzVMSqlServerExtension** PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços que a extensão suporta. Para obter mais informações, consulte os artigos mencionados na seção de [serviços suportados](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais Azure, consulte o Que é o [SQL Server em Máquinas Virtuais Azure?](virtual-machines-windows-sql-server-iaas-overview.md).
