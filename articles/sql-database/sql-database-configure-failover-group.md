---
title: Configurar um grupo de failover
description: Aprenda a configurar um grupo de failover automático para um banco de dados único do Banco de Dados SQL do Azure, pool elástico e instância gerenciada usando o portal Azure, o Az CLI e o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461792"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configure um grupo de failover para o Banco de Dados SQL do Azure

Este tópico ensina como configurar um [grupo de failover automático](sql-database-auto-failover-group.md) para um banco de dados único do Banco de Dados SQL do Azure, pool elástico e instância gerenciada usando o portal Azure ou PowerShell. 

## <a name="single-database"></a>Banco de dados individual
Crie o grupo failover e adicione um único banco de dados a ele usando o portal Azure ou PowerShell.

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de login e firewall do servidor secundário devem corresponder à do servidor principal. 

### <a name="create-failover-group"></a>Criar grupo de failover

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie seu grupo de failover e adicione seu banco de dados único a ele usando o portal Azure.


1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o banco de dados único que deseja adicionar ao grupo de failover. 
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Servidor aberto para db único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** no painel **Configurações** e selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **Grupo failover,** digite ou selecione os valores necessários e selecione **Criar**.

   - **Bancos de dados dentro do grupo**: Escolha o banco de dados que deseja adicionar ao seu grupo de failover. A adição do banco de dados ao grupo de failover iniciará automaticamente o processo de geo-replicação. 
        
    ![Adicionar SQL DB ao grupo failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Crie seu grupo de failover e adicione seu banco de dados único a ele usando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Failover de Teste 

Teste failover do seu grupo failover usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste failover do seu grupo failover usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o banco de dados único que deseja adicionar ao grupo de failover. 

   ![Servidor aberto para db único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** no painel **Configurações** e escolha o grupo de failover que você acabou de criar. 
  
   ![Selecione o grupo failover do portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Revise qual servidor é primário e qual servidor é secundário. 
1. Selecione **Failover** do painel de tarefas para falhar sobre seu grupo de failover contendo seu banco de dados único. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Falha no seu grupo de failover contendo seu banco de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Revise qual servidor agora é primário e qual servidor é secundário. Se failover foi bem sucedido, os dois servidores devem ter trocado de funções. 
1. Selecione **Failover** novamente para falhar os servidores de volta às suas funções originais. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Teste failover do seu grupo failover usando o PowerShell.  

Verifique o papel da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Falha no servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Se você precisar excluir o banco de dados secundário, remova-o do grupo failover antes de excluí-lo. A exclusão de um banco de dados secundário antes de ser removido do grupo failover pode causar comportamento imprevisível. 

## <a name="elastic-pool"></a>Pool elástico
Crie o grupo failover e adicione um pool elástico a ele usando o portal Azure ou PowerShell.  

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de login e firewall do servidor secundário devem corresponder à do servidor principal. 

### <a name="create-the-failover-group"></a>Crie o grupo failover 

Crie o grupo failover para seu pool elástico usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie seu grupo de failover e adicione seu pool elástico a ele usando o portal Azure.

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o pool elástico que deseja adicionar ao grupo de failover. 
1. No **painel Visão geral,** selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abra o servidor para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos Failover** no painel **Configurações** e selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **Grupo failover,** digite ou selecione os valores necessários e selecione **Criar**. Crie um novo servidor secundário ou selecione um servidor secundário existente. 

1. Selecione **Bancos de Dados dentro do grupo** e escolha o pool elástico que deseja adicionar ao grupo de failover. Se um pool elástico ainda não existir no servidor secundário, um aviso aparecerá solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e selecione **OK** para criar o pool elástico no servidor secundário. 
        
    ![Adicionar piscina elástica ao grupo failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecione **Selecionar** para aplicar suas configurações de pool elástico no grupo failover e, em seguida, selecione **Criar** para criar seu grupo de failover. A adição da piscina elástica ao grupo failover iniciará automaticamente o processo de geo-replicação. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie seu grupo de failover e adicione seu pool elástico a ele usando o PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Failover de Teste

Teste failover do seu pool elástico usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Falhe seu grupo failover no servidor secundário e, em seguida, falhe usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o pool elástico que deseja adicionar ao grupo de failover. 
1. No **painel Visão geral,** selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abra o servidor para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selecione **grupos Failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2. 
  
   ![Selecione o grupo failover do portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Revise qual servidor é primário e qual servidor é secundário. 
1. Selecione **Failover** do painel de tarefas para falhar sobre o grupo de failover que contém seu pool elástico. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Falha no seu grupo de failover contendo seu banco de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Revise qual servidor é primário, qual servidor é secundário. Se failover foi bem sucedido, os dois servidores devem ter trocado de funções. 
1. Selecione **Failover** novamente para falhar o grupo failover de volta às configurações originais. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Teste failover do seu grupo failover usando o PowerShell.

Verifique o papel da réplica secundária: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Falha no servidor secundário: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Se você precisar excluir o banco de dados secundário, remova-o do grupo failover antes de excluí-lo. A exclusão de um banco de dados secundário antes de ser removido do grupo failover pode causar comportamento imprevisível. 

## <a name="managed-instance"></a>Instância gerenciada

Crie um grupo de failover entre duas instâncias gerenciadas usando o portal Azure ou o PowerShell. 

Você precisará configurar o [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou criar um gateway para a rede virtual de cada instância gerenciada, conectar os dois gateways e, em seguida, criar o grupo failover. 

### <a name="prerequisites"></a>Pré-requisitos
Considere os seguintes pré-requisitos:

- A instância secundária gerenciada deve estar vazia.
- O intervalo de sub-rede para a rede virtual secundária não deve se sobrepor à faixa de sub-rede da rede virtual primária. 
- A colagem e o fuso horário da instância secundária devem coincidir com o da instância primária. 
- Ao conectar os dois gateways, a **chave compartilhada** deve ser a mesma para ambas as conexões. 

### <a name="create-primary-virtual-network-gateway"></a>Criar gateway de rede virtual principal 

Se você não tiver configurado [o ExpressRoute,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)você pode criar o gateway de rede virtual principal com o portal Azure ou powerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o gateway de rede virtual principal usando o portal Azure. 

1. No [portal Azure,](https://portal.azure.com)vá para o seu grupo de recursos e selecione o recurso **de rede Virtual** para sua instância gerenciada principal. 
1. Selecione **Sub-redes** em **Configurações** e selecione para adicionar uma nova **sub-rede Gateway**. Deixe os valores padrão. 

   ![Adicionar gateway para instância gerenciada primária](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Uma vez que o gateway de sub-rede seja criado, **selecione Criar um recurso** do painel de navegação à esquerda e, em seguida, digite `Virtual network gateway` na caixa de pesquisa. Selecione o recurso **de gateway de rede virtual** publicado pela **Microsoft**. 

   ![Crie um novo gateway de rede virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos necessários para configurar o gateway na instância gerenciada principal. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada primária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Assinatura** |  A assinatura onde está sua instância gerenciada principal. |
    | **Nome** | O nome do seu gateway de rede virtual. | 
    | **Região** | A região onde sua instância secundária gerenciada está. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecione **baseado em rota** |
    | **Sku**| Deixe o `VpnGw1`padrão de . |
    | **Local**| O local onde sua instância gerenciada secundária e rede virtual secundária é.   |
    | **Rede virtual**| Selecione a rede virtual para sua instância gerenciada secundária. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Digite um nome para o seu endereço IP. |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **'Revisar + criar** para rever as configurações do gateway de rede virtual'.

   ![Configurações de gateway principal](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar seu novo gateway de rede virtual. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie o gateway de rede virtual principal usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Criar gateway de rede virtual secundário

Crie o gateway de rede virtual secundário usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Repita as etapas na seção anterior para criar a sub-rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos necessários para configurar o gateway para sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A assinatura onde está sua instância gerenciada secundária. |
   | **Nome** | O nome para o gateway `secondary-mi-gateway`de rede virtual, tais como . | 
   | **Região** | A região onde sua instância secundária gerenciada está. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo de VPN** | Selecione **baseado em rota** |
   | **Sku**| Deixe o `VpnGw1`padrão de . |
   | **Local**| O local onde sua instância gerenciada secundária e rede virtual secundária é.   |
   | **Rede virtual**| Selecione a rede virtual criada na seção 2, como `vnet-sql-mi-secondary`. |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Digite um nome para o `secondary-gateway-IP`seu endereço IP, como . |
   | &nbsp; | &nbsp; |

   ![Configurações de gateway secundário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie o gateway de rede virtual secundário usando o PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Conecte os gateways 
Crie conexões entre os dois gateways usando o portal Azure ou powerShell. 

Duas conexões precisam ser criadas - a conexão do gateway primário para o gateway secundário e, em seguida, a conexão do gateway secundário para o gateway principal. 

A chave compartilhada usada para ambas as conexões deve ser a mesma para cada conexão. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie conexões entre os dois gateways usando o portal Azure. 

1. Selecione **Criar um recurso** no portal [Azure](https://portal.azure.com).
1. Digite `connection` a caixa de pesquisa e, em seguida, pressione enter to search, o que o leva ao recurso **Conexão,** publicado pela Microsoft.
1. Selecione **Criar** para criar sua conexão. 
1. Na guia **Noções básicas,** selecione os seguintes valores e selecione **OK**. 
    1. Selecione `VNet-to-VNet` para o **tipo Conexão**. 
    1. Selecione sua assinatura na lista suspensa. 
    1. Selecione o grupo de recursos para sua instância gerenciada na queda. 
    1. Selecione a localização da instância gerenciada primária a partir da queda 
1. Na guia **Configurações,** selecione ou digite os seguintes valores e selecione **OK**:
    1. Escolha o gateway de rede principal para `Primary-Gateway`o gateway de rede virtual **First,** como .  
    1. Escolha o gateway de rede secundário para o `Secondary-Gateway`segundo gateway de rede **virtual,** como . 
    1. Selecione a caixa de seleção ao lado **de Estabelecer conectividade bidirecional**. 
    1. Deixe o nome de conexão principal padrão ou renomeie-o para um valor de sua escolha. 
    1. Forneça uma **chave compartilhada (PSK)** para `mi1m2psk`a conexão, como . 

   ![Criar conexão de gateway](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Na guia **Resumo,** revise as configurações da sua conexão bidirecional e selecione **OK** para criar sua conexão. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie conexões entre os dois gateways usando o PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Crie o grupo failover 
Crie o grupo failover para suas instâncias gerenciadas usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o grupo failover para suas instâncias gerenciadas usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione a instância gerenciada primária que deseja adicionar ao grupo de failover.  
1. Em **Configurações,** navegue até **Grupos de failover de instância** e opte por adicionar **grupo** para abrir a página **'Grupo de falha de instâncias'.** 

   ![Adicionar um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página **'Grupo de failover' exemplo,** digite o nome do seu grupo de failover e escolha a instância gerenciada secundária a partir da parada. Selecione **Criar** para criar seu grupo de failover. 

   ![Criar grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Uma vez que a implantação do grupo failover esteja concluída, você será levado de volta à página do **grupo Failover.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie o grupo failover para suas instâncias gerenciadas usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Failover de Teste

Teste failover do seu grupo failover usando o portal Azure ou PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste failover do seu grupo failover usando o portal Azure. 

1. Navegue até a instância _gerenciada secundária_ no [portal Azure](https://portal.azure.com) e selecione **Grupos de failover de instância** satisfazê-los em configurações. 
1. A revisão qual instância gerenciada é a principal e qual instância gerenciada é a secundária. 
1. Selecione **Failover** e selecione **Sim** no aviso sobre as sessões Desconectadas do TDS. 

   ![Falhar sobre o grupo failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Revisar qual instância manged é a primária e qual instância é a secundária. Se failover foi bem sucedido, as duas instâncias devem ter trocado de função. 

   ![As instâncias gerenciadas mudaram de função após failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Vá para a nova instância _gerenciada secundária_ e selecione **Failover** mais uma vez para falhar a instância primária de volta à função principal. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Teste failover do seu grupo failover usando o PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Localizar ponto final do ouvinte

Uma vez que seu grupo de failover esteja configurado, atualize a seqüência de conexões para o aplicativo para o ponto final do ouvinte. Isso manterá seu aplicativo conectado ao ouvinte do grupo failover, em vez do banco de dados principal, pool elástico ou instância gerenciada. Dessa forma, você não precisa atualizar manualmente a seqüência de conexões toda vez que sua entidade de banco de dados SQL do Azure falhar, e o tráfego for encaminhado para qualquer entidade que esteja atualmente primária. 

O ponto final do ouvinte `fog-name.database.windows.net`está na forma de , e é visível no portal Azure, ao visualizar o grupo failover:

![Cadeia de conexão de grupo failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Comentários

- A remoção de um grupo de failover para um banco de dados único ou pooled não interrompe a replicação e não exclui o banco de dados replicado. Você precisará parar manualmente a replicação geográfica e excluir o banco de dados do servidor secundário se quiser adicionar um banco de dados único ou agrupado de volta a um grupo de failover depois de removido. Não fazer nenhuma das coisas pode `The operation cannot be performed due to multiple errors` resultar em um erro semelhante ao tentar adicionar o banco de dados ao grupo de failover. 


## <a name="next-steps"></a>Próximas etapas

Para obter etapas detalhadas configurando um grupo de failover, consulte os seguintes tutoriais:
- [Adicione um único banco de dados a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
- [Adicionar um pool elástico a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
- [Adicionar instâncias gerenciadas a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Para obter uma visão geral das opções de alta disponibilidade do Banco de Dados Azure SQL, consulte os grupos [de replicação geográfica](sql-database-active-geo-replication.md) e [failover automático](sql-database-auto-failover-group.md). 
