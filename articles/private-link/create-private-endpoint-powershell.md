---
title: Crie um endpoint privado do Azure usando o Azure PowerShell| Microsoft Docs
description: Saiba mais sobre o Azure Private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430336"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Crie um ponto final privado usando o Azure PowerShell
Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. 

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, um servidor do banco de dados SQL com um ponto de extremidade privado do Azure usando o Azure PowerShell. Em seguida, você pode acessar com segurança o servidor do banco de dados SQL da VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar seus recursos, você deve criar um grupo de recursos que hospeda a Rede Virtual e o ponto final privado com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização do *WestUS:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Nesta seção, você cria uma rede virtual e uma sub-rede. Em seguida, você associa a sub-rede à sua Rede Virtual.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede Virtual para o seu ponto final privado com [o New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implanta recursos em uma sub-rede dentro de uma Rede Virtual, então você precisa criar uma sub-rede. Crie uma configuração de sub-rede chamada *mySubnet* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma sub-rede chamada *mySubnet* com o sinalizador de diretiva de ponto final privado definido como **Disabled**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> É fácil confundir o `PrivateEndpointNetworkPoliciesFlag` parâmetro com outra bandeira `PrivateLinkServiceNetworkPoliciesFlag`disponível, porque ambos são palavras longas e têm aparência semelhante.  Certifique-se de que você `PrivateEndpointNetworkPoliciesFlag`está usando o certo, .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associe a Subnet à Rede Virtual

Você pode escrever a configuração da sub-rede na Rede Virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual

Crie uma VM na Rede Virtual com [O Novo-AzVM](/powershell/module/az.compute/new-azvm). Quando você executa o próximo comando, é solicitado a informar as credenciais. Insira um nome de usuário e senha para a VM:

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

## <a name="create-a-sql-database-server"></a>Criar um Servidor do Banco de Dados SQL 

Crie um servidor de banco de dados SQL usando o comando New-AzSqlServer. Lembre-se de que o nome do seu servidor sql database deve ser único em todo o Azure, então substitua o valor do espaço reservado entre parênteses pelo seu próprio valor único:

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

Ponto final privado para o servidor de banco de dados SQL em sua rede virtual com [new-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>Configurar a Zona DNS Privada 
Crie uma zona de DNS privada para o domínio do SQL Database Server e crie um link de associação com a rede virtual: 

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
  
## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) para retornar o endereço IP público de uma VM. Este exemplo retorna o endereço IP público da VM *myVM:*

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Abra um prompt de comando no computador local. Comande o comando do MSTSC. Substitua <publicIpAddress> pelo endereço IP público retornado da última etapa: 


> [!NOTE]
> Se você estiver executando esses comandos em um prompt do PowerShell no computador local e estiver usando a versão 1.0 ou posterior do módulo Az PowerShell, poderá continuar nessa interface.
```
mstsc /v:<publicIpAddress>
```

1. Se solicitado, selecione **Conectar**. 
2. Insira o nome de usuário e senha que você especificou ao criar a VM.
  > [!NOTE]
  > Você pode precisar selecionar Mais opções > Use uma conta diferente, para especificar as credenciais inseridas quando criou a VM. 
  
3. Selecione **OK**. 
4. Você pode receber um aviso de certificado. Se você decidir, selecione **Sim** ou **Continuar**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Acessar o Servidor do Banco de Dados SQL de forma privada através da VM

1. Na Área de Trabalho Remota do myVM, abra o PowerShell.
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
3. Instale o SQL Server Management Studio
4. Em Conectar-se ao servidor, digite ou selecione essas informações: Configurando o tipo de servidor de valor Selecionar mecanismo de banco de dados.
      Nome do servidor Selecione myserver.database.windows.net nome de usuário Digite um nome de usuário fornecido durante a criação.
      Senha Digite uma senha fornecida durante a criação.
      Lembre-se de senha Selecione Sim.
5. Selecione Conectar.
6. Procurar bancos de dados do menu esquerdo. 
7. (Opcionalmente) Criar ou consultar informações do meubanco de dados
8. Feche a conexão remota da área de trabalho para *myVM*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o ponto final privado, o servidor sql database e o VM, use [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele possui:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Link Privado do Azure](private-link-overview.md)
