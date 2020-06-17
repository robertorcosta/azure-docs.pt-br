---
title: Registrar com o provedor de recursos de VM do SQL
description: Registre sua máquina virtual do Azure SQL Server com o provedor de recursos da VM do SQL para habilitar recursos para VMs do SQL Server implantadas fora do Azure Marketplace, bem como conformidade e capacidade de gerenciamento aprimorada.
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
ms.openlocfilehash: af2d23104f07991fc9833951bb4e2395d39be9b3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039827"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registre uma máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registrar sua VM (máquina virtual) do SQL Server no Azure com o provedor de recursos de VM do SQL. O registro com o provedor de recursos cria o **recurso** da _máquina virtual SQL_ em sua assinatura, que é um recurso separado do recurso de máquina virtual. Cancelar o registro de sua VM do SQL Server do provedor de recursos removerá o **recurso** da _máquina virtual do SQL_, mas não removerá a máquina virtual real. 

Implantar uma imagem do Azure Marketplace da VM do SQL Server por meio do portal do Azure registra automaticamente uma VM do SQL Server no provedor de recursos. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual do Azure de um VHD personalizado, deverá registrar sua VM do SQL Server com o provedor de recursos para:

- **Benefícios do recurso**: registrar sua VM do SQL Server com o provedor de recursos desbloqueia [aplicação de patch automatizada](automated-patching.md), [backup automatizado](automated-backup.md), bem como recursos de monitoramento e gerenciamento. Também desbloqueia o [licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) e a flexibilidade da [edição](change-sql-server-edition.md). Anteriormente, esses recursos estavam disponíveis apenas para imagens de VM do SQL Server implantadas do Azure Marketplace. 

- **Conformidade**: O registro com o provedor de recursos de VM do SQL oferece um método simplificado para atender à necessidade de notificar a Microsoft de que o Benefício Híbrido do Azure foi habilitado conforme especificado nos termos do produto. Esse processo nega a necessidade de gerenciar os formulários de registro de licenciamento para cada recurso.  

- **Gerenciamento gratuito**:  o registro com o provedor de recursos de VM do SQL nos três modos de gerenciamento é completamente gratuito. Não há custo adicional associado ao provedor de recursos ou à alteração dos modos de gerenciamento. 

- **Gerenciamento de licenças simplificado**: o registro com o provedor de recursos de VM do SQL simplifica o gerenciamento de licenças do SQL Server e permite que você identifique rapidamente VMs do SQL Server com o Benefício Híbrido do Azure habilitado usando o [portal do Azure](manage-sql-vm-portal.md), a CLI do Azure ou o PowerShell: 

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

