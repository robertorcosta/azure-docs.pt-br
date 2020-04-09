---
title: Registre-se no provedor de recursos SQL VM
description: Registre sua máquina virtual Do Azure SQL Server com o provedor de recursos SQL VM para habilitar recursos para VMs sql server implantados fora do Azure Marketplace, bem como conformidade e melhor capacidade de gerenciamento.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985381"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registre uma máquina virtual sql server no Azure com o provedor de recursos SQL VM

Este artigo descreve como registrar sua máquina virtual SQL Server (VM) no Azure com o provedor de recursos SQL VM. O registro com o provedor de recursos cria o _recurso_ **de máquina virtual SQL** dentro de sua assinatura, que é um recurso separado do recurso da máquina virtual. O descadastramento do VM do Servidor SQL do provedor de recursos removerá o _recurso_ da **máquina virtual SQL,** mas não soltará a máquina virtual real. 

A implantação de uma imagem do SQL Server VM Azure Marketplace através do portal Azure registra automaticamente o VM do SQL Server com o provedor de recursos. No entanto, se você optar por instalar o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual Azure a partir de um VHD personalizado, você deve registrar seu VM do SQL Server com o provedor de recursos para:

- **Benefícios do recurso**: Registrar sua VM do SQL Server com o provedor de recursos desbloqueia [patches automatizados,](virtual-machines-windows-sql-automated-patching.md) [backup automatizado,](virtual-machines-windows-sql-automated-backup-v2.md)bem como recursos de monitoramento e capacidade de gerenciamento. Também desbloqueia a flexibilidade [de licenciamento](virtual-machines-windows-sql-ahb.md) e [edição.](virtual-machines-windows-sql-change-edition.md) Anteriormente, esses recursos só estavam disponíveis para imagens VM do SQL Server implantadas no Azure Marketplace. 

- **Conformidade**: O registro com o provedor de recursos SQL VM oferece um método simplificado de cumprir o requisito para notificar a Microsoft de que o Azure Hybrid Benefit foi ativado conforme especificado nos termos do produto. Esse processo nega a necessidade de gerenciar formulários de registro de licenciamento para cada recurso.  

- **Gestão gratuita**: Registrar-se com o provedor de recursos SQL VM nos três modos de gerenciamento é completamente gratuito. Não há custo adicional associado ao provedor de recursos ou com a alteração dos modos de gerenciamento. 

