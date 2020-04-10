---
title: Dimensione um tipo de nó de malha de serviço azure
description: Aprenda como dimensionar um cluster do Microsoft Azure Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 4dbb9e4fbfeb27c5b8b13f70207888cf37bbb0e0
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998937"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Dimensionar um tipo de nó primário do cluster do Service Fabric
Este artigo descreve como dimensionar um tipo de nó primário de cluster do Service Fabric aumentando os recursos da máquina virtual. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Após criar um cluster do Service Fabric, será possível dimensionar verticalmente um tipo de nó de cluster (alterar os recursos dos nós) ou atualizar o sistema operacional das VMs do tipo de nó.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

> [!WARNING]
> Não comece a alterar o serão iniciados alterar o SKU da VM do nodetype primário se o cluster não estiver íntegro. Se o cluster não estiver íntegro, tentar alterar o SKU da VM só desestabilizará o cluster ainda mais.
>
> É recomendável não alterar a SKU da VM de um tipo de nó/conjunto de dimensionamento, a menos que esteja em [Durabilidade prata ou maior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Alterar o Tamanho de SKU da VM é uma operação de infraestrutura no local com destruição de dados. Sem alguma habilidade para atrasar ou monitorar essa alteração, é possível que a operação possa causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem estado. Isso significa que seu tipo de nó primário, que está executando os serviços do sistema de malha de serviço com estado ou qualquer tipo de nó que estiver executando suas cargas de trabalho do aplicativo com estado.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Atualizar o tamanho e o sistema operacional das VMs do tipo de nó primário
Este é o processo para atualizar o tamanho da VM e o sistema operacional das VMs do tipo de nó primário.  Após fazer upgrade, as VMs do tipo de nó primário estarão em tamanho Standard D4_V2 e em execução no Windows Server 2016 Datacenter com Contêineres.

> [!WARNING]
> Antes de tentar esse procedimento em um cluster de produção, é recomendável que você estude os modelos de exemplo e verifique o processo em um cluster de teste. O cluster também estará indisponível por um tempo. Você NÃO pode fazer alterações em vários VMSS declarados como o mesmo NodeType em paralelo; é preciso executar operações de implantação separadas para aplicar alterações a cada VMSS do NodeType individualmente.

1. Implante o cluster inicial com dois tipos de nós e dois conjuntos de dimensionamento (um conjunto de dimensionamento por tipo de nó) usando esses arquivos de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) de exemplo.  Ambos os conjuntos de dimensionamento são do tamanho Standard D2_V2 e em execução no Windows Server 2012 R2 Datacenter.  Aguarde o cluster concluir o upgrade da linha de base.   
2. Opcional - implante um exemplo com estado no cluster.
3. Após atualizar as VMs do tipo de nó primário, adicione um novo conjunto de dimensionamento ao tipo de nó primário usando esses arquivos de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) de exemplo para que o tipo de nó primário agora tenha dois conjuntos de dimensionamento.  Os serviços do sistema e aplicativos do usuário podem migrar entre as VMs nos dois conjuntos de dimensionamento diferentes.  As novas VMs do conjunto de dimensionamento são de tamanho Standard D4_V2 e em execução no Windows Server 2016 Datacenter com Contêineres.  Um novo balanceador de carga e endereço IP público também são adicionados com o novo conjunto de dimensionamento.  
    Para localizar o novo conjunto de dimensionamento configurado no modelo, pesquise o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro *vmNodeType2Name*.  O novo conjunto de dimensionamento é adicionado ao tipo de nó primário usando a configuração propriedades-> virtualMachineProfile-> extensionProfile-> extensões-> propriedades-> configurações-> nodeTypeRef.
4. Verifique a integridade do cluster e se todos os nós estão íntegros.
5. Desabilite os nós no antigo conjunto de dimensionamento do tipo de nó primário com a intenção de remover o nó. É possível desabilitar todos de uma vez e as operações serão enfileiradas. Aguarde até que todos os nós estejam desabilitados, o que pode demorar algum tempo.  Como os nós mais antigos no tipo de nó estão desabilitados, os serviços do sistema e os nós de propagação migram para as VMs do novo conjunto de dimensionamento configurado no tipo de nó primário.
6. Remova o conjunto de dimensionamento mais antigo do tipo de nó primário. (Depois que os nós forem desativados como na etapa 5, na lâmina de conjunto de escala de máquina virtual no portal Azure, desaloque os nós do nó antigo tipo um por um.)
7. Remova o balanceador de carga associado ao antigo conjunto de dimensionamento. O cluster não estará disponível enquanto o novo endereço IP público e o balanceador de carga estiverem configurados para o novo conjunto de dimensionamento.  
8. Armazene as configurações de DNS do endereço IP público associado ao antigo conjunto de dimensionamento do tipo de nó primário em uma variável e remova esse endereço IP público.
9. Substitua as configurações de DNS do endereço IP público associado ao novo conjunto de dimensionamento do tipo de nó primário com as configurações de DNS do endereço IP público excluído.  O cluster agora está acessível novamente.
10. Remova o estado do nó dos nós do cluster.  Se o nível de durabilidade do antigo conjunto de dimensionamento for ouro ou prata, essa etapa será feita automaticamente pelo sistema.
11. Se você implantou o aplicativo com estado em uma etapa anterior, verifique se o aplicativo está funcionando.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

