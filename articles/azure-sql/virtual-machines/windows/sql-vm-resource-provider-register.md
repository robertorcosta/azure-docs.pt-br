---
title: Instalar SQL Server extensão do agente IaaS
description: Registre sua máquina virtual do Azure SQL Server com o provedor de recursos da VM do SQL para habilitar recursos para SQL Server máquinas virtuais implantadas fora do Azure Marketplace, bem como conformidade e capacidade de gerenciamento aprimorada.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286147"
---
# <a name="install-sql-server-iaas-agent-extension"></a>Instalar SQL Server extensão do agente IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Registrar seu SQL Server VM com o provedor de recursos da VM do SQL instala a [extensão do agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md). 

Este artigo ensina a registrar uma única VM SQL Server com o provedor de recursos de VM do SQL. Como alternativa, você pode registrar todas as VMs SQL Server [automaticamente](sql-vm-resource-provider-automatic-registration.md) ou com [script em massa](sql-vm-resource-provider-bulk-register.md).


## <a name="overview"></a>Visão geral

O registro com o provedor de recursos instala a [extensão do agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md) e também cria o _recurso_ de **máquina virtual do SQL** em sua assinatura, que é um recurso _separado_ do recurso de máquina virtual. Cancelar o registro de sua VM do SQL Server do provedor de recursos removerá o **recurso** da _máquina virtual do SQL_ , mas não removerá a máquina virtual real.

Implantar uma imagem do Azure Marketplace da VM do SQL Server por meio do portal do Azure registra automaticamente uma VM do SQL Server no provedor de recursos. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual do Azure de um VHD personalizado, deverá registrar sua VM SQL Server com o provedor de recursos da VM do SQL para instalar a extensão do agente IaaS do SQL. 

