---
title: Tipos de nó e conjuntos de dimensionamento de máquinas virtuais
description: Saiba como os tipos de nó do Azure Service Fabric estão relacionados aos conjuntos de dimensionamento de máquinas virtuais e como se conectar remotamente a uma instância do conjunto de dimensionamento ou nó de cluster.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: e751b3dd9108d364c900bbd059dc89c1eb3770c4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722332"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual

[Conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets) são um recurso de computação do Azure. Você pode usar os conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que você define em um cluster do Azure Service Fabric configura uma escala separada. O tempo de execução do Service Fabric é instalado em cada máquina virtual no conjunto de dimensionamento pela extensão da máquina virtual *Microsoft. Azure. perfabric* . Cada tipo de nó pode ser escalado vertical ou horizontalmente de forma independente, ter a SKU de sistema operacional em execução em cada nó de cluster, ter conjuntos diferentes de portas abertas e usar métricas de capacidade diferentes.

A figura a seguir mostra um cluster que tem dois tipos de nó, chamados *frontend* e *back-end*. Cada tipo de nó tem cinco nós.

![Um cluster com dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjuntos de dimensionamento de máquinas virtuais para nós

Conforme mostrado na figura anterior, as instâncias do conjunto de dimensionamento começam na instância 0 e, em seguida, aumentam em 1. A numeração é refletida nos nomes do nó. Por exemplo, o nó BackEnd_0 é a instância 0 do conjunto de dimensionamento de BackEnd. Esse conjunto de dimensionamento específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de dimensionamento verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de dimensionamento geralmente será o nome do conjunto de dimensionamento mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nó e conjuntos de dimensionamento

Se você tiver implantado o cluster no portal do Azure ou usado o modelo do exemplo do Azure Resource Manager, todos os recursos em um grupo de recursos serão listados. Você pode ver os balanceadores de carga para cada conjunto de dimensionamento ou tipo de nó. O nome do balanceador de carga usa o seguinte formato: **LB-&lt;nome do tipo de nó&gt;** . Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão de máquina virtual Service Fabric

Service Fabric extensão de máquina virtual é usada para inicializar Service Fabric a máquinas virtuais do Azure e configurar a segurança do nó.

Veja a seguir um trecho de Service Fabric extensão da máquina virtual:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

A seguir estão as descrições de propriedade:

| **Nome** | **Valores permitidos** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
| {1&gt;name&lt;1} | string | nome exclusivo para a extensão |
| type | "ServiceFabricLinuxNode" ou "ServiceFabricWindowsNode" | Identifica o sistema operacional Service Fabric está carregando para |
| autoUpgradeMinorVersion | true ou false | Habilitar a atualização automática de versões secundárias do Runtime da it |
| publisher | Microsoft.Azure.ServiceFabric | Nome do editor de extensão de Service Fabric |
| clusterEndpont | string | URI: porta para ponto de extremidade de gerenciamento |
| nodeTypeRef | string | nome do nodeType |
| durabilityLevel | bronze, silver, gold, platinum | tempo permitido para pausar a infraestrutura imutável do Azure |
| enableParallelJobs | true ou false | Habilitar computação ParallelJobs como remover VM e reinicializar VM no mesmo conjunto de dimensionamento em paralelo |
| nicPrefixOverride | string | Prefixo de sub-rede como "10.0.0.0/24" |
| commonNames | string[] | Nomes comuns de certificados de cluster instalados |
| x509StoreName | string | Nome do repositório onde o certificado de cluster instalado está localizado |
| typeHandlerVersion | 1.1 | Versão da extensão. 1,0 a versão clássica da extensão é recomendada para atualizar para o 1,1 |
| Caminho | string | Caminho para a unidade usada para salvar o estado de Service Fabric serviços do sistema e dados de aplicativo.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Conectar-se remotamente](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) uma instância específica do conjunto de dimensionamento
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nas VMs de cluster após a implantação
* [Alterar o nome de usuário administrador e a senha](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para as VMs do cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
