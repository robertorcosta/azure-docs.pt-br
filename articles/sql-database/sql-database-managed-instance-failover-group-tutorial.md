---
title: 'Tutorial: Adicione uma instância gerenciada a um grupo de failover'
description: Aprenda a configurar um grupo de failover para a instância gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462642"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Tutorial: Adicione uma instância gerenciada do Banco de Dados SQL a um grupo de failover

Adicionar uma instância gerenciada do Banco de Dados SQL a um grupo de failover. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um grupo de [failover](sql-database-auto-failover-group.md). 
> - Failover de Teste

  > [!NOTE]
  > - Ao passar por este tutorial, certifique-se de que você está configurando seus recursos com os [pré-requisitos para configurar grupos de failover para instância gerenciada](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Criar uma instância gerenciada pode levar um tempo significativo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte [operações gerenciadas de gerenciamento de instâncias](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - As instâncias gerenciadas que participam de um grupo de failover exigem [expressroute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou dois gateways VPN conectados. Este tutorial fornece etapas para criar e conectar os gateways VPN. Pule essas etapas se você já tiver o ExpressRoute configurado. 


## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, verifique se você tem: 

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Para completar o tutorial, certifique-se de ter os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Criar grupo de recursos e instância gerenciada primária
Nesta etapa, você criará o grupo de recursos e a principal instância gerenciada para o seu grupo de failover usando o portal Azure ou PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie o grupo de recursos e sua instância gerenciada principal usando o portal Azure. 

1. Selecione **SQL do Azure** no menu à esquerda do portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode exibir informações adicionais sobre os diferentes bancos de dados selecionando Mostrar detalhes no bloco Bancos de dados.
1. Selecione **Criar** no bloco **de instâncias gerenciadas SQL.** 

    ![Selecione a instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na página **'Criar a ocorrência gerenciada do banco de dados do Azure SQL',** na guia **Básico**
    1. Em **Detalhes do projeto,** selecione sua **assinatura** na parte baixa e escolha **criar um novo** grupo de recursos. Digite um nome para o `myResourceGroup`seu grupo de recursos, como . 
    1. Em **Detalhes de instância gerenciada,** forneça o nome da instância gerenciada e a região onde você gostaria de implantar sua instância gerenciada. Deixe o **Compute + armazenamento** em valores padrão. 
    1. Em **Conta de Administrador,** forneça um login `azureuser`de administrador, como , e uma senha de administrador complexa. 

    ![Criar MI primário](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Deixe o resto das configurações em valores padrão e selecione **'Revisar+ criar** para rever as configurações da instância gerenciada'. 
1. Selecione **Criar** para criar sua instância gerenciada primária. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie seu grupo de recursos e a instância gerenciada principal usando o PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual em um grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede de uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do Banco de Dados SQL do Azure.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Criar rede virtual secundária
Se você estiver usando o portal Azure para criar sua instância gerenciada, você precisará criar a rede virtual separadamente porque há um requisito de que a sub-rede da instância gerenciada primária e secundária não tenha intervalos sobrepostos. Se estiver usando o PowerShell para configurar sua instância gerenciada, pule para o passo 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Para verificar o intervalo de sub-rede da sua rede virtual primária, siga estas etapas:
1. No [portal Azure,](https://portal.azure.com)navegue até o seu grupo de recursos e selecione a rede virtual para sua instância principal. 
1. Selecione **Sub-redes** em **Configurações** e observe a **faixa Endereço**. O intervalo de endereço de sub-rede da rede virtual para a instância gerenciada secundária não pode se sobrepor a isso. 


   ![Sub-rede primária](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** e procurar rede *virtual.* 
1. Selecione a opção **Rede Virtual** publicada pela Microsoft e selecione **Criar** na próxima página. 
1. Preencha os campos necessários para configurar a rede virtual para sua instância gerenciada secundária e, em seguida, selecione **Criar**. 

   A tabela a seguir mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Valor |
    | --- | --- |
    | **Nome** |  O nome da rede virtual a ser usada pela instância `vnet-sql-mi-secondary`gerenciada secundária, como . |
    | **Espaço de endereço** | O espaço de endereço para `10.128.0.0/16`sua rede virtual, tais como . | 
    | **Assinatura** | A assinatura onde reside sua instância gerenciada primária e o grupo de recursos. |
    | **Região** | O local onde você implantará sua instância gerenciada secundária. |
    | **Sub-rede** | O nome da sua sub-rede. `default`é fornecido para você por padrão. |
    | **Intervalo de endereços**| O intervalo de endereços da sua sub-rede. Isso deve ser diferente do intervalo de endereços de sub-rede usado `10.128.0.0/24`pela rede virtual da sua instância gerenciada primária, como .  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundários](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Esta etapa só é necessária se você estiver usando o portal Azure para implantar sua instância gerenciada. Pule para o passo 3 se estiver usando o PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Criar uma instância gerenciada secundária
Nesta etapa, você criará uma instância gerenciada secundária no portal Azure, que também configurará a rede entre as duas instâncias gerenciadas. 

Sua segunda instância gerenciada deve:
- Esteja vazio. 
- Tenha uma sub-rede e um intervalo de IP diferente da instância gerenciada primária. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Crie a instância gerenciada secundária usando o portal Azure. 

1. Selecione **SQL do Azure** no menu à esquerda do portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode exibir informações adicionais sobre os diferentes bancos de dados selecionando Mostrar detalhes no bloco Bancos de dados.
1. Selecione **Criar** no bloco **de instâncias gerenciadas SQL.** 

    ![Selecione a instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na guia Básico da página Criar instância gerenciada do banco de **dados SQL do Azure,** preencha os campos **necessários** para configurar sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para a instância gerenciada secundária:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Assinatura** |  A assinatura onde está sua instância gerenciada principal. |
    | **Grupo de recursos**| O grupo de recursos onde está sua instância gerenciada primária. |
    | **Nome da Instância Gerenciada** | O nome de sua nova instância gerenciada secundária, como`sql-mi-secondary`  | 
    | **Região**| A localização da sua instância gerenciada secundária.  |
    | **Logon de administrador da Instância Gerenciada** | O login que você deseja usar para sua nova `azureuser`instância gerenciada secundária, como . |
    | **Senha** | Uma senha complexa que será usada pelo login de admin para a nova instância gerenciada secundária.  |
    | &nbsp; | &nbsp; |

1. Na guia **Rede,** para a **Rede Virtual,** selecione a rede virtual criada para a instância gerenciada secundária a partir do drop-down.

   ![Rede MI secundária](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na **guia Configurações adicionais,** para **Geo-Replicação,** escolha **Sim** para _usar como secundário failover_. Selecione a instância gerenciada primária a partir do drop-down. 
    1. Certifique-se de que a colagem e o fuso horário correspondem ao da instância gerenciada primária. A instância gerenciada primária criada neste tutorial `SQL_Latin1_General_CP1_CI_AS` usou o `(UTC) Coordinated Universal Time` padrão de colagem e o fuso horário. 

   ![Rede MI secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecione **'Revisar + criar para** rever as configurações da instância gerenciada secundária'. 
1. Selecione **Criar** para criar sua instância gerenciada secundária. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie a instância gerenciada secundária usando o PowerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual em um grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede de uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do Banco de Dados SQL do Azure.  |

---

## <a name="4---create-primary-gateway"></a>4 - Criar gateway primário 
Para duas instâncias gerenciadas participarem de um grupo de failover, deve haver um ExpressRoute ou um gateway configurado entre as redes virtuais das duas instâncias gerenciadas para permitir a comunicação de rede. Se você optar por configurar o [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) em vez de conectar dois gateways VPN, pule para a [etapa 7](#7---create-a-failover-group).  

Este artigo fornece etapas para criar os dois gateways VPN e conectá-los, mas você pode pular para criar o grupo failover se você tiver configurado o ExpressRoute em vez disso. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o gateway para a rede virtual de sua instância gerenciada principal usando o portal Azure. 


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
    | **Nome** | O nome para o gateway `primary-mi-gateway`de rede virtual, tais como . | 
    | **Região** | A região onde sua instância secundária gerenciada está. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecione **baseado em rota** |
    | **Sku**| Deixe o `VpnGw1`padrão de . |
    | **Local**| O local onde sua instância gerenciada primária e a rede virtual primária é.   |
    | **Rede virtual**| Selecione a rede virtual criada na seção 2, como `vnet-sql-mi-primary`. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Digite um nome para o `primary-gateway-IP`seu endereço IP, como . |
    | &nbsp; | &nbsp; |

1. Deixe os outros valores como padrão e, em seguida, selecione **'Revisar + criar** para rever as configurações do gateway de rede virtual'.

   ![Configurações de gateway principal](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar seu novo gateway de rede virtual. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie o gateway para a rede virtual de sua instância gerenciada principal usando o PowerShell. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual em um grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |


---


## <a name="5---create-secondary-gateway"></a>5 - Criar gateway secundário 
Nesta etapa, crie o gateway para a rede virtual da sua instância gerenciada secundária usando o portal Azure, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Usando o portal Azure, repita as etapas na seção anterior para criar a sub-rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos necessários para configurar o gateway para sua instância gerenciada secundária. 

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

Crie o gateway para a rede virtual da instância gerenciada secundária usando o PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual em um grupo de recursos. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |

---


## <a name="6---connect-the-gateways"></a>6 - Conectar os gateways
Nesta etapa, crie uma conexão bidirecional entre os dois gateways das duas redes virtuais. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Conecte os dois gateways usando o portal Azure. 


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

Conecte os dois gateways usando o PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Esta parte do tutorial usa o seguinte cmdlet powershell:

| Comando | Observações |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma conexão entre os dois Gateways de Rede Virtual.   |

---


## <a name="7---create-a-failover-group"></a>7 - Criar um grupo de failover
Nesta etapa, você criará o grupo failover e adicionará ambas as instâncias gerenciadas a ele. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie o grupo failover usando o portal Azure. 


1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione a instância gerenciada primária que você `sql-mi-primary`criou na primeira seção, como . 
1. Em **Configurações,** navegue até **Grupos de failover de instância** e opte por adicionar **grupo** para abrir a página **'Grupo de falha de instâncias'.** 

   ![Adicionar um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página **'Grupo de failover' instância,** digite `failovergrouptutorial` o nome do seu grupo de `sql-mi-secondary` failover, como e escolha a instância gerenciada secundária, como a partir do drop-down. Selecione **Criar** para criar seu grupo de failover. 

   ![Criar grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Uma vez que a implantação do grupo failover esteja concluída, você será levado de volta à página do **grupo Failover.** 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Crie o grupo failover usando o PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Esta parte do tutorial usa o seguinte cmdlet powershell:

| Comando | Observações |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instância gerenciada do Banco de Dados SQL do Azure.  |


---


## <a name="8---test-failover"></a>8 - Failover de teste
Nesta etapa, você falhará seu grupo de failover no servidor secundário e, em seguida, falhará usando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Failover de teste usando o portal Azure. 


1. Navegue até a instância _gerenciada secundária_ no [portal Azure](https://portal.azure.com) e selecione **Grupos de failover de instância** satisfazê-los em configurações. 
1. A revisão qual instância gerenciada é a principal e qual instância gerenciada é a secundária. 
1. Selecione **Failover** e selecione **Sim** no aviso sobre as sessões Desconectadas do TDS. 

   ![Falhar sobre o grupo failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Revisar qual instância manged é a primária e qual instância é a secundária. Se falhar em cima do sucesso, as duas instâncias devem ter trocado de papel. 

   ![As instâncias gerenciadas mudaram de função após failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Vá para a nova instância _gerenciada secundária_ e selecione **Failover** mais uma vez para falhar a instância primária de volta à função principal. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Teste failover usando PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Reverta o grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instância gerenciada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa um failover de um grupo de failover de instância gerenciada. | 

---



## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos excluindo primeiro a instância gerenciada, depois o cluster virtual, depois todos os recursos restantes e, finalmente, o grupo de recursos. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com). 
1. Selecione as instâncias gerenciadas e, em seguida, selecione **Excluir**. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**. Esse processo pode levar algum tempo para ser concluído em segundo plano, e até que seja concluído, você não poderá excluir o *cluster Virtual* ou quaisquer outros recursos dependentes. Monitore a exclusão na guia Atividade para confirmar se a instância gerenciada foi excluída. 
1. Depois que a instância gerenciada for excluída, exclua o *cluster virtual* selecionando-o no grupo de recursos e, em seguida, escolhendo **Excluir**. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**. 
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**. 
1. Exclua o grupo de recursos selecionando **Excluir grupo de recursos**, digitando o nome do grupo de recursos, `myResourceGroup`, e, em seguida, selecionando **Excluir**. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Você precisará remover o grupo de recursos duas vezes. Remover o grupo de recursos pela primeira vez removerá a instância gerenciada e os clusters virtuais, mas falhará em seguida com esta mensagem de erro: `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Execute o comando Remove-AzResourceGroup uma segunda vez para remover todos os recursos residuais, bem como o grupo de recursos.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Esta parte do tutorial usa o seguinte cmdlet powershell:

| Comando | Observações |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. |

---

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual em um grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede de uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do Banco de Dados SQL do Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retorna informações sobre a instância gerenciada de Banco de Dados SQL do Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um Gateway de Rede Virtual |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma conexão entre os dois Gateways de Rede Virtual.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instância gerenciada do Banco de Dados SQL do Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instância gerenciada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa um failover de um grupo de failover de instância gerenciada. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Não há scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um grupo de failover entre duas instâncias gerenciadas. Você aprendeu a:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um grupo de [failover](sql-database-auto-failover-group.md). 
> - Failover de Teste

Avance para o próximo quickstart sobre como se conectar à sua instância gerenciada e como restaurar um banco de dados à sua instância gerenciada: 

> [!div class="nextstepaction"]
> [Conecte-se à sua instância](sql-database-managed-instance-configure-vm.md)
> gerenciada[Restaurar um banco de dados em uma instância gerenciada](sql-database-managed-instance-get-started-restore.md)