Para utilizar o provedor de recursos de VM do SQL, você deve primeiro [registrar sua assinatura com o provedor de recursos](#register-subscription-with-rp), o que fornece ao provedor de recursos a capacidade de criar recursos dentro dessa assinatura específica.

> [!IMPORTANT]
> A extensão do agente IaaS do SQL coleta dados para a finalidade expressa de fornecer aos clientes benefícios opcionais ao usar SQL Server nas máquinas virtuais do Azure. A Microsoft não usará esses dados para auditorias de licenciamento sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma máquina virtual do modelo de recurso do Azure [Windows Server 2008 (ou superior)](../../../virtual-machines/windows/quick-create-portal.md) com [SQL Server 2008 (ou superior)](https://www.microsoft.com/sql-server/sql-server-downloads) implantada na nuvem pública ou do Azure governamental. 
- A versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell (mínimo de 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Registrar assinatura com RP

Para registrar sua VM do SQL Server no provedor de recursos da VM do SQL, registre o provedor de recursos em sua assinatura. Isso fornece ao provedor de recursos da VM do SQL a capacidade de criar recursos em sua assinatura.  Você pode fazer isso usando o portal do Azure, o CLI do Azure ou o Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal do Azure e navegue até **Todos os Serviços**. 
1. Vá para **Assinaturas** e selecione a assinatura de seu interesse.  
1. Na página **Assinaturas** , vá para **Provedores de recursos**. 
1. Insira **sql** no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione **Registrar** , **Registrar novamente** ou **Cancelar registro** de para o provedor **Microsoft.SqlVirtualMachine** , dependendo da ação desejada. 

   ![Modificar o provedor](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comando

Registre seu provedor de recursos de VM do SQL em sua assinatura do Azure usando CLI do Azure ou Azure PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Registrar com RP

Há três modos de gerenciamento para a [extensão do agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

A instalação da extensão no modo de gerenciamento completo reinicia o serviço SQL Server para que seja recomendável instalar a extensão no modo leve primeiro e, em seguida, [atualizar para completo](#upgrade-to-full) durante uma janela de manutenção. 

### <a name="lightweight-management-mode"></a>Modo de gerenciamento Leve

Use o CLI do Azure ou Azure PowerShell para registrar sua VM do SQL Server com o provedor de recursos e instalar a extensão de IaaS do SQL no modo leve. Isso não reiniciará o serviço SQL Server. Em seguida, você pode atualizar para o modo completo a qualquer momento, mas isso reiniciará o serviço de SQL Server, assim, é recomendável aguardar até uma janela de manutenção agendada. 

Forneça o tipo de licença do SQL Server como pago conforme o uso (`PAYG`) para pagar por uso, Benefício Híbrido do Azure (`AHUB`) para usar sua licença ou recuperação de desastre (`DR`) para ativar a [licença de réplica de DR gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

As instâncias de cluster de failover e as implantações de várias instâncias só podem ser registradas com o provedor de recursos de VM do SQL no modo leve. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registre uma VM SQL Server no modo leve com o CLI do Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Registre uma VM SQL Server no modo leve com Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Pacote de gerenciamento completo

Registrar seu SQL Server VM no modo completo reiniciará o serviço SQL Server. Continue com cuidado. 

Para registrar sua VM SQL Server diretamente no modo completo (e possivelmente reiniciar o serviço de SQL Server), use o seguinte comando Azure PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gerenciamento NoAgent 

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 ( _não R2_ ) podem ser registrados com o provedor de recursos de VM do SQL no modo [NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Essa opção garante a conformidade e permite que a VM do SQL Server seja monitorada no portal do Azure com funcionalidade limitada.

Especifique `AHUB`, `PAYG` ou `DR` como o **sqlLicenseType** e `SQL2008-WS2008` ou `SQL2008R2-WS2008` como **sqlImageOffer**. 

Para registrar seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use o seguinte CLI do Azure ou Azure PowerShell trecho de código: 


# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registre sua máquina virtual SQL Server 2008 no modo noagent com o CLI do Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registre sua máquina virtual SQL Server 2008 R2 no modo noagent com o CLI do Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Registre sua máquina virtual SQL Server 2008 no modo noagent com Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Registre sua máquina virtual SQL Server 2008 R2 no modo noagent com Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Modo de verificação

Você pode exibir o modo atual de seu SQL Server agente IaaS usando Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Atualizar para completo  

SQL Server VMs que têm a extensão de IaaS *leve* instalada podem atualizar o modo para _completo_ usando o portal do Azure, o CLI do Azure ou Azure PowerShell. As VMs do SQL Server no modo _NoAgent_ podem ser atualizadas para _completo_ depois que o sistema operacional é atualizado para o Windows 2008 R2 e superior. Não é possível fazer downgrade – para isso, você precisará [cancelar o registro](#unregister-from-rp) da VM do SQL Server do provedor de recursos da VM do SQL. Isso removerá o **recurso** da _máquina virtual do SQL_ , mas não excluirá a máquina virtual em si. 


### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso [máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selecione sua VM SQL Server e selecione **visão geral**. 
1. Para VMs do SQL Server com o modo NoAgent ou IaaS leve, selecione a mensagem **Somente atualizações de edição e tipo de licença estão disponíveis com a extensão de IaaS do SQL**.

   ![Seleções para alterar o modo usando o portal](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Marque a caixa de seleção **Eu concordo em reiniciar o serviço SQL Server na máquina virtual** e selecione **Confirmar** para atualizar o modo IaaS para completo. 

    ![Marque a caixa para concordar em reiniciar o serviço SQL Server na máquina virtual](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Execute o seguinte snippet de código da CLI do Azure:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Execute o seguinte trecho de código de Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o status de registro
Você pode verificar se sua VM de SQL Server já foi registrada com o provedor de recursos de VM do SQL usando o portal do Azure, o CLI do Azure ou Azure PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Vá para suas [VMs SQL Server](manage-sql-vm-portal.md).
1. Selecione a VM do SQL Server na lista. Se a VM do SQL Server não estiver listada aqui, provavelmente ela não foi registrada com o provedor de recursos de VM do SQL. 
1. Ver o valor em **Status**. Se o **Status** é **Bem-sucedido** , a VM do SQL Server foi registrada com êxito no provedor de recursos da VM do SQL. 

   ![Verificar o status com o registro de RP do SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Linha de comando

Verifique o status atual de registro da VM SQL Server usando CLI do Azure ou Azure PowerShell. `ProvisioningState` mostrará `Succeeded` se o registro foi bem-sucedido. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que a VM do SQL Server não foi registrada com o provedor de recursos. 


## <a name="unregister-from-rp"></a>Cancelar o registro do RP

Para cancelar o registro da VM SQL Server com o provedor de recursos de VM do SQL, exclua o *recurso* de máquina virtual do SQL usando o portal do Azure ou CLI do Azure. Excluir o *recurso* de máquina virtual do SQL não exclui a VM SQL Server. No entanto, tenha cuidado e siga as etapas com cuidado porque é possível excluir inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O cancelamento do registro da máquina virtual do SQL com o provedor de recursos da VM do SQL é necessário para fazer o downgrade completo do modo de gerenciamento. 

### <a name="azure-portal"></a>Portal do Azure

Para cancelar o registro da VM do SQL Server com o provedor de recursos usando o portal do Azure, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Navegue até o recurso de VM do SQL. 
  
   ![Recurso de máquinas virtuais do SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selecione **Excluir**. 

   ![Selecione Excluir na parte superior da navegação](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual do SQL e **desmarque a caixa de seleção ao lado da máquina virtual**.

   ![Desmarque a VM para impedir a exclusão da máquina virtual real e, em seguida, selecione Excluir para continuar com a exclusão do recurso de VM do SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Falha ao desmarcar a caixa de seleção ao lado do nome da máquina virtual *excluirá* a máquina virtual por completo. Desmarque a caixa de seleção para cancelar o registro da VM do SQL Server do provedor de recursos, mas *não exclua a máquina virtual real*. 

1. Selecione **excluir** para confirmar a exclusão do *recurso* de máquina virtual do SQL e não a VM SQL Server. 

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para cancelar o registro da VM SQL Server do provedor de recursos com CLI do Azure, use o comando [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Isso removerá o SQL Server *recurso* da VM, mas não excluirá a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para cancelar o registro da VM SQL Server do provedor de recursos com Azure PowerShell, use o comando [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Isso removerá o SQL Server *recurso* da VM, mas não excluirá a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)  
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
