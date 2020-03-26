---
title: Criar uma VM do Windows do SQL Server com o Azure PowerShell | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual do Windows SQL Server 2017 com Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8994079cf18a9af5f5e1368761015bbd8b836bd9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74790899"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Início Rápido: criar uma máquina virtual do Windows do SQL Server com o Azure PowerShell

Esse início rápido percorre a criação de uma máquina virtual do SQL Server com o Azure PowerShell.

> [!TIP]
> - Este início rápido fornece um caminho para provisionar rapidamente e conectar-se a uma VM do SQL. Para obter mais informações sobre outras opções do Azure PowerShell para criar VMs do SQL, confira o [Guia de provisionamento para VMs do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).
> - Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Obter uma assinatura do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Obter o Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Configurar o PowerShell

1. Abra o PowerShell e estabeleça o acesso à sua conta do Azure executando o comando **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Você deve ver uma tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Defina uma variável com um nome exclusivo de grupo de recurso. Para simplificar o restante do início rápido, o restante dos comandos usa esse nome como base para outros nomes de recurso.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina um local de uma região de destino do Azure para todos os recursos de VM.

   ```powershell
   $Location = "East US"
   ```

1. Crie o grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Definir as configurações de rede

1. Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Crie um grupo de segurança de rede. Configure regras para permitir conexões da área de trabalho remota (RDP) e do SQL Server.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Crie a interface de rede.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Criar a VM de SQL

1. Defina suas credenciais para entrar na VM. O nome de usuário é "azureadmin". Altere a \<senha> antes de executar o comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Crie um objeto de configuração de máquina virtual e depois crie a VM. O comando a seguir cria uma VM do SQL Server 2017 Developer Edition no Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > São necessários vários minutos para criar a VM.

## <a name="install-the-sql-iaas-agent"></a>Instalar o SQL IaaS Agent

Para obter a integração do portal e recursos da VM do SQL, você deve instalar a [Extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Para instalar o agente na nova VM, execute o comando a seguir após a criação da VM.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "2.0" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Área de trabalho remota na VM

1. Use o comando a seguir para recuperar o endereço IP público da nova VM.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Passe o endereço IP retornado como um parâmetro de linha de comando para **mstsc** para iniciar uma sessão da Área de Trabalho Remota na nova VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando as credenciais forem solicitadas, opte por inserir as credenciais para uma conta diferente. Insira o nome de usuário precedido de uma barra invertida (por exemplo, `\azureadmin`) e a senha definida anteriormente neste início rápido.

## <a name="connect-to-sql-server"></a>Conecte-se ao SQL Server

1. Depois de entrar na sessão da Área de Trabalho Remota, inicie o **SQL Server Management Studio 2017** no menu Iniciar.

1. Na caixa de diálogo **Conectar-se ao Servidor**, mantenha os padrões. O nome do servidor é o mesmo da VM. A autenticação está definida como **Autenticação do Windows**. Selecione **Conectar**.

Agora você está conectado ao SQL Server localmente. Caso deseje se conectar remotamente, você precisará [configurar a conectividade](virtual-machines-windows-sql-connect.md) no portal ou manualmente.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não precise que a VM seja executada continuamente, é possível evitar encargos desnecessários interrompendo-a quando não estiver em uso. O comando a seguir interrompe a VM, mas a deixa disponível para uso futuro.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também é possível excluir permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup**. Essa ação também excluirá a máquina virtual permanentemente; portanto, use esse comando com cuidado.

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você criou uma máquina virtual do SQL Server 2017 no Azure PowerShell. Para saber mais sobre como migrar seus dados para o novo SQL Server, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Migrar um banco de dados para uma VM do SQL](virtual-machines-windows-migrate-sql.md)
