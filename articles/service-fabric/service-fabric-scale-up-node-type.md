---
title: Dimensionar um tipo de nó do Azure Service Fabric
description: Aprenda como dimensionar um cluster do Microsoft Azure Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610686"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Dimensionar um tipo de nó primário do cluster do Service Fabric
Este artigo descreve como dimensionar um tipo de nó primário de cluster do Service Fabric aumentando os recursos da máquina virtual. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Após criar um cluster do Service Fabric, será possível dimensionar verticalmente um tipo de nó de cluster (alterar os recursos dos nós) ou atualizar o sistema operacional das VMs do tipo de nó.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

> [!WARNING]
> Não tente um procedimento de expansão do tipo de nó primário se o status do cluster não estiver íntegro, pois isso desestabilizará ainda mais o cluster.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Processo para atualizar o tamanho e o sistema operacional das VMs do tipo de nó primário
Este é o processo para atualizar o tamanho da VM e o sistema operacional das VMs do tipo de nó primário.  Após fazer upgrade, as VMs do tipo de nó primário estarão em tamanho Standard D4_V2 e em execução no Windows Server 2016 Datacenter com Contêineres.

> [!WARNING]
> Antes de tentar esse procedimento em um cluster de produção, é recomendável que você estude os modelos de exemplo e verifique o processo em um cluster de teste. O cluster também estará indisponível por um tempo. Você NÃO pode fazer alterações em vários VMSS declarados como o mesmo NodeType em paralelo; é preciso executar operações de implantação separadas para aplicar alterações a cada VMSS do NodeType individualmente.

1. Implante o cluster inicial com dois tipos de nós e dois conjuntos de dimensionamento (um conjunto de dimensionamento por tipo de nó) usando esses arquivos de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) de exemplo.  Ambos os conjuntos de dimensionamento são do tamanho Standard D2_V2 e em execução no Windows Server 2012 R2 Datacenter.  Aguarde o cluster concluir o upgrade da linha de base.   
2. Opcional - implante um exemplo com estado no cluster.
3. Após atualizar as VMs do tipo de nó primário, adicione um novo conjunto de dimensionamento ao tipo de nó primário usando esses arquivos de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) de exemplo para que o tipo de nó primário agora tenha dois conjuntos de dimensionamento.  Os serviços do sistema e aplicativos do usuário podem migrar entre as VMs nos dois conjuntos de dimensionamento diferentes.  As novas VMs do conjunto de dimensionamento são de tamanho Standard D4_V2 e em execução no Windows Server 2016 Datacenter com Contêineres.  Um novo balanceador de carga e endereço IP público também são adicionados com o novo conjunto de dimensionamento.  
    Para localizar o novo conjunto de dimensionamento configurado no modelo, pesquise o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro *vmNodeType2Name*.  O novo conjunto de dimensionamento é adicionado ao tipo de nó primário usando a configuração propriedades-> virtualMachineProfile-> extensionProfile-> extensões-> propriedades-> configurações-> nodeTypeRef.
4. Verifique a integridade do cluster e se todos os nós estão íntegros.
5. Desabilite os nós no antigo conjunto de dimensionamento do tipo de nó primário com a intenção de remover o nó. É possível desabilitar todos de uma vez e as operações serão enfileiradas. Aguarde até que todos os nós estejam desabilitados, o que pode demorar algum tempo.  Como os nós mais antigos no tipo de nó estão desabilitados, os serviços do sistema e os nós de propagação migram para as VMs do novo conjunto de dimensionamento configurado no tipo de nó primário.
6. Remova o conjunto de dimensionamento mais antigo do tipo de nó primário. (Depois que os nós forem desabilitados como na etapa 5, na folha do conjunto de dimensionamento de máquinas virtuais no portal do Azure, desaloque os nós do tipo de nó antigo um a um.)
7. Remova o balanceador de carga associado ao antigo conjunto de dimensionamento. O cluster não estará disponível enquanto o novo endereço IP público e o balanceador de carga estiverem configurados para o novo conjunto de dimensionamento.  
8. Armazene as configurações de DNS do endereço IP público associado ao antigo conjunto de dimensionamento do tipo de nó primário em uma variável e remova esse endereço IP público.
9. Substitua as configurações de DNS do endereço IP público associado ao novo conjunto de dimensionamento do tipo de nó primário com as configurações de DNS do endereço IP público excluído.  O cluster agora está acessível novamente.
10. Remova o estado do nó dos nós do cluster.  Se o nível de durabilidade do antigo conjunto de dimensionamento for ouro ou prata, essa etapa será feita automaticamente pelo sistema.
11. Se você implantou o aplicativo com estado em uma etapa anterior, verifique se o aplicativo está funcionando.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Comece baixando os dois conjuntos de arquivos necessários neste tutorial, o [modelo]() e os [parâmetros]() anteriores e o [modelo]() e os [parâmetro]() posteriores.