- **Gerenciamento simplificado de licenças**: O registro com o provedor de recursos SQL VM simplifica o gerenciamento de licenças do SQL Server e permite identificar rapidamente vMs do SQL Server com o Azure Hybrid Benefit ativado usando o [portal Azure,](virtual-machines-windows-sql-manage-portal.md)o Az CLI ou powerShell: 

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Para utilizar o provedor de recursos SQL VM, você deve primeiro [registrar sua assinatura com o provedor de recursos](#register-subscription-with-rp), o que dá ao provedor de recursos a capacidade de criar recursos dentro dessa assinatura específica.

Para obter mais informações sobre os benefícios do uso do provedor de recursos SQL VM, assista a este vídeo [do canal9:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Beneficie-se do Provedor de Recursos SQL VM ao instalar o SQL Server no Azure - Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Pré-requisitos

Para registrar seu VM do SQL Server com o provedor de recursos, você precisará: 

- Uma [assinatura do Azure.](https://azure.microsoft.com/free/)
- Um VM do Azure Resource Model [SQL Server](virtual-machines-windows-portal-sql-server-provision.md) foi implantado na nuvem pública ou do Governo Azure. 
- A versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) ou [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modos de gestão

Se a [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) ainda não tiver sido instalada, o registro no provedor de recursos SQL VM instala automaticamente a extensão SQL Server IaaS em um dos três modos de gerenciamento, especificados durante o processo de registro. Sem especificar o modo de gerenciamento, instalará a extensão SQL IaaS no modo de gerenciamento completo.  

Se a extensão SQL IaaS já tiver sido instalada manualmente, ela já está em modo de gerenciamento completo, e o registro com o provedor de recursos em modo completo não reiniciará o serviço sql server.

Os três modos de gestão são:

- **O** modo leve não requer a reinicialização do SQL Server, mas suporta apenas a alteração do tipo de licença e edição do SQL Server. Use essa opção para VMs do Servidor SQL com várias instâncias ou participe de uma instância de cluster de failover (FCI). Não há impacto na memória ou na CPU ao usar o modo leve, e não há custo associado. Recomenda-se registrar seu VM sql server no modo leve primeiro e, em seguida, atualizar para o modo Completo durante uma janela de manutenção programada.  

- **O** modo completo oferece todas as funcionalidades, mas requer uma reinicialização das permissões do SQL Server e do administrador do sistema. Esta é a opção que é instalada por padrão ao instalar manualmente a extensão SQL IaaS. Use-o para gerenciar um VM do Servidor SQL com uma única instância. O modo completo instala dois serviços windows que têm um impacto mínimo na memória e na CPU - estes podem ser monitorados através do gerenciador de tarefas. Não há custo associado ao uso do modo de gerenciamento completo. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalados no Windows Server 2008. Não há impacto na memória ou na CPU ao usar o modo NoAgent. Não há custo associado ao uso do modo de gerenciamento NoAgent. 

Você pode visualizar o modo atual do seu agente SQL Server IaaS usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registre assinatura com RP

Para registrar sua VM do SQL Server com o provedor de recursos SQL VM, você deve primeiro registrar sua assinatura com o provedor de recursos. Isso dá ao provedor de recursos SQL VM a capacidade de criar recursos dentro de sua assinatura.  Você pode fazê-lo usando o portal Azure, o Azure CLI ou powerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal Azure e vá para **Todos os Serviços**. 
1. Vá para **Assinaturas** e selecione a assinatura de interesse.  
1. Na página **Assinaturas,** vá para **provedores de recursos**. 
1. Digite **sql** no filtro para trazer os provedores de recursos relacionados ao SQL. 
1. Selecione **Registrar**, **Recadastrar**ou Cancelar o **registro** para o provedor **Microsoft.SqlVirtualMachine,** dependendo da ação desejada. 

![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comando

Registre seu provedor de recursos SQL VM em sua assinatura do Azure usando a Cli az ou o PowerShell. 

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registre SQL VM com RP 

### <a name="lightweight-management-mode"></a>Modo de gestão leve

Se a extensão do [agente Iaas do Servidor SQL](virtual-machines-windows-sql-server-agent-extension.md) não tiver sido instalada na VM, então a recomendação é registrar-se no provedor de recursos SQL VM no modo leve. Isso instalará a extensão SQL IaaS no [modo leve](#management-modes) e impedirá que o serviço sql server seja reiniciado. Em seguida, você pode atualizar para o modo completo a qualquer momento, mas ao fazê-lo reiniciará o serviço do SQL Server, por isso é recomendável esperar até uma janela de manutenção programada. 

Forneça o tipo de licença do SQL Server`PAYG`como pay-as-you-go (`AHUB`) para pagar por uso,`DR`Azure Hybrid Benefit ( ) para usar sua própria licença, ou recuperação de desastres ( ) para ativar a [licença de réplica dr gratuita](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure).

Cluster De failover As instâncias e implantações em várias instâncias só podem ser registradas no provedor de recursos SQL VM no modo leve. 

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Registre o VM do SQL Server no modo leve com o Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registre o SQL Server VM no modo leve com o PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modo de gestão completa


Se a extensão SQL IaaS já tiver sido instalada manualmente na VM, então você poderá registrar o VM do SQL Server em modo completo sem reiniciar o serviço do SQL Server. **No entanto, se a extensão SQL IaaS não tiver sido instalada, o registro no modo completo instalará a extensão SQL IaaS no modo completo e reiniciará o serviço SQL Server. Por favor, prossiga com cautela.**


Para registrar seu VM do Servidor SQL diretamente no modo completo (e possivelmente reiniciar o serviço do SQL Server), use o seguinte comando PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gerenciamento noAgent 

O SQL Server 2008 e o 2008 R2 instalados no Windows Server 2008 (_não R2_) podem ser registrados no provedor de recursos SQL VM no [modo NoAgent](#management-modes). Essa opção garante a conformidade e permite que o VM do SQL Server seja monitorado no portal Azure com funcionalidade limitada.

Especifique `AHUB`, `PAYG` `DR` ou como o **sqlLicenseType,** `SQL2008-WS2008` e ou `SQL2008R2-WS2008`como o **sqlImageOffer**. 

Para registrar o seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use o seguinte trecho de código Az CLI ou PowerShell: 


# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Registre seu VM SQL Server 2008 no modo NoAgent com o Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registre seu SQL Server 2008 R2 VM no modo NoAgent com o Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registre o VM SQL Server 2008 no modo NoAgent com o PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registre o SQL Server 2008 R2 VM no modo NoAgent com o PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Atualize para o modo de gerenciamento completo 

As VMs do SQL Server que tiverem a extensão IaaS *leve* instalada podem atualizar o modo para _a totalidade_ usando o portal Azure, o Az CLI ou o PowerShell. As VMs do SQL Server no modo _NoAgent_ podem ser atualizadas para _a íntegra_ depois que o sistema operacional for atualizado para o Windows 2008 R2 e acima. Não é possível fazer o downgrade - para isso, você precisará cancelar o [registro](#unregister-vm-from-rp) do VM do SQL Server do provedor de recursos SQL VM. Isso removerá o _recurso_da **máquina virtual SQL,** mas não excluirá a máquina virtual real. 

Você pode visualizar o modo atual do seu agente SQL Server IaaS usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Para atualizar o modo de agente para o máximo: 


### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso [de máquinas virtuais SQL.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Selecione a máquina virtual do SQL Server e selecione **Visão geral**. 
1. Para VMs do Servidor SQL com o modo NoAgent ou IaaS leve, selecione o tipo de licença Única e as atualizações de edição estão disponíveis com a mensagem **de extensão SQL IaaS.**

   ![Seleções para alterar o modo a partir do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selecione o **que eu concordo em reiniciar o serviço SQL Server na** caixa de seleção da máquina virtual e, em seguida, **selecione Confirmar** para atualizar o modo IaaS para o máximo. 

    ![Caixa de seleção para concordar em reiniciar o serviço SQL Server na máquina virtual](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comando

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Execute o seguinte trecho de código Az CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Execute o seguinte trecho de código PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar a situação do registro
Você pode verificar se o VM do SQL Server já foi registrado no provedor de recursos SQL VM usando o portal Azure, o Azure CLI ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Vá para suas [máquinas virtuais SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selecione seu VM do Servidor SQL na lista. Se o VM do SQL Server não estiver listado aqui, provavelmente não foi registrado no provedor de recursos SQL VM. 
1. Exibir o valor em **Status**. Se **o Status** for bem **sucedido,** o VM do Servidor SQL foi registrado com sucesso no provedor de recursos SQL VM. 

![Verificar status com registro de RP SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Linha de comando

Verifique o status de registro atual do SQL Server VM usando a Az CLI ou o PowerShell. `ProvisioningState`mostrará `Succeeded` se o registro foi bem sucedido. 

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que o VM do Servidor SQL não foi registrado no provedor de recursos. 


## <a name="unregister-vm-from-rp"></a>Não registre VM de RP

Para cancelar o registro do VM do SQL Server com o provedor de recursos SQL VM, exclua o *recurso* SQL Virtual Machine usando o portal Azure ou o Azure CLI. A exclusão do *recurso* SQL Virtual Machine não exclui o VM do Servidor SQL. No entanto, tenha cuidado e siga os passos com cuidado, pois é possível excluir inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O descadastramento do VM SQL com o provedor de recursos SQL VM é necessário para fazer o downgrade do modo de gerenciamento de cheio. 

### <a name="azure-portal"></a>Portal do Azure

Para cancelar o registro do VM do SQL Server com o provedor de recursos usando o portal Azure, siga estas etapas:

1. Inscreva-se no [portal Azure](https://portal.azure.com).
1. Navegue até o recurso VM do SQL Server. 
  
   ![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selecione **Excluir**. 

   ![Excluir provedor de recursos SQL VM](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual SQL e **limpe a caixa de seleção ao lado da máquina virtual**.

   ![Excluir provedor de recursos SQL VM](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se não limpar a caixa de seleção ao lado do nome da máquina *virtual, excluirá* totalmente a máquina virtual. Limpe a caixa de seleção para cancelar o registro do VM do Servidor SQL do provedor de recursos, mas *não exclua a máquina virtual real*. 

1. Selecione **Excluir** para confirmar a exclusão do *recurso*da máquina virtual SQL e não a máquina virtual SQL Server. 

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para desregistrar sua máquina virtual SQL Server do provedor de recursos com o Azure CLI, use o comando [az sql vm delete.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Isso removerá o *recurso* da máquina virtual SQL Server, mas não excluirá a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para desregistrar sua máquina virtual SQL Server do provedor de recursos com o Azure CLI, use o comando [New-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Isso removerá o *recurso* da máquina virtual SQL Server, mas não excluirá a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitações

O provedor de recursos SQL VM só suporta:
- VMs do SQL Server implantados através do Gerenciador de Recursos do Azure. As VMs do SQL Server implantadas através do modelo clássico não são suportadas. 
- VMs do SQL Server implantados na nuvem pública ou do Governo Azure. Não são suportadas implantações para outras nuvens privadas ou governamentais. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**Devo registrar meu VM do Servidor SQL provisionado a partir de uma imagem do SQL Server no Azure Marketplace?**

Não. A Microsoft registra automaticamente VMs provisionados a partir das imagens do SQL Server no Azure Marketplace. O registro no provedor de recursos SQL VM só é necessário se a VM *não* foi provisionada a partir das imagens do SQL Server no Azure Marketplace e o SQL Server foi auto-instalado.

**O provedor de recursos da VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registrar suas VMs do SQL Server com o provedor de recursos SQL VM se não usarem uma imagem do SQL Server do Azure Marketplace e, em vez disso, o SQL Server auto-instalado, ou se trouxeram seu VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (Direct, Enterprise Agreement e Cloud Solution Provider) podem se registrar no provedor de recursos SQL VM.

**Devo me registrar no provedor de recursos SQL VM se o meu SQL Server VM já tiver a extensão SQL Server IaaS instalada?**

Se o VM do SQL Server estiver autoinstalado e não provisionado das imagens do SQL Server no Azure Marketplace, você deverá se registrar no provedor de recursos SQL VM mesmo se você instalou a extensão SQL Server IaaS. O registro no provedor de recursos SQL VM cria um novo recurso do tipo Microsoft.SqlVirtualMachines. A instalação da extensão SQL Server IaaS não cria esse recurso.

**O que é o modo de gerenciamento padrão ao se registrar no provedor de recursos SQL VM?**

O modo de gerenciamento padrão quando você se registra no provedor de recursos SQL VM está *cheio*. Se a propriedade de gerenciamento do SQL Server não for definida quando você se registrar no provedor de recursos SQL VM, o modo será definido como gerenciamento completo e seu serviço sql server será reiniciado. Recomenda-se registrar-se no provedor de recursos SQL VM no modo leve primeiro e, em seguida, atualizar para o máximo durante uma janela de manutenção. 

**Quais são os pré-requisitos para se registrar no provedor de recursos SQL VM?**

Não há pré-requisitos para se registrar no provedor de recursos SQL VM no modo leve ou no modo sem agente. O pré-requisito para se registrar no provedor de recursos SQL VM em modo completo é ter a extensão SQL Server IaaS instalada na VM, caso contrário o serviço SQL Server será reiniciado. 

**Posso me registrar no provedor de recursos SQL VM se eu não tiver a extensão SQL Server IaaS instalada na VM?**

Sim, você pode se registrar com o provedor de recursos SQL VM no modo de gerenciamento leve se você não tiver a extensão SQL Server IaaS instalada na VM. No modo leve, o provedor de recursos SQL VM usará um aplicativo de console para verificar a versão e a edição da instância do SQL Server. 

O modo de gerenciamento SQL padrão ao se registrar no provedor de recursos SQL VM é _Full_. Se a propriedade SQL Management não for definida ao se registrar no provedor de recursos SQL VM, o modo será definido como Gerenciabilidade Completa. Recomenda-se registrar-se no provedor de recursos SQL VM no modo leve primeiro e, em seguida, atualizar para o máximo durante uma janela de manutenção. 

**O registro no provedor de recursos SQL VM instalará um agente na minha VM?**

Não. O registro no provedor de recursos SQL VM criará apenas um novo recurso de metadados. Não instalará um agente na VM.

A extensão SQL Server IaaS é necessária apenas para permitir a capacidade de gerenciamento total. O upgrade do modo de capacidade de gerenciamento de leve para completo instalará a extensão SQL Server IaaS e reiniciará o SQL Server.

**O registro com o provedor de recursos SQL Server VM reiniciará o SQL Server na minha VM?**

Depende do modo especificado durante o registro. Se o modo leve ou NoAgent for especificado, o serviço SQL Server não será reiniciado. No entanto, especificar o modo de gerenciamento como completo ou deixar o modo de gerenciamento em branco instalará a extensão SQL IaaS no modo de gerenciamento completo, o que fará com que o serviço SQL Server seja reiniciado. 

**Qual é a diferença entre os modos de gerenciamento leve e sem agente ao se registrar no provedor de recursos SQL VM?** 

O modo de gerenciamento sem agente está disponível apenas para o SQL Server 2008 e o SQL Server 2008 R2 no Windows Server 2008. É o único modo de gerenciamento disponível para essas versões. Para todas as outras versões do SQL Server, os dois modos de capacidade de gerenciamento disponíveis são leves e completos. 

O modo sem agente requer que as propriedades de versão e edição do SQL Server sejam definidas pelo cliente. O modo leve consulta a VM para encontrar a versão e a edição da instância do SQL Server.

**Posso me registrar no provedor de recursos SQL VM sem especificar o tipo de licença do SQL Server?**

Não. O tipo de licença do SQL Server não é uma propriedade opcional quando você está se registrando no provedor de recursos SQL VM. Você tem que definir o tipo de licença do SQL Server como pay-as-you-go ou Azure Hybrid Benefit ao se registrar no provedor de recursos SQL VM em todos os modos de gerenciamento (sem agente, leve e completo).

**Posso atualizar a extensão SQL Server IaaS do modo sem agente para o modo completo?**

Não. O upgrade do modo de capacidade de gerenciamento para completo ou leve não está disponível para o modo sem agente. Esta é uma limitação técnica do Windows Server 2008. Você precisará atualizar o SO primeiro para o Windows Server 2008 R2 ou superior, e então você poderá atualizar para o modo de gerenciamento completo. 

**Posso atualizar a extensão SQL Server IaaS do modo leve para o modo completo?**

Sim. A atualização do modo de capacidade de gerenciamento de leve para total é suportada via PowerShell ou portal Azure. Ele requer reiniciar o serviço do SQL Server.

**Posso fazer o downgrade da extensão SQL Server IaaS do modo completo para o modo de gerenciamento sem agente ou leve?**

Não. Não é suportado o modo de gerenciamento de extensão SQL Server IaaS. O modo de gerenciamento não pode ser rebaixado do modo completo para o modo leve ou sem agente, e não pode ser rebaixado do modo leve para o modo sem agente. 

Para alterar o modo de gerenciamento da capacidade de gerenciamento total, [não registre](#unregister-vm-from-rp) a máquina virtual do SQL Server do provedor de recursos SQL Server, soltando o *recurso* do SQL Server e reregistrando o VM do SQL Server com o provedor de recursos SQL VM novamente em um modo de gerenciamento diferente.

**Posso me registrar no provedor de recursos SQL VM do portal Azure?**

Não. O registro no provedor de recursos SQL VM não está disponível no portal azure. O registro no provedor de recursos SQL VM só é suportado com o Azure CLI ou PowerShell. 

**Posso registrar uma VM com o provedor de recursos SQL VM antes que o SQL Server seja instalado?**

Não. Uma VM deve ter pelo menos uma instância do SQL Server (Database Engine) para se registrar com sucesso no provedor de recursos SQL VM. Se não houver nenhuma instância do SQL Server na VM, o novo recurso Microsoft.SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com o provedor de recursos SQL VM se houver várias instâncias do SQL Server?**

Sim. O provedor de recursos SQL VM registrará apenas uma instância do SQL Server (Database Engine). O provedor de recursos SQL VM registrará a instância padrão do SQL Server no caso de várias instâncias. Se não houver uma instância padrão, então apenas o registro no modo leve é suportado. Para atualizar do modo de gerenciamento leve para completo, a instância padrão do SQL Server deve existir ou a VM deve ter apenas uma ocorrência chamada SQL Server.

**Posso registrar uma instância de cluster de failover do SQL Server com o provedor de recursos SQL VM?**

Sim. As instâncias de cluster de failover do SQL Server em uma VM Do Azure podem ser registradas no provedor de recursos SQL VM no modo leve. No entanto, as instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com o provedor de recursos SQL VM se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não há restrições para registrar uma instância do SQL Server em uma VM Azure com o provedor de recursos SQL VM se você estiver participando de uma configuração de grupo de disponibilidade Always On.

**Qual é o custo para se registrar no provedor de recursos SQL VM ou com a atualização para o modo de gerenciamento completo?**
Nenhum. Não há taxa associada ao registro no provedor de recursos SQL VM ou ao uso de qualquer um dos três modos de gerenciamento. O gerenciamento do VM do SQL Server com o provedor de recursos é completamente gratuito. 

**Qual é o impacto de desempenho do uso dos diferentes modos de capacidade de gerenciamento?**
Não há impacto ao usar os modos *NoAgent* e de gerenciamento *leve.* Há um impacto mínimo ao usar o modo de gerenciamento *completo* de dois serviços instalados no Sistema Operacional. Estes podem ser monitorados via gerenciador de tarefas e vistos no console integrado do Windows Services. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery`(Nome da `Microsoft SQL Server IaaS Query Service`exibição - )
- `SQLIaaSExtension`(Nome da `Microsoft SQL Server IaaS Agent`exibição - )


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
