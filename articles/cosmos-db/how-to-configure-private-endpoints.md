---
title: Configure o Azure Private Link para uma conta do Azure Cosmos
description: Saiba como configurar o Azure Private Link para acessar uma conta do Azure Cosmos usando um endereço IP privado em uma rede virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 9a6a1560e169c51256c198868dc7293a020189f4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421430"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Configure o Azure Private Link para uma conta do Azure Cosmos

Usando o Azure Private Link, você pode se conectar a uma conta do Azure Cosmos através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados em uma sub-rede dentro de sua rede virtual. Em seguida, você pode limitar o acesso a uma conta do Azure Cosmos em endereços IP privados. Quando o Private Link é combinado com políticas restritas de NSG, ele ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [do Azure Private Link.](../private-link/private-link-overview.md)

O Private Link permite que os usuários acessem uma conta do Azure Cosmos a partir da rede virtual ou de qualquer rede virtual. Os recursos mapeados para o Private Link também são acessíveis no local através de peering privado através de VPN ou Azure ExpressRoute. 

Você pode se conectar a uma conta do Azure Cosmos configurada com o Private Link usando o método de aprovação automática ou manual. Para saber mais, consulte a [seção Aprovação](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) do fluxo de trabalho da documentação do Private Link. 

Este artigo descreve as etapas para criar um ponto final privado. Ele assume que você está usando o método de aprovação automática.

> [!NOTE]
> O suporte de ponto final privado está disponível geralmente apenas para o modo de conexão do gateway. Para o modo direto, ele está disponível como um recurso de visualização.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Crie um ponto final privado usando o portal Azure

Use as seguintes etapas para criar um ponto final privado para uma conta Azure Cosmos existente usando o portal Azure:

1. No painel **Todos os recursos,** escolha uma conta do Azure Cosmos.