Em seguida, entre na sua conta do Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Este tutorial percorre o cenário de criação de um certificado autoassinado. Para usar um certificado já existente no Azure Key Vault, ignore a etapa abaixo e, em vez disso, espelhe as etapas em [usando um certificado existente para implantar o cluster](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado autoassinado e implantar o cluster

Primeiramente, atribua as variáveis necessárias para a implantação do cluster do Service Fabric. Ajuste os valores de `resourceGroupName`, `certSubjectName`, `parameterFilePath` e `templateFilePath` da sua conta e ambiente específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Verifique se a localização de `certOutputFolder` existe no computador local, antes de executar o comando para implantar um novo cluster do Service Fabric.

Em seguida, abra o arquivo *Deploy-2NodeTypes-2ScaleSets.parameters.json* e ajuste os valores de `clusterName` e `dnsName`, de acordo com os valores dinâmicos que você definiu no PowerShell, e salve as alterações.

Em seguida, implante o cluster de teste do Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Quando a implantação for concluída, localize o arquivo *.pfx* (`$certPfx`) no seu computador local e importe-o para o repositório de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação retornará a impressão digital do certificado, que você usará para se conectar ao novo cluster e verificar seu status da integridade.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Conectar-se ao novo cluster e verificar o status da integridade

Conecte-se ao cluster e verifique se todos os seus nós estão íntegros (substituindo as variáveis `clusterName` e `thumb` do cluster):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Estamos prontos para iniciar o procedimento de atualização.

## <a name="upgrade-the-primary-node-type-vms"></a>Atualizar as VMs com tipo de nó primário

Após atualizar as VMs do tipo de nó primário, adicione um novo conjunto de dimensionamento ao tipo de nó primário para que ele tenha dois conjuntos de dimensionamento. Um arquivo de exemplo de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) foi fornecido para mostrar as alterações necessárias. As novas VMs do conjunto de dimensionamento são de tamanho Standard D4_V2 e são executadas no Windows Server 2016 Datacenter com contêineres. Um novo balanceador de carga e endereço IP público também são adicionados com o novo conjunto de dimensionamento. 

Para localizar o novo conjunto de dimensionamento configurado no modelo, pesquise o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro vmNodeType2Name. O novo conjunto de dimensionamento é adicionado ao tipo de nó primário usando a configuração propriedades-> virtualMachineProfile-> extensionProfile-> extensões-> propriedades-> configurações-> nodeTypeRef.

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado

Ajuste `parameterFilePath` e `templateFilePath` conforme necessário e execute este comando:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a implantação for concluída, verifique a integridade do cluster novamente e veja se todos os nós (nos conjuntos de dimensionamento novo e original) estão íntegros.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrar nós para o novo conjunto de dimensionamento

Agora estamos prontos para começar a desabilitar os nós do conjunto de dimensionamento original. Com a desabilitação desses nós, os serviços do sistema e os nós semente migram para as VMs do novo conjunto de dimensionamento porque ele também está marcado como tipo de nó primário.

Para escalar verticalmente tipos de nó não primários, nesta etapa, você modificaria a restrição de posicionamento do serviço para incluir o novo tipo de nó/conjunto de dimensionamento de máquinas virtuais e, em seguida, reduzir a contagem de instâncias antiga do conjunto de dimensionamento de máquinas virtuais para zero, um nó por vez (para garantir que a remoção do nó não afete a confiabilidade do cluster

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Use o Service Fabric Explorer para monitorar a migração dos nós semente para o novo conjunto de dimensionamento e a progressão dos nós no conjunto de dimensionamento original do status *Desabilitando* para o status *Desabilitado*.

> [!NOTE]
> Pode levar algum tempo para concluir a operação de desabilitação em todos os nós do conjunto de dimensionamento original. Para garantir a consistência dos dados, apenas um nó semente pode ser alterado por vez. Cada alteração de nó semente requer uma atualização do cluster. Portanto, a substituição de um nó semente exige duas atualizações do cluster (uma para adição e outra para remoção do nó). Atualizar os cinco nós semente neste cenário de exemplo resultará em dez atualizações do cluster.

## <a name="remove-the-original-scale-set"></a>Remover o conjunto de dimensionamento original

Quando a operação de desabilitação for concluída, remova o conjunto de dimensionamento.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

No Service Fabric Explorer, os nós removidos (e, portanto, o *estado de integridade do cluster*) agora aparecerão no estado de *Erro*.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Remover o balanceador de carga antigo e atualizar as configurações de DNS

Agora, podemos remover os recursos relacionados ao tipo de nó primário antigo, começando pelo balanceador de carga e pelo IP público antigo. 

```powershell
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
```

Em seguida, atualizamos as configurações de DNS do novo IP público para espelhar as configurações do IP público do tipo de nó primário antigo.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Mais uma vez, verifique a integridade do cluster.

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Por fim, remova o estado de cada um dos nós relacionados. Se o nível de durabilidade do antigo conjunto de dimensionamento for ouro ou prata, isso ocorrerá automaticamente.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

O tipo de nó primário do cluster foi atualizado. Verifique se todos os aplicativos implantados funcionam corretamente e se a integridade do cluster está perfeita.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

