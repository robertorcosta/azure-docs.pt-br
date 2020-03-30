---
title: Implantar um cluster em zonas de disponibilidade
description: Saiba como criar um cluster de malha de serviço do Azure em zonas de disponibilidade.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609971"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implantar um cluster de malha de serviço do Azure em zonas de disponibilidade
Disponibilidade As zonas no Azure são uma oferta de alta disponibilidade que protege seus aplicativos e dados contra falhas no data center. Uma Zona de Disponibilidade é um local físico único equipado com energia independente, resfriamento e rede dentro de uma região azure.

O Service Fabric suporta clusters que se estendem por Zonas de Disponibilidade, implantando tipos de nós que são fixados em regiões específicas. Isso garantirá alta disponibilidade de seus aplicativos. As Zonas de Disponibilidade do Azure só estão disponíveis em regiões selecionadas. Para obter mais informações, consulte [a visão geral das zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Os modelos de exemplo estão disponíveis: [modelo de zona de disponibilidade cruzada do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia recomendada para o tipo de nó principal de clusters de malha de serviço Do Azure que abrangem as Zonas de Disponibilidade
Um cluster de malha de serviço distribuído entre zonas de disponibilidade garante alta disponibilidade do estado de cluster. Para cobrir um cluster de malha de serviço entre as regiões, você deve criar um tipo de nó primário em cada Zona de Disponibilidade suportada pela região. Isso distribuirá os nódulos de sementes uniformemente em cada um dos tipos de nó primário.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* O nível de confiabilidade do cluster definido como Platinum.
* Três tipos de node marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de escala de máquina virtual deve ter pelo menos cinco nodes (Durabilidade de Prata).
* Um único recurso ip público usando o SKU padrão.
* Um recurso de balanceador de carga único usando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de escala de máquina virtual.

>[!NOTE]
> A propriedade de grupo de colocação única definida em escala de máquina virtual deve ser definida como verdadeira, uma vez que o Service Fabric não suporta um único conjunto de escala de máquina virtual que abrange regiões.

 ![Arquitetura da zona de disponibilidade do uso do serviço azure][sf-architecture]

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>Recurso público de IP e balanceador de carga
Para habilitar a propriedade de regiões em um recurso de conjunto de escala de máquina virtual, o balanceador de carga e o recurso IP referenciados por esse conjunto de escala de máquina virtual devem estar usando um SKU *padrão.* A criação de um balanceador de carga ou recurso IP sem a propriedade SKU criará um SKU básico, que não suporta Zonas de disponibilidade. Um balanceador de carga SKU padrão bloqueará todo o tráfego do exterior por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Não é possível fazer uma mudança no local do SKU no IP público e nos recursos do balanceador de carga. Se você está migrando dos recursos existentes que têm um SKU básico, consulte a seção de migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Escala de máquina virtual define regras NAT
As regras naT de entrada do balanceador de carga devem corresponder aos pools NAT do conjunto de escala da máquina virtual. Cada conjunto de escala de máquina virtual deve ter um pool NAT de entrada exclusivo.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regras de saída do Balanceador de carga SKU padrão
O Standard Load Balancer e o Standard Public IP introduzem novas habilidades e comportamentos diferentes para a conectividade de saída quando comparados com o uso de SKUs básicos. Se quiser ter conectividade de saída ao trabalhar com SKUs Standard, você precisará defini-la explicitamente com endereços IP públicos Standard ou com o Load Balancer Standard público. Para obter mais informações, consulte [conexões outbound](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> O modelo padrão faz referência a um NSG que permite que todos os tráfegos de saída por padrão. O tráfego de entrada é limitado às portas necessárias para as operações de gerenciamento de malha de serviço. As regras do NSG podem ser modificadas para atender às suas necessidades.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Habilitando zonas em um conjunto de escala de máquina virtual
Para habilitar uma região, em um conjunto de escala de máquina virtual, você deve incluir os três valores a seguir no recurso de conjunto de escala de máquina virtual.

* O primeiro valor é a propriedade **regiões,** que especifica para qual zona de disponibilidade o conjunto de escala de máquina virtual será implantado.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como verdadeira.
* O terceiro valor é a propriedade "faultDomainOverride" na extensão do conjunto de conjuntos de máquinas virtuais Service Fabric. O valor desta propriedade deve incluir a região e a região em que este conjunto de escala de máquina virtual será colocado. Exemplo: "faultDomainOverride": "eastus/az1" Todos os recursos de conjunto de escala de máquina virtual devem ser colocados na mesma região porque os clusters de malha de serviço do Azure não têm suporte a regiões cruzadas.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Habilitando vários tipos de nó primário saem do recurso Service Fabric Cluster
Para definir um ou mais tipos de nó como primários em um recurso de cluster, defina a propriedade "isPrimary" como "true". Ao implantar um cluster de malha de serviço em Zonas de disponibilidade, você deve ter três tipos de nó em zonas distintas.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para usar zonas de disponibilidade a partir de um cluster usando um Balanceador de carga SKU básico e um IP SKU básico
Para migrar um cluster, que estava usando um Balancer de carga e IP com um SKU básico, você deve primeiro criar um recurso IP e Balancer de carga totalmente novo usando o SKU padrão. Não é possível atualizar esses recursos no local.

Os novos LB e IP devem ser referenciados nos novos tipos de nó de zona de disponibilidade cruzada que você gostaria de usar. No exemplo acima, três novos recursos de conjunto de escala de máquina virtual foram adicionados nas zonas 1,2 e 3. Esses conjuntos de escala de máquina virtual fazem referência ao LB e IP recém-criados e são marcados como tipos de nó primário no recurso de cluster de malha de malha de serviço.

Para começar, você precisará adicionar os novos recursos ao modelo de Gerenciador de recursos existente. Esses recursos incluem:
* Um recurso IP público usando o Padrão SKU.
* Um recurso do Balanceador de carga usando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de escala de máquina virtual.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de escala de máquina virtual deve ter pelo menos cinco nodes (Durabilidade de Prata).

Um exemplo desses recursos pode ser encontrado no [modelo de amostra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Uma vez que os recursos tenham terminado de ser implantados, você pode começar a desativar os nós no tipo de nó primário do cluster original. À medida que os nós forem desativados, os serviços do sistema migrarão para o novo tipo de nó primário que havia sido implantado na etapa acima.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Uma vez que os nós estejam todos desativados, os serviços do sistema serão executados no tipo de nó primário, que está espalhado por regiões. Em seguida, você pode remover os nódulos desativados do cluster. Uma vez que os nós são removidos, você pode remover os recursos originais de conjunto de escala de IP, Load Balancer e virtual machine.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Em seguida, você deve remover as referências a esses recursos do modelo Gerenciador de recursos implantado.

A etapa final envolverá a atualização do nome DNS e do IP público.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