1. Selecione **Conexões de ponto final privado** na lista de configurações e, em seguida, selecione Ponto final **privado**:

   ![Seleções para criar um ponto final privado no portal Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. No **Criar um ponto final privado - Painel básico,** insira ou selecione os seguintes detalhes:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione um grupo de recursos.|
    | **Detalhes de instância** |  |
    | Nome | Digite qualquer nome para o seu ponto final privado. Se esse nome for tomado, crie um único. |
    |Região| Selecione a região onde deseja implantar o Private Link. Crie o ponto final privado no mesmo local onde sua rede virtual existe.|
    |||
1. Selecione **A seguir: Recurso**.
1. Em **Criar um ponto de extremidade privado – Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione **Conectar a um recurso do Azure no meu diretório**. <br/><br/> Em seguida, você pode escolher um de seus recursos para configurar o Private Link. Ou você pode se conectar ao recurso de outra pessoa usando um ID de recurso ou alias que eles compartilharam com você.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.AzureCosmosDB/banco de dadosContas**. |
    | Recurso |Selecione sua conta do Azure Cosmos. |
    |Sub-recurso de destino |Selecione o tipo de API Azure Cosmos DB que você deseja mapear. Isso é padrão para apenas uma opção para as APIs SQL, MongoDB e Cassandra. Para as APIs Gremlin e Table, você também pode escolher **Sql** porque essas APIs são interoperáveis com a API SQL. |
    |||

1. Selecione **A seguir: Configuração**.
1. Em **Criar um ponto final privado - Configuração,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione sua rede virtual. |
    | Sub-rede | Selecione sua sub-rede. |
    |**Integração Privada de DNS**||
    |Integrar com a zona DNS privado |Selecione **Sim**. <br><br/> Para se conectar privadamente com seu ponto final privado, você precisa de um registro DNS. Recomendamos que você integre seu ponto final privado com uma zona DNS privada. Você também pode usar seus próprios servidores DNS ou criar registros De DNS usando os arquivos host em suas máquinas virtuais. |
    |Zona DNS privado |Selecione **privatelink.documents.azure.com**. <br><br/> A zona dns privada é determinada automaticamente. Você não pode alterá-lo usando o portal Azure.|
    |||

1. Selecione **Revisão + criar**. Na página **'Revisar + criar',** o Azure valida sua configuração.
1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**.

Quando você aprovou o Private Link para uma conta do Azure Cosmos, no portal Azure, a opção **Todas as redes** no painel firewall e redes **virtuais** está indisponível.

A tabela a seguir mostra o mapeamento entre diferentes tipos de API de conta do Azure Cosmos, subrecursos suportados e os nomes de zona privada correspondentes. Você também pode acessar as contas de API Gremlin e Table através da API SQL, portanto há duas entradas para essas APIs.

|Azure Cosmos conta tipo de API  |Subrecursos suportados (ou IDs de grupo) |Nome da zona privada  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Busque os endereços IP privados

Depois que o ponto final privado for provisionado, você pode consultar os endereços IP. Para visualizar os endereços IP do portal Azure:

1. Selecione **Todos os recursos**.
1. Procure o ponto final privado que você criou anteriormente. Neste caso, é **cdbPrivateEndpoint3**.
1. Selecione a **guia Visão geral** para ver as configurações de DNS e endereços IP.

![Endereços IP privados no portal Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Vários endereços IP são criados por ponto final privado:

* Um para o ponto final global (região-agnóstico) da conta Azure Cosmos
* Uma para cada região onde a conta do Azure Cosmos é implantada

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Crie um ponto final privado usando o Azure PowerShell

Execute o seguinte script do PowerShell para criar um ponto final privado chamado "MyPrivateEndpoint" para uma conta Existente do Azure Cosmos. Substitua os valores variáveis pelos detalhes do seu ambiente.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma região de DNS privada

Depois de criar o ponto final privado, você pode integrá-lo com uma região DNS privada usando o seguinte script PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Busque os endereços IP privados

Depois que o ponto final privado for provisionado, você pode consultar os endereços IP e o mapeamento FQDN usando o seguinte script powerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Crie um ponto final privado usando o Azure CLI

Execute o seguinte script azure CLI para criar um ponto final privado chamado "myPrivateEndpoint" para uma conta Azure Cosmos existente. Substitua os valores variáveis pelos detalhes do seu ambiente.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma região de DNS privada

Depois de criar o ponto final privado, você pode integrá-lo com uma região DNS privada usando o seguinte script Azure CLI:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Crie um ponto final privado usando um modelo de Gerenciador de recursos

Você pode configurar o Private Link criando um ponto final privado em uma sub-rede virtual. Você consegue isso usando um modelo do Azure Resource Manager.

Use o seguinte código para criar um modelo de Gerenciador de recursos chamado "PrivateEndpoint_template.json". Esse modelo cria um ponto final privado para uma conta API Azure Cosmos SQL existente em uma rede virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Defina o arquivo de parâmetros para o modelo**

Crie um arquivo de parâmetros para o modelo e nomeie-o de "PrivateEndpoint_parameters.json". Adicione o seguinte código ao arquivo parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Implante o modelo usando um script PowerShell**

Crie um script PowerShell usando o seguinte código. Antes de executar o script, substitua o ID de assinatura, o nome do grupo de recursos e outros valores variáveis pelos detalhes do seu ambiente.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

No script PowerShell, `GroupId` a variável pode conter apenas um valor. Esse valor é o tipo de API da conta. Os valores `Sql` `MongoDB`permitidos são: , , `Cassandra`, `Gremlin`e `Table`. Alguns tipos de conta do Azure Cosmos são acessíveis através de várias APIs. Por exemplo: 

* Uma conta API Gremlin pode ser acessada tanto de contas Gremlin quanto da API SQL.
* Uma conta de API de tabela pode ser acessada tanto em contas de Tabela quanto de API SQL.

Para essas contas, você deve criar um ponto final privado para cada tipo de API. O tipo de API correspondente `GroupId` é especificado na matriz.

Depois que o modelo é implantado com sucesso, você pode ver uma saída semelhante ao que a imagem a seguir mostra. O `provisioningState` valor `Succeeded` é se os pontos finais privados forem configurados corretamente.

![Saída de implantação para o modelo Gerenciador de recursos](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Depois que o modelo é implantado, os endereços IP privados são reservados dentro da sub-rede. A regra de firewall da conta Azure Cosmos está configurada para aceitar conexões apenas do ponto final privado.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma zona de DNS privada

Use o seguinte código para criar um modelo de Gerenciador de recursos chamado "PrivateZone_template.json". Esse modelo cria uma zona de DNS privada para uma conta API Azure Cosmos SQL existente em uma rede virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Use o seguinte código para criar um modelo de Gerenciador de recursos chamado "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Defina o arquivo de parâmetros para o modelo**

Crie o arquivo de dois parâmetros a seguir para o modelo. Crie o "PrivateZone_parameters.json". pelo código a seguir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Crie o "PrivateZoneRecords_parameters.json". pelo código a seguir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Implante o modelo usando um script PowerShell**

Crie um script PowerShell usando o seguinte código. Antes de executar o script, substitua o ID de assinatura, o nome do grupo de recursos e outros valores variáveis pelos detalhes do seu ambiente.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Configurar DNS personalizado

Você deve usar uma zona DNS privada dentro da sub-rede onde você criou o ponto final privado. Configure os pontos finais para que cada endereço IP privado seja mapeado para uma entrada DNS. (Veja `fqdns` a propriedade na resposta mostrada anteriormente.)

Quando você está criando o ponto final privado, você pode integrá-lo com uma zona de DNS privada no Azure. Se você optar por usar uma região DNS personalizada, você terá que configurá-la para adicionar registros DNS para todos os endereços IP privados reservados para o ponto final privado.

## <a name="private-link-combined-with-firewall-rules"></a>Private Link combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o Private Link em combinação com as regras de firewall:

* Se você não configurar nenhuma regra de firewall, então, por padrão, todo o tráfego pode acessar uma conta do Azure Cosmos.

* Se você configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada serão autorizados pelo tipo correspondente de regra de firewall.

* Se você não configurar nenhum tráfego público ou ponto final de serviço e criar pontos finais privados, então a conta Do Azure Cosmos só será acessível através dos pontos finais privados. Se você não configurar o tráfego público ou um ponto final de serviço, depois que todos os pontos finais privados aprovados forem rejeitados ou excluídos, a conta será aberta para toda a rede.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Atualize um ponto final privado quando você adicionar ou remover uma região

Adicionar ou remover regiões a uma conta do Azure Cosmos exige que você adicione ou remova entradas DNS para essa conta. Depois que as regiões forem adicionadas ou removidas, você pode atualizar a zona dns privada da sub-rede para refletir as entradas DNS adicionadas ou removidas e seus endereços IP privados correspondentes.

Por exemplo, imagine que você implante uma conta do Azure Cosmos em três regiões: "EUA Ocidentais", "EUA Central" e "Europa Ocidental". Quando você cria um ponto final privado para sua conta, quatro IPs privados são reservados na sub-rede. Há um IP para cada uma das três regiões, e há um IP para o ponto final global/região-agnóstico.

Mais tarde, você pode adicionar uma nova região (por exemplo, "East US") à conta do Azure Cosmos. Depois de adicionar a nova região, você precisa adicionar um registro DNS correspondente à sua zona DNS privada ou ao seu DNS personalizado.

Você pode usar os mesmos passos quando remover uma região. Depois de remover a região, você precisa remover o registro DNS correspondente da sua zona DNS privada ou do Seu DNS personalizado.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam quando você está usando o Private Link com uma conta do Azure Cosmos:

* Quando você estiver usando o Private Link com uma conta do Azure Cosmos usando uma conexão de modo direto, você pode usar apenas o protocolo TCP. O protocolo HTTP ainda não foi suportado.

* O suporte de ponto final privado está disponível geralmente apenas para o modo de conexão do gateway. Para o modo direto, ele está disponível como um recurso de visualização.

* Quando você está usando a API do Azure Cosmos DB para contas MongoDB, um ponto final privado é suportado apenas para `*.mongo.cosmos.azure.com`contas na versão 3.6 do servidor (ou seja, contas usando o ponto final no formato). O Private Link não é suportado para contas na versão 3.2 do `*.documents.azure.com`servidor (ou seja, contas usando o ponto final no formato ). Para usar o Private Link, você deve migrar contas antigas para a nova versão.

* Quando você está usando a API do Azure Cosmos DB para contas MongoDB que têm Private Link, você não pode usar ferramentas como Robo 3T, Studio 3T e Mongoose. O ponto final só pode ter `appName=<account name>` suporte ao Private Link se o parâmetro for especificado. Um exemplo é `replicaSet=globaldb&appName=mydbaccountname`. Como essas ferramentas não passam o nome do aplicativo na seqüência de conexão para o serviço, você não pode usar o Private Link. Mas você ainda pode acessar essas contas usando drivers SDK com a versão 3.6.

* Você não pode mover ou excluir uma rede virtual se ela contiver o Private Link.

* Você não pode excluir uma conta do Azure Cosmos se ela estiver anexada a um ponto final privado.

* Você não pode falhar sobre uma conta do Azure Cosmos para uma região que não está mapeada para todos os pontos finais privados anexados à conta.

* Um administrador de rede deve receber pelo menos a permissão "*/PrivateEndpointConnectionsApproval" no escopo da conta Do Azure Cosmos para criar pontos finais privados aprovados automaticamente.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitações à integração privada da zona DedNS

Os registros de DNS na região DNS privada não são removidos automaticamente quando você exclui um ponto final privado ou remove uma região da conta do Azure Cosmos. Você deve remover manualmente os registros DNS antes:

* Adicionando um novo ponto final privado vinculado a esta zona dns privada.
* Adicionando uma nova região a qualquer conta de banco de dados que tenha pontos finais privados vinculados a essa zona de DNS privada.

Se você não limpar os registros de DNS, problemas inesperados de data plane podem acontecer. Esses problemas incluem a interrupção de dados em regiões adicionadas após a remoção de ponto final privado ou a remoção da região.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do Azure Cosmos DB, consulte os seguintes artigos:

* Para configurar um firewall para o Azure Cosmos DB, consulte [suporte ao Firewall](firewall-support.md).

* Para saber como configurar um ponto final de serviço de rede virtual para sua conta Do Azure Cosmos, consulte [Configurar o acesso a partir de redes virtuais](how-to-configure-vnet-service-endpoint.md).

* Para saber mais sobre o Private Link, consulte a documentação do [Azure Private Link.](../private-link/private-link-overview.md)