Para utilizar o provedor de recursos de VM do SQL, primeiro você deve [registrar sua assinatura com o provedor de recursos](#register-subscription-with-rp), que fornece ao provedor de recursos a capacidade de criar recursos dentro dessa assinatura específica.

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [VM do SQL Server](create-sql-vm-portal.md) do Modelo de Recurso do Azure implantado na nuvem pública ou no Azure Government. 
- A versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) ou do [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modos de gerenciamento

Se a [extensão de IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md) ainda não tiver sido instalada, o registro com o provedor de recursos da VM do SQL a instalará automaticamente a extensão de IaaS do SQL Server em um dos três modos de gerenciamento especificados durante o processo de registro. Não especificar o modo de gerenciamento instalará a extensão IaaS do SQL no modo de gerenciamento completo.  

Se a extensão de IaaS do SQL já tiver sido instalada manualmente, ela já estará no modo de gerenciamento completo e o registro com o provedor de recursos no modo completo não reiniciará o serviço SQL Server.

Os três modos de gerenciamento são:

- O modo **Leve** não requer a reinicialização de SQL Server, mas dá suporte apenas à alteração do tipo de licença e da edição do SQL Server. Use essa opção para VMs do SQL Server com várias instâncias ou para participar de uma FCI (instância de cluster de failover). Não há nenhum impacto à memória ou à CPU ao usar o modo leve e não há nenhum custo associado. É recomendável registrar sua VM do SQL Server no modo leve primeiro e, em seguida, atualizar para o modo completo durante uma janela de manutenção agendada.  

- O modo **Completo** fornece todas as funcionalidades, mas requer uma reinicialização das permissões do SQL Server e administrador do sistema. Essa é a opção instalada por padrão ao instalar a extensão de IaaS do SQL manualmente. Use-a para gerenciar uma VM do SQL Server com uma instância. O modo completo instala dois serviços do Windows que têm um impacto mínimo sobre a memória e a CPU, que podem ser monitoradas por meio do gerenciador de tarefas. Não há nenhum custo associado ao uso do modo de gerenciamento completo. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalados no Windows Server 2008. Não há nenhum impacto à memória ou na CPU ao usar o modo NoAgent. Não há nenhum custo associado ao uso do modo de gerenciamento NoAgent. 

Você pode ver o modo atual do SQL Server IaaS Agent usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrar assinatura com RP

Para registrar sua VM do SQL Server no provedor de recursos da VM do SQL, registre o provedor de recursos em sua assinatura. Isso fornece ao provedor de recursos da VM do SQL a capacidade de criar recursos em sua assinatura.  É possível localizar a URL usando o portal do Azure, a CLI do Azure ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal do Azure e navegue até **Todos os Serviços**. 
1. Vá para **Assinaturas** e selecione a assinatura de seu interesse.  
1. Na página **Assinaturas**, vá para **Provedores de recursos**. 
1. Insira **sql** no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione **Registrar**, **Registrar novamente** ou **Cancelar registro** de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

![Modificar o provedor](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comando

Registre seu provedor de recursos de VM do SQL em sua assinatura do Azure usando a CLI do Azure ou o PowerShell. 

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

## <a name="register-sql-vm-with-rp"></a>Registrar a VM do SQL com RP 

### <a name="lightweight-management-mode"></a>Modo de gerenciamento Leve

Se a [Extensão do SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) não tiver sido instalada na VM, a recomendação será registrar-se com o provedor de recursos da VM do SQL no modo leve. Isso instalará a extensão IaaS do SQL no [modo leve](#management-modes) e impedirá que o serviço do SQL Server seja reiniciado. Em seguida, você pode atualizar para o modo completo a qualquer momento, mas isso reiniciará o serviço de SQL Server, assim, é recomendável aguardar até uma janela de manutenção agendada. 

Forneça o tipo de licença do SQL Server como pago conforme o uso (`PAYG`) para pagar por uso, Benefício Híbrido do Azure (`AHUB`) para usar sua licença ou recuperação de desastre (`DR`) para ativar a [licença de réplica de DR gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

As instâncias de cluster de failover e as implantações de várias instâncias só podem ser registradas com o provedor de recursos de VM do SQL no modo leve. 

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Registre a VM do SQL Server no modo leve com a CLI do Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registre a VM do SQL Server no modo leve com o PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Pacote de gerenciamento completo


Se a extensão de IaaS do SQL já tiver sido instalada na VM manualmente, você poderá registrar a VM do SQL Server no modo completo sem reiniciar o serviço do SQL Server. **No entanto, se a extensão de IaaS do SQL não tiver sido instalada, o registro no modo completo instalará a extensão de IaaS do SQL no modo completo e reiniciará o serviço do SQL Server. Continue com cuidado.**


Para registrar sua VM do SQL Server diretamente no modo completo (e possivelmente reiniciar o serviço de SQL Server), use o seguinte comando do PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gerenciamento NoAgent 

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 (_não R2_) podem ser registrados com o provedor de recursos de VM do SQL no modo [NoAgent](#management-modes). Essa opção garante a conformidade e permite que a VM do SQL Server seja monitorada no portal do Azure com funcionalidade limitada.

Especifique `AHUB`, `PAYG` ou `DR` como o **sqlLicenseType** e `SQL2008-WS2008` ou `SQL2008R2-WS2008` como **sqlImageOffer**. 

Para registrar seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use os seguintes snippets de código da CLI do Azure ou do PowerShell: 


# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Registre sua VM do SQL Server 2008 no modo NoAgent com a CLI do Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registre sua VM do SQL Server 2008 R2 no modo NoAgent com a CLI do Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrar a VM do SQL Server 2008 no modo NoAgent com o PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrar a VM do SQL Server 2008 R2 no modo NoAgent com o PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Atualizar para o modo de gerenciamento completo 

VMs do SQL Server que têm a extensão de IaaS *leve* instalada podem atualizar o modo para _completo_ usando o portal do Azure, a CLI do Azure ou o PowerShell. As VMs do SQL Server no modo _NoAgent_ podem ser atualizadas para _completo_ depois que o sistema operacional é atualizado para o Windows 2008 R2 e superior. Não é possível fazer downgrade – para isso, você precisará [cancelar o registro](#unregister-vm-from-rp) da VM do SQL Server do provedor de recursos da VM do SQL. Isso removerá o **recurso** da _máquina virtual do SQL_, mas não excluirá a máquina virtual em si. 

Você pode ver o modo atual do SQL Server IaaS Agent usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Para atualizar o modo do agente para completo: 


### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso [máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selecione sua máquina virtual SQL Server e selecione **Visão geral**. 
1. Para VMs do SQL Server com o modo NoAgent ou IaaS leve, selecione a mensagem **Somente atualizações de edição e tipo de licença estão disponíveis com a extensão de IaaS do SQL**.

   ![Seleções para alterar o modo usando o portal](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Marque a caixa de seleção **Eu concordo em reiniciar o serviço SQL Server na máquina virtual** e selecione **Confirmar** para atualizar o modo IaaS para completo. 

    ![Marque a caixa para concordar em reiniciar o serviço SQL Server na máquina virtual](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comando

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)

Execute o seguinte snippet de código da CLI do Azure:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Execute o seguinte snippet de código do PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o status de registro
Você pode verificar se a sua VM do SQL Server já foi registrada com o provedor de recursos de VM do SQL usando o portal do Azure, a CLI do Azure ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Vá para suas [máquinas virtuais do SQL Server](manage-sql-vm-portal.md).
1. Selecione a VM do SQL Server na lista. Se a VM do SQL Server não estiver listada aqui, provavelmente ela não foi registrada com o provedor de recursos de VM do SQL. 
1. Ver o valor em **Status**. Se o **Status** é **Bem-sucedido**, a VM do SQL Server foi registrada com êxito no provedor de recursos da VM do SQL. 

![Verificar o status com o registro de RP do SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Linha de comando

Verifique o status atual do registro da VM do SQL Server usando a CLI do Azure ou o PowerShell. `ProvisioningState` mostrará `Succeeded` se o registro foi bem-sucedido. 

# <a name="az-cli"></a>[CLI do Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que a VM do SQL Server não foi registrada com o provedor de recursos. 


## <a name="unregister-vm-from-rp"></a>Cancelar o registro da VM do RP

Para cancelar o registro da VM do SQL Server com o provedor de recursos da VM do SQL, exclua o *recurso* da Máquina Virtual do SQL usando o portal do Azure ou a CLI do Azure. Excluir o *recurso* da Máquina Virtual do SQL não exclui a VM do SQL Server. No entanto, tenha cuidado e siga as etapas com cuidado porque é possível excluir inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O cancelamento do registro da VM do SQL com o provedor de recursos da VM do SQL é necessário para fazer downgrade completo do modo de gerenciamento. 

### <a name="azure-portal"></a>Portal do Azure

Para cancelar o registro da VM do SQL Server com o provedor de recursos usando o portal do Azure, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Navegue até o recurso de VM do SQL Server. 
  
   ![Recurso de máquinas virtuais do SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selecione **Excluir**. 

   ![Excluir o provedor de recursos de VM do SQL](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual do SQL e **desmarque a caixa de seleção ao lado da máquina virtual**.

   ![Excluir o provedor de recursos de VM do SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Falha ao desmarcar a caixa de seleção ao lado do nome da máquina virtual *excluirá* a máquina virtual por completo. Desmarque a caixa de seleção para cancelar o registro da VM do SQL Server do provedor de recursos, mas *não exclua a máquina virtual real*. 

1. Selecione **Excluir** para confirmar a exclusão do *recurso* da máquina virtual SQL, e não da máquina virtual SQL Server. 

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para cancelar o registro de sua máquina virtual SQL Server do provedor de recursos com a CLI do Azure, use o comando [az sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete). Isso removerá o *recurso* da máquina virtual do SQL Server, mas não excluirá a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para cancelar o registro de sua máquina virtual SQL Server do provedor de recursos com a CLI do Azure, use o comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm). Isso removerá o *recurso* da máquina virtual do SQL Server, mas não excluirá a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitações

O provedor de recursos de VM do SQL só dá suporte a:
- VMs do SQL Server implantadas por meio do Azure Resource Manager. Não há suporte para as VMs do SQL Server implantadas por meio do modelo clássico. 
- VMs do SQL Server implantadas na nuvem pública ou do Azure Government. Não há suporte para implantações em outras nuvens privadas ou governamentais. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**Devo registrar minha VM do SQL Server provisionada de uma imagem do SQL Server no Azure Marketplace?**

Não. A Microsoft registra automaticamente as VMs provisionadas usando imagens do SQL Server no Azure Marketplace. O registro com o provedor de recursos da VM do SQL será necessário somente se a VM *não* tiver sido provisionada das imagens do SQL Server no Azure Marketplace e o SQL Server tiver sido autoinstalado.

**O provedor de recursos da VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes deverão registrar suas VMs SQL Server com o provedor de recursos de VM do SQL caso não tenham usado uma imagem de SQL Server do Azure Marketplace mas, em vez disso, o SQL Server autoinstalado ou se tiverem trazido o próprio VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (Direta, Contrato Enterprise e Provedor de Soluções na Nuvem) podem se registrar no provedor de recursos da VM do SQL.

**Devo registrar com o provedor de recursos de VM do SQL se minha VM de SQL Server já tem a extensão de IaaS do SQL Server instalada?**

Se a VM do SQL Server tiver sido autoinstalada e não for provisionada usando as imagens SQL Server no Azure Marketplace, você deverá se registrar no provedor de recursos da VM do SQL mesmo se tiver instalado a extensão de IaaS do SQL Server. O registro com o provedor de recursos de VM do SQL cria um recurso do tipo Microsoft.SqlVirtualMachines. Instalar a extensão de IaaS do SQL Server não cria esse recurso.

**Qual é o modo de gerenciamento padrão ao registrar-se com o provedor de recursos de VM do SQL?**

O modo de gerenciamento padrão quando você se registra no provedor de recursos de VM do SQL é *completo*. Se a propriedade de gerenciamento do SQL Server não estiver definida quando você se registrar no provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento total e o serviço do SQL Server será reiniciado. É recomendável registrar com o provedor de recursos de VM do SQL no modo leve primeiro e, em seguida, atualizar para completo durante uma janela de manutenção. 

**Quais são os pré-requisitos para registrar-se com o provedor de recursos de VM do SQL?**

Não há nenhum pré-requisito para registrar-se com o provedor de recursos de VM do SQL no modo leve ou no modo sem agente. O pré-requisito para registrar-se com o provedor de recursos de VM do SQL no modo completo é ter a extensão de IaaS do SQL Server instalada na VM, caso contrário, o serviço de SQL Server será reiniciado. 

**Posso registrar com o provedor de recursos de VM do SQL se eu não tenho a extensão de IaaS do SQL Server instalada na VM?**

Sim, você poderá registrar com o provedor de recursos de VM do SQL no modo de gerenciamento leve se não tiver a extensão de IaaS do SQL Server instalada na VM. No modo leve, o provedor de recursos da VM do SQL usará um aplicativo de console para verificar a versão e a edição da instância do SQL Server. 

O modo de gerenciamento SQL padrão ao registrar-se com o provedor de recursos de VM do SQL é _completo_. Se a propriedade de gerenciamento do SQL não for definida durante o registro com o provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento completa. É recomendável registrar com o provedor de recursos de VM do SQL no modo leve primeiro e, em seguida, atualizar para completo durante uma janela de manutenção. 

**O registro com o provedor de recursos da VM do SQL instalará um agente em minha VM?**

Não. O registro com o provedor de recursos da VM do SQL criará apenas um recurso de metadados. Ele não instalará um agente na VM.

A extensão de IaaS do SQL Server é necessária apenas para habilitar a capacidade de gerenciamento total. A atualização do modo de gerenciamento leve para o completo instalará a extensão de IaaS do SQL Server e reiniciará o SQL Server.

**O registro com o provedor de recursos de VM do SQL Server reinicia p SQL Server em minha VM?**

Depende do modo especificado durante o registro. Se o modo leve ou NoAgent for especificado, o serviço de SQL Server não será reiniciado. No entanto, especificar o modo de gerenciamento como completo ou deixar o modo de gerenciamento em branco instalará a extensão de IaaS do SQL no modo de gerenciamento completo, o que fará com que o serviço de SQL Server seja reiniciado. 

**Qual é a diferença entre os modos de gerenciamento leve e sem agente ao registrar-se com o provedor de recursos de VM do SQL?** 

O modo de gerenciamento sem agente está disponível somente para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. É o único modo de gerenciamento disponível para essas versões. Para todas as outras versões do SQL Server, os dois modos de gerenciamento disponíveis são leve e completo. 

O modo sem agente exige que as propriedades de edição e versão do SQL Server sejam definidas pelo cliente. O modo leve consulta a VM para localizar a versão e a edição da instância do SQL Server.

**Posso registrar com o provedor de recursos de VM do SQL sem especificar o tipo de licença do SQL Server?**

Não. O tipo de licença do SQL Server não é uma propriedade opcional quando você está se registrando com o provedor de recursos de VM do SQL. Você precisa definir o tipo de licença do SQL Server como pago conforme o uso ou Benefício Híbrido do Azure ao registrar-se com o provedor de recursos de VM do SQL em todos os modos de gerenciamento (sem agente, leve e completo).

**Posso atualizar a extensão de IaaS do SQL Server do modo sem agente para o modo completo?**

Não. A atualização do modo de gerenciamento para o modo completo ou leve não está disponível para o modo sem agente. Essa é uma limitação técnica do Windows Server 2008. Você precisará atualizar o sistema operacional primeiro para o Windows Server 2008 R2 ou superior e, em seguida, poderá atualizar para o modo de gerenciamento completo. 

**Posso atualizar a extensão de IaaS do SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gerenciamento de leve para completo tem suporte por meio do PowerShell ou do portal do Azure. Ela requer a reinicialização do serviço SQL Server.

**Posso fazer downgrade da extensão de IaaS do SQL Server do modo completo para o modo de gerenciamento sem agente ou leve?**

Não. Não há suporte para o downgrade do modo de gerenciamento de extensão de IaaS do SQL Server. Não é possível fazer downgrade do modo de gerenciamento completo para o modo leve ou sem agente e ele não pode ser desatualizado do modo leve para o modo sem agente. 

Para alterar o modo de gerenciamento da capacidade de gerenciamento total, [cancele o registro](#unregister-vm-from-rp) da máquina virtual SQL Server do provedor de recursos SQL Server descartando o *recursos* do SQL Server e registrando novamente a VM SQL Server com o provedor de recursos da VM do SQL em um modo de gerenciamento diferente.

**Posso me registrar com o provedor de recursos de VM do SQL no portal do Azure?**

Não. O registro com o provedor de recursos de VM do SQL não está disponível no portal do Azure. O registro com o provedor de recursos da VM do SQL só tem suporte com a CLI do Azure ou o PowerShell. 

**Posso registrar uma VM com o provedor de recursos da VM do SQL antes que o SQL Server seja instalado?**

Não. Uma VM deve ter pelo menos uma instância do SQL Server (Mecanismo de Banco de Dados) para registrar com êxito com o provedor de recursos da VM do SQL. Se não houver nenhuma instância do SQL Server na VM, o novo recurso Microsoft.SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com o provedor de recursos da VM do SQL se há várias instâncias do SQL Server?**

Sim. O provedor de recursos da VM do SQL registrará apenas uma instância do SQL Server (Mecanismo de Banco de Dados). O provedor de recursos da VM do SQL registrará a instância do SQL Server padrão no caso de várias instâncias. Se não houver instância padrão, haverá suporte apenas para o registro no modo leve. Para atualizar do modo de gerenciamento leve para o completo, a instância do SQL Server padrão deve existir ou a VM deve ter apenas uma instância nomeada do SQL Server.

**Posso registrar uma instância de cluster de failover do SQL Server com o provedor de recursos de VM do SQL?**

Sim. Instâncias de cluster de failover do SQL Server em uma VM do Azure podem ser registradas com o provedor de recursos de VM do SQL no modo leve. No entanto, instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com o provedor de recursos da VM do SQL se um grupo de disponibilidade Always On está configurado?**

Sim. Não há restrições para registrar uma instância de SQL Server em uma VM do Azure com o provedor de recursos de VM do SQL se você está participando de uma configuração de grupo de disponibilidade Always On.

**Qual é o custo de registro com o provedor de recursos da VM do SQL ou com a atualização para o modo de gerenciamento completo?**
Nenhum. Não há nenhum valor associado ao registro com o provedor de recursos da VM do SQL ou ao uso de qualquer um dos três modos de gerenciamento. O gerenciamento de sua VM do SQL Server com o provedor de recursos é completamente gratuito. 

**Qual é o impacto no desempenho do uso dos diferentes modos de gerenciamento?**
Não há nenhum impacto ao usar os modos de gerenciamento *NoAgent* e *leve*. Há um impacto mínimo ao usar o modo de gerenciamento *completo* de dois serviços que são instalados no sistema operacional. Eles podem ser monitorados por meio do gerenciador de tarefas e vistos no console interno de serviços do Windows. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (Nome de exibição – `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Nome de exibição – `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
