---
title: Create an Azure Private Endpoint using Azure PowerShell| Microsoft Docs
description: Learn about Azure Private Link
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 83f1cbc3f8da61370c90744be3f0a7b230e016c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229397"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Create a private endpoint using Azure PowerShell
Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. 

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, um servidor do banco de dados SQL com um ponto de extremidade privado do Azure usando o Azure PowerShell. Em seguida, você pode acessar com segurança o servidor do banco de dados SQL da VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Before you can create your resources, you must create a resource group that hosts the Virtual Network and the private endpoint with [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). The following example creates a resource group named *myResourceGroup* in the *WestUS* location:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
In this section, you create a virtual network and a subnet. Next, you associate the subnet to your Virtual Network.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Create a Virtual network for your private endpoint with [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). The following example creates a Virtual Network named *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Add a Subnet

Azure deploys resources to a subnet within a Virtual Network, so you need to create a subnet. Create a subnet configuration named *mySubnet* with [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). The following example creates a subnet named *mySubnet* with the private endpoint network policy flag set to **Disabled**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associate the Subnet to the Virtual Network

You can write the subnet configuration to the Virtual Network with [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Create a VM in the Virtual Network with [New-AzVM](/powershell/module/az.compute/new-azvm). Quando você executa o próximo comando, é solicitado a informar as credenciais. Insira um nome de usuário e senha para a VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

A opção `-AsJob` cria a VM em segundo plano. Você pode continuar para a próxima etapa.

Quando o Azure inicia a criação da VM em segundo plano, você receberá de volta algo parecido com isto:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 

Create a  SQL Database Server by using the New-AzSqlServer command. Remember that the name of your SQL Database server must be unique across Azure, so replace the placeholder value in brackets with your own unique value:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Private Endpoint for the SQL Database Server in your Virtual Network with [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a private DNS zone for SQL Database Server domain and create an association link with the virtual network: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Conectar uma VM a partir da Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) para retornar o endereço IP público de uma VM. This example returns the public IP address of the *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Abra um prompt de comando no computador local. Run the mstsc command. Substitua <publicIpAddress> pelo endereço IP público retornado da última etapa: 


> [!NOTE]
> Se você estiver executando esses comandos em um prompt do PowerShell no computador local e estiver usando a versão 1.0 ou posterior do módulo Az PowerShell, poderá continuar nessa interface.
```
mstsc /v:<publicIpAddress>
```

1. Se solicitado, selecione **Conectar**. 
2. Insira o nome de usuário e senha que você especificou ao criar a VM.
  > [!NOTE]
  > You may need to select More choices > Use a different account, to specify the credentials you entered when you created the VM. 
  
3. Selecione **OK**. 
4. Você pode receber um aviso de certificado. Se você decidir, selecione **Sim** ou **Continuar**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

1. In the Remote Desktop of myVM, open PowerShell.
2. Digite `nslookup myserver.database.windows.net`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information: Setting Value Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## <a name="clean-up-resources"></a>Limpar recursos 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximos passos
- Learn more about [Azure Private Link](private-link-overview.md)
