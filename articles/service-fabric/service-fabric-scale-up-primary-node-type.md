---
title: Escalar verticalmente um tipo de nó primário Service Fabric do Azure
description: Saiba como dimensionar um Cluster Service Fabric adicionando um tipo de nó.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: 01f6c90f9f7d7679f5b108138e2d2318eb6b9e18
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010801"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Dimensionar um tipo de nó primário do cluster do Service Fabric
Este artigo descreve como escalar verticalmente um tipo de nó primário de Cluster Service Fabric adicionando um tipo de nó adicional ao cluster. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente.

Os modelos de exemplo no tutorial a seguir podem ser encontrados aqui: [Service Fabric exemplos de dimensionamento de tipo de nó primário](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Não tente um procedimento de expansão do tipo de nó primário se o status do cluster não estiver íntegro, pois isso desestabilizará ainda mais o cluster.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Processo para atualizar o tamanho e o sistema operacional do tipo de nó primário
Veja a seguir o processo de atualização do tamanho da VM e do sistema operacional das VMs do tipo de nó primário.  Após a atualização, as VMs do tipo de nó primário são de tamanho padrão D4_V2 e executando o Windows Server 2019 datacenter com contêineres.

> [!WARNING]
> Antes de tentar esse procedimento em um cluster de produção, é recomendável que você estude os modelos de exemplo e verifique o processo em um cluster de teste. O cluster também pode estar indisponível por um curto período de tempo. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Implantar o cluster de Service Fabric inicial 
Se você quiser acompanhar o exemplo, implante o cluster inicial com um único tipo de nó primário e um único conjunto de dimensionamento [Service Fabric cluster inicial](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Você pode ignorar esta etapa se tiver um cluster de Service Fabric existente já implantado. 

1. Faça logon em sua conta do Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Criar um grupo de recursos. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. Preencha os valores de parâmetro nos arquivos de modelo. 
4. Implante o cluster no grupo de recursos criado na etapa 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Adicionar um novo tipo de nó primário ao cluster
> [!Note]
> Os recursos criados nas etapas a seguir se tornarão o novo tipo de nó primário no cluster depois que a operação de dimensionamento for concluída. Certifique-se de usar nomes que sejam exclusivos da sub-rede inicial, IP público, Load Balancer, conjunto de dimensionamento de máquinas virtuais e tipo de nó. 

> [!Note]
> Se você já estiver usando um IP público de SKU Standard e o SKU LB Standard, talvez não precise criar novos recursos de rede. 

Você pode encontrar um modelo com todas as etapas a seguir concluídas aqui: [Service Fabric – novo cluster de tipo de nó](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). As etapas a seguir contêm trechos de recursos parciais que destacam as alterações nos novos recursos.  

1. Crie uma nova sub-rede em sua rede virtual existente.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Crie um novo recurso de IP público com um domainNameLabel exclusivo. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Crie um novo recurso de Load Balancer que depende do IP público criado acima. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Crie um novo conjunto de dimensionamento de máquinas virtuais que usa o novo SKU de VM e o SKU de so para o qual você deseja escalar verticalmente. 

Referência de tipo de nó 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU da VM
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU DO SO 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. Adicione um novo tipo de nó ao cluster, que faz referência ao conjunto de dimensionamento de máquinas virtuais que foi criado acima. A propriedade **IsPrimary** neste tipo de nó deve ser definida como true. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Implante o modelo ARM atualizado. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

O cluster de Service Fabric agora terá dois tipos de nó quando a implantação for concluída. 

### <a name="remove-the-existing-node-type"></a>Remover o tipo de nó existente 
Depois que os recursos concluírem a implantação, você poderá começar a desabilitar os nós no tipo de nó primário original. À medida que os nós estiverem desabilitados, os serviços do sistema serão migrados para o novo tipo de nó primário que foi implantado na etapa anterior.

1. Desabilite os nós no tipo de nó 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Para durabilidade de bronze, aguarde até que todos os nós cheguem ao estado desabilitado.
* Para durabilidade prata e ouro, alguns nós entrarão em desabilitado e o restante estará no estado desabilitando. Verifique a guia detalhes dos nós no estado desabilitando, se todos estiverem presos na garantia de quorum para partições de serviço de infraestrutura, será seguro continuar.

> [!Note]
> Esta etapa pode demorar um pouco para ser concluída. 

2. Parar dados no tipo de nó 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
3. Desalocar nós no conjunto de dimensionamento de máquinas virtuais original 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

4. Remova o estado do nó do tipo de nó 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
5. Defina a propriedade de tipo de nó primário no Service Fabric recurso de cluster como false. 

```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```

5. Implante o modelo com a propriedade IsPrimary atualizada no tipo de nó original. Você pode encontrar um modelo com o sinalizador principal definido como false no tipo de nó original aqui: [Service Fabric-tipo de nó primário false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

7. Agora você pode excluir o IP original e Load Balancer recursos. Nesta etapa, você também atualizará o nome DNS. 
```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 
6. Atualize o ponto de extremidade de gerenciamento no cluster para fazer referência ao novo IP. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
7. Remova a referência do tipo de nó original do recurso Service Fabric no modelo ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Somente para clusters de durabilidade prata e mais alta, atualize o recurso de cluster no modelo e configure as políticas de integridade para ignorar a malha:/integridade do aplicativo do sistema adicionando applicationDeltaHealthPolicies em Propriedades de recurso de cluster, conforme indicado abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de integridade.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

8. Remova todos os outros recursos relacionados ao tipo de nó original do modelo ARM. Consulte [Service Fabric – novo cluster de tipo de nó](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) para um modelo com todos esses recursos originais removidos.

9. Implante o modelo de Azure Resource Manager modificado. * * Esta etapa levará um tempo, geralmente até duas horas. Essa atualização irá alterar as configurações para o InfrastructureService, portanto, é necessária uma reinicialização de nó. Nesse caso, forceRestart é ignorado. O parâmetro upgradeReplicaSetCheckTimeout especifica o tempo máximo que Service Fabric aguarda até que uma partição esteja em um estado seguro, se ainda não estiver em um estado seguro. Depois que as verificações de segurança forem aprovadas para todas as partições em um nó, Service Fabric continuará com a atualização nesse nó. O valor do parâmetro upgradeTimeout pode ser reduzido para 6 horas, mas para a segurança máxima 12 horas deve ser usado.
Em seguida, valide isso:

* Service Fabric recurso no portal mostra pronto.

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

O tipo de nó primário do cluster foi atualizado. Verifique se todos os aplicativos implantados funcionam corretamente e se a integridade do cluster está perfeita.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).
